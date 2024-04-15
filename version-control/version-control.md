# Version Control

## What is Version Control

![version control comic](./assets/version-control-comic.jpg)

- A system that manages and keeps records of changes made to files
- Allows for collaboration
- Associates changes with authors
- Allows you to revert changes and go back to a previous state
- Useful beyond code/programming

![version control diagram](./assets/version-control-vs-traditional.jpg)

## Git

- A Version Control System
- Created by **Linus Torvalds** in 2005
- Replacement for BitKeeper to manage the Linux Kernel
- Command Line Program
- Distributed Version Control
- Cross-platform
- Free and Open Source

### Distributed

All _contributors_ have a copy of the repo, with all files and the full history.

Contributors sync their repos **through** the use of a central remote repo, e.g. **GitHub**.

Hosted remotes like GitHub also provide access to the repo through a web browser.

### Interface

- Command Line Interface by default
- Many available GUIs. (**Github Desktop**, **GitKraken**, Internal IDE tooling)
- Web UIs by remote repo providers like **GitHub**

![git interfaces](./assets/git-interfaces.jpg)

### How Git Works

![how git works comic](./assets/xkcd-1597.png)

- Git tracks your files using **“snapshots”**, records
  of the files in your project at a given point
  in time
- You decide when to take a snapshot and of what files, this
  is known as a **commit**
  - Can be used as noun or verb
  - _“I commited code”_ and _“I just made a new commit”_
- Have the ability to go back and visit any commit
- A project is made up of a bunch of commits

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

### Repository

- A project, or group of files that you want to track together
- Often referred to as a **repo**, a collection of files and
  the history of those files (i.e. commits)
- Can live on a local machine or on a remote server (e.g. GitHub)
- The act of copying a repo from a remote server is called **cloning**
- Cloning from a remote server allows teams to work together
- The process of downloading commits that don’t exist on your
  machine from a remote server is called **pulling** changes
- The process of adding your local changes to a remote repo
  is called **pushing** changes

![git push and pull part 1](./assets/push-and-pull-1.jpg)

![git push and pull part 2](./assets/push-and-pull-2.jpg)

![git push and pull part 3](./assets/push-and-pull-3.jpg)

### Commits

Commits contain 3 pieces of information:

1. Commit Message - Information about the changes from the previous commit
2. A reference to the previous commit, AKA the parent commit
3. An SHA-1 hash code (e.g. `fb2d2ec5069fc6776c80b3ad6b7cbde3cade4e`)

#### Sha-what?

- Hashing is the process of transforming any given key or a string of characters into another value. Typically a shorter, fixed-length value
- SHA-1 is a hashing algorithm. It takes some data as input and generates a **unique** 40 character string from it.

#### Atomic Commits

- Single Responsibility Principle: A unit of code should do only one thing
- Atomic commits are the smallest possible change that is meaningful on its own
- Ideally can be described in one simple sentence
- Why?
  - An atomic change is a reversible change
  - Clean git history
  - Easier to review and understand changes

#### Making a commit

- Make changes to files

```bash
echo 'hello world!' > example.txt
```

- Add the changed files to the staging area

```bash
git add example.txt
# OR
git add .
```

- Add a commit with a commit message

```bash
git commit -m 'Add `example.txt` file
```

- (Optional) Push the changes to remote

```bash
git push origin main
```

#### Commit Messages

![git commit messages comic](./assets/xkcd-1296.png)

[https://cbea.ms/git-commit/](https://cbea.ms/git-commit/)

2. Separate subject from body with a blank line
3. Limit the subject line to 50 characters
4. Capitalize the subject line
5. Do not end the subject line with a period
6. Use the imperative mood in the subject line
   - If applied, this commit will "your commit message"
7. Wrap the body at 72 characters
8. Use the body to explain what and why vs. how

> In addition to the general commit guidelines, organizations and projects can have their own preferences and conventions for commit messages

### Branches

![branches](./assets/branches.jpg)

- A group of commits
- All commits live on a branch
- There can be many branches, but each repo must have one default branch
- Each branch is a separate different version of the repo
- The default branch is called `master` or `main`
- One branch can be **merged** into another

### Overview

![git overview 1](assets/git-breakdown-1.jpg)

```bash
echo 'hello world!' > example.txt
```

![git overview 2](assets/git-breakdown-2.jpg)

```bash
git add example.txt
```

![git overview 3](assets/git-breakdown-3.jpg)

```bash
git commit -m 'Add example.txt'
```

![git overview 4](assets/git-breakdown-4.jpg)

```bash
git push origin dev
```

![git overview 5](assets/git-breakdown-5.jpg)

```bash
git reset HEAD~3
```

![git overview 6](assets/git-breakdown-6.jpg)

```bash
git reset --hard origin/dev
```

![git overview 7](assets/git-breakdown-7.jpg)

### Further Reading

- [https://www.rithmschool.com/courses/git](https://www.rithmschool.com/courses/git)
- [https://www.codecademy.com/learn/learn-git](https://www.codecademy.com/learn/learn-git)
- [https://missing.csail.mit.edu/2020/version-control](https://missing.csail.mit.edu/2020/version-control)
- [https://ohshitgit.com](https://ohshitgit.com)
- [https://jvns.ca/blog/2024/01/26/inside-git](https://jvns.ca/blog/2024/01/26/inside-git)
- [https://jvns.ca/blog/2024/03/08/how-head-works-in-git](https://jvns.ca/blog/2024/03/08/how-head-works-in-git)

## GitHub

### Pull Requests

- A proposal to merge a set of changes from one branch into another
- Includes a title and description
- You can discuss and review the potential changes with collaborators
- Follow-up commits are added in response to feedback

### README.md

- All the information about the project included in this document
- Uses markdown syntax to make your file clear and easy to read
- Can include information about getting started or deployment
- Can include information about code structure and standards
- Can include functional breakdown and even documentation

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
