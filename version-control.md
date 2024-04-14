# Version Control

## What is Version Control

- A system that manages and keeps records of changes made to files
- Allows for collaboration
- Associates changes with authors
- Allows you to revert changes and go back to a previous state
- Useful beyond code/programming

## Git

- A Version Control System
- Created by Linus Torvalds in 2005
- Replacement for BitKeeper to manage the Linux Kernel
- Command Line Program
- Distributed Version Control
- Cross-platform
- Free and Open Source

### Distributed

All _contributors_ have a copy of the repo, with all files and the full history.

Contributors sync their repos through the use of a central remote repo, e.g. **GitHub**.

Hosted remotes like GitHub also provide access to the repo through a web browser.

### Interface

- Command Line Interface by default
- Many available GUIs. (**Github Desktop**, **GitKraken**, Internal IDE tooling)
- Web UIs by remote repo providers like **GitHub**

### Terminology

- **Repository** - A project or code-base. AKA **repo**
- **Working Tree** - Any directory that has a repo associated with it
- **Commit** - A snapshot of the repo at a point in time. Identified by a hash
- **The Index** - AKA **Staging Area**. Tracks changes to be committed
- **Branch** - A group of commits with a specific name. A version of the repo. A repo must have a default branch (default names: `main` or `master`).
- **Tag** - A label for a commit. Used with releases.
- **HEAD** - Points to the currently _checked out_ code
  - If a branch: HEAD points to the branch name
  - If a specific commit: Detached Head. points to the hash of the commit.

### How Git Works

### Branches

### Commits

- Atomic Commits

### Commit Messages

- General Conventions
- Org-Level Conventions

### Pull Requests

### Further Reading

- [https://www.rithmschool.com/courses/git](https://www.rithmschool.com/courses/git)
- [https://www.codecademy.com/learn/learn-git](https://www.codecademy.com/learn/learn-git)
- [https://missing.csail.mit.edu/2020/version-control](https://missing.csail.mit.edu/2020/version-control)
- [https://ohshitgit.com](https://ohshitgit.com)
- [https://jvns.ca/blog/2024/01/26/inside-git](https://jvns.ca/blog/2024/01/26/inside-git)
- [https://jvns.ca/blog/2024/03/08/how-head-works-in-git](https://jvns.ca/blog/2024/03/08/how-head-works-in-git)

# GitHub

- Organization Roles and Permissions
- Accepting Invites
- SSH Keys
- Commit Signing
- GH CLI
- Actions
- Issues
- Releases

# Misc

- Two-Factor Authentication (2FA)
- Password Managers
