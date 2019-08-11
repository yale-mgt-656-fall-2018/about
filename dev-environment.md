## Development Environments

You will need to create a "development environment"
to complete the homework assignments and the class
project in this class. By "development environment"
I mean a setup on a computer such that you can write
code, run code, and use version control. We'll be
writing Go, HTML, CSS, and JavaScript. We'll
be using Git for version control.

Your development environment can either be
on your
personal computer or you can use a 
[AWS Cloud9](https://aws.amazon.com/cloud9/) environment
provided by the instructor (including the cost 😉).
There are tradeoffs between these. Setting up your
personal computer is ideal. However, it can be 
sometimes be challenging on Windows. (Well...it's not
really challening, its only that the teaching staff
don't have that much experience with windows.)

You are free to use either your computer or Cloud9.
If you'd like a Cloud9 environment, you need only 
tell the instructor. An announcement will be made
in class.

Below, you will find documentation on how to get
started on mac, windows, and Cloud9.

### Mac

1. [Install Go](https://golang.org/doc/install)
* Or use Homebrew:
    * `brew update`
    * `brew install golang`
2. [Setup your workspace](https://www.callicoder.com/golang-installation-setup-gopath-workspace/)
* Go requires you to organize your code in a specific way, and all your code should be in one workspace (directory). Additionally, you should create the subdirectories `src` `bin` and `pkg` in this workspace. You can run the following commands to help setup your workspace: 
    * `echo 'export GOPATH=$HOME/where_you_keep_your_code/go-workspace' >>~/.bash_profile`
    * `source ~/.bash_profile`
    * `mkdir -p $GOPATH $GOPATH/src $GOPATH/pkg $GOPATH/bin`
3. Install git. It is likely git is already installed on your
   mac. Open your terminal and type `git --version`.  If you
   see version information, you've got git. If you see 
   "command not found" or similar, you don't.
   You might also be prompted to install the Mac OSX Command
   Line tools. If you are prompted to do so, you should install
   them, then you'll have git. (See [this guide](https://hackernoon.com/install-git-on-mac-a884f0c9d32c).)
   To install git,
   follow [these instructions](https://www.atlassian.com/git/tutorials/install-git)
   or [these instructions](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
   You might also like the [GitHub git GUI](https://desktop.github.com/),
   but you should use it as a complement instead of replacement
   for the command line.
4. Install a code editor. I like
   [Visual Studio Code](https://code.visualstudio.com/) or
   [Atom](https://atom.io/) but you can use anything.

### Windows

1. [Install Go](https://golang.org/doc/install)
2. [Setup your workspace](https://www.callicoder.com/golang-installation-setup-gopath-workspace/)
* Go requires you to organize your code in a specific way, and all your code should be in one workspace (directory). Additionally, you should create the subdirectories `src` `bin` and `pkg` in this workspace.
3. Install git. Follow
   [these instructions](https://www.atlassian.com/git/tutorials/install-git)
   or [these instructions](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
   TODO: SOMEBODY HELP ME HERE PLEASE. SHOULD THEY INSTALL 
   GITBASH, GITHUB'S DESKTOP CLIENT AND POWERSHELL GIT INTEGRATION,
   OR USE THE LINUX SUBSYSTEM?
4. Install a code editor. I like
   [Visual Studio Code](https://code.visualstudio.com/) or
   [Atom](https://atom.io/) but you can use anything.

### Cloud9

1. Tell the instructor you want to use Cloud9. You will receive a link
   to your environment and your login credentials by email.
2. Update go and fix a small bug. When you first log into the environment,
   you should see a command prompt in one of the window panes. (If you
   don't, click the green plus sign and "New Terminal".) Run the following
   commands to update Go and fix a small bug related to "code completion".

   ```sh
   sudo yum update go
   mkdir -p ~/.c9/gocode
   GOPATH=$HOME/.c9/gocode go get -u github.com/nsf/gocode && ~/.c9/gocode/bin/gocode
   ```
3. Now you're all set. Your Cloud9 environment uses
   [Amazon Linux](https://aws.amazon.com/amazon-linux-ami/). You have
   git already installed and, clearly, Cloud9 has a built-in code editor.
