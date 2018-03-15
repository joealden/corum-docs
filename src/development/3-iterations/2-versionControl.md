# Version Control

Corum uses a version control system to manage and store the project.
Specifically, Corum uses Git and has the repository uploaded to Github. The way
that version control systems (git in particular) works is when a change is made
to the codebase, the developer makes something called a 'commit'. This contains
a diff of the codebase with the state before and the state after the change.
This diff is sent along with a message describing the change.

## Why Version Control?

Version control systems offers a lot of benefits, although a lot of these
benefits are only helpful when working on a team. As Corum is developed by me
alone, Corum cannot take advantages of these features.

There are three main benefits of using a version control system in the context
of Corum's development. The following sub sections will explain what these are:

### Backup in Case of File Loss

While not required, it is useful to create a remote version of the repository (A
version of the repo that is not stored locally). Not only does this mean that I
can easily work on Corum across devices (I have worked on this project on both
my desktop and my laptop), it means that it is almost impossible to lose any
files. This is because in my case, the codebase is stored on three separate
machines at all times (My laptop, desktop, and on Github's servers). For
example, if I accidentally delete the folder, the files corrupt, or my device
breaks, the codebase will still be intact.

### Backup in Case of a Coding Mistake

Due to how version control systems work by storing each change, it means that if
I make a serious mistake and want to get back to the previous working state, I
just have to run a single command. It also means that I can revert to any state
in the history of the repo. If I was not using a version control system this
would be difficult or sometimes impossible, as the 'undo' button only stores a
limited amount of state.

### Documentation

Due to the fact that with every change (commit) made to the repo is accompanied
by a message, it means that anyone can look at the history of the repo and see
when and why a change was made. This is extremely useful; for example, if I find
a bug I can easily look at the history of that file and determine when this
change was made. This allows me to more easily find more potential bugs that
might stem from the same commit.

Also, for this project, this is useful because it allows me to examine the
development process of Corum and more easily comment on it for this report.
Also, if you wanted to, you could see an extremely detailed timeline of the
development process by visiting the projects Github repo.
([https://github.com/joealden/corum-client](https://github.com/joealden/corum-client))
