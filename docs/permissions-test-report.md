# CloudByte Permissions Test Report: Section 2

Date: 24.11.2026

| User  | Action                                              | Expected | Observed |
|-------|-----------------------------------------------------|----------|----------|
| alice | touch in /shared/engineering                        | allow    | allow; file tagged group engineering (setgid) |
| bob   | touch in /shared/engineering                        | allow    | allow; file tagged group engineering (setgid) |
| -     | find /shared/engineering -type f -group engineering | all files| all files listed (setgid propagated) |
| frank | ls /shared/dropbox                                  | deny     | deny     |
| frank | echo > /shared/dropbox/issue123.txt                 | allow    | allow    |
| frank | cat /shared/dropbox/urgent_help.txt (known path)    | allow    | allow; caveat: dropbox blocks listing, not reading by known path |
| frank | rm /shared/dropbox/urgent_help.txt                  | deny     | deny (sticky bit) |
| kate  | ls /shared/dropbox                                  | allow    | allow    |
| kate  | cat /shared/dropbox/issue123.txt                    | allow    | allow    |

## Notes

- Setgid (mode `2770`) makes "which files does the engineering team own?" an
  answerable question. Without it, ownership tracks the creator's primary group
  and the team-level view falls apart.
- The sticky bit (mode `1773`) protects submissions from cross-user deletion.
- Caveat: the dropbox blocks browsing other people's submissions, not reading
  one whose filename you already know. Standard Unix permissions can't close
  that gap on their own; tools beyond the scope of this project would.