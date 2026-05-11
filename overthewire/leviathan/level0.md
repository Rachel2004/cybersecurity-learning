# Leviathan Level 0 → 1

## Goal
Locate and retrieve the password for the next level.

## Enumeration

Commands used:

```bash
ls -la
cd .backup
ls -la
```

---

## Key Findings

A hidden directory named `.backup` contained a file called:

```text
bookmarks.html
```

This suggested there may be useful information stored inside the file.

---

## Investigation

I searched the file contents for the word `password` using:

```bash
cat bookmarks.html | grep -i password
```

This revealed a line containing credentials for the next level.

---

## Exploitation

The password for the next level was successfully recovered from the HTML file.

---

## Lessons Learned

- Importance of checking hidden directories and files
- Basic Linux enumeration techniques
- Using `grep` to quickly search large files
- Sensitive information can sometimes be exposed in unexpected locations
