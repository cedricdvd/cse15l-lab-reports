# Lab Report 1

This report is intended to show incoming CSE 15L students how to:

1. Install Visual Studio Code
2. Connect to a Remote Server
3. Run Commands on a Remote Server

## Installing VSCode

Since I already had Visual Studio Code on my computer, I did not need to
install anything. Incase you still need to install it, you can download the
editor through the [VSCode website][1]. Be sure to install the version suitable
for your Operating System. Once that is done, open up the application. It
should look similar to the image below.

![Example VSCode window][2]

## Remotely Connecting

If you have not previously remotely connected, make sure to reset the password
to your CSE15L account by following [this tutorial][3].

Once that is done, open a new terminal window either through the Terminal
Application or through VSCode's Integrated Terminal. Once there, type in the
following command (`alo` will be replaced with the letters to your
course-specific account):

```shell
$ ssh cs15lwi23alo@ieng6.ucsd.edu
```

This will begin the remote login process. If this is your first time logging
in to the server, the following prompt may come up:

```shell
The authenticity of host 'ieng6.ucsd.edu (128.54.70.227)' can't be established.
RSA key fingerprint is SHA256:ksruYwhnYH+sySHnHAtLUHngrPEyZTDl/1x99wUQcec.
Are you sure you want to continue connecting (yes/no/[fingerprint])? 
```

If that is the case, type `yes`. When the terminal prompts you for your
password, type it in.

If you have successfully logged in, the output should look something like
this:

![remote output][4]

## Trying Some Commands

Now that you are logged in, we can beginning testing out some commands.

The following command will print out the current working directory.

```shell
$ pwd
```

![print working directory][5]

The following command will list files and directories in your current
directory.

```shell
$ ls
```

![list files][6]

By adding `-a` to the previous command, all files and directories (including
those beginning with `.`) will be listed.

```shell
$ ls -a
```

![list of all files and directories][7]

The following commands will change your directory to the `perl5` directory
and list out its path.

```shell
$ cd perl5
$ pwd
```

![change directory to perl5][8]

Although the following command appears to not do anything, it is still working.
The directory does not contain anything to be printed.

```shell
$ ls
```

![list nothing][9]

The first command will change your directory back to the parent directory.
The second command adds `-lat` to the `ls` command, causing it to list all
files and directories in long format and order them in descending order by
time modified.

```shell
$ cd ..
$ ls -lat
```

![change directory and list files][10]

Once you are done using the remote server, you can logout with the following
command

```shell
$ exit
```

![logout][11]

[1]: https://code.visualstudio.com/
[2]: ../images/lab1/vscode-window.png
[3]: https://docs.google.com/document/d/1hs7CyQeh-MdUfM9uv99i8tqfneos6Y8bDU0uhn1wqho/edit?usp=sharing
[4]: ../images/lab1/remote-output.png
[5]: ../images/lab1/pwd.png
[6]: ../images/lab1/ls.png
[7]: ../images/lab1/ls-a.png
[8]: ../images/lab1/cd-perl5.png
[9]: ../images/lab1/perl5-ls.png
[10]: ../images/lab1/cd-ls-lat.png
[11]: ../images/lab1/exit.png
