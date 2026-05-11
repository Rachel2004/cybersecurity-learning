# Leviathan Level 1 → 2

## Goal
Analyze the SUID binary `check` and gain access to the next level.

## Enumeration

Commands used:

```bash
ls -la
file check
strings check
```

## Key Findings

The binary was identified as a SUID executable:

```text
setuid ELF 32-bit executable
```

Using `strings`, several interesting values appeared:

```text
strcmp
/bin/sh
password:
sex
```

This suggested:
- the program compares user input with a hardcoded value
- successful authentication may spawn a shell

## Investigation

To observe the program behavior dynamically, I used:

```bash
ltrace ./check
```

After entering input, the trace revealed:

```text
strcmp("sex", "sex")
system("/bin/sh")
```

This confirmed:
- the password comparison used `strcmp`
- the binary executed `/bin/sh` after successful authentication

## Exploitation

After entering the correct password, a shell was spawned.

To preserve the elevated privileges from the SUID binary, I used:

```bash
/bin/bash -p
```

This provided access as the next user level.

The password for the next level was then retrieved from:

```bash
cat /etc/leviathan_pass/leviathan2
```

## Lessons Learned

- Introduction to SUID binaries
- Difference between static and dynamic analysis
- Using `strings` to inspect binaries
- Using `ltrace` to monitor library calls
- Understanding how privilege inheritance works
- Importance of `bash -p` when interacting with SUID shells