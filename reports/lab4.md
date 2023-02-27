# Lab Report 4

This report is intended to show how to quickly use the command line interface
to complete the tasks listed below.

## Task List

1. Log into `ieng6`
2. Clone fork of `lab7` repository from Github account
3. Run the tests, demonstrating that they fail
4. Edit the code to fix the failing tests
5. Run the tests, demonstrating that they now succeed
6. Commit and push the resulting change to your Github account

## Log into `ieng6`

Keys pressed: `ctrl-r ssh <enter>`

Since I had previously used `ssh` before in other labs, I was able to search
through my command history with `ctrl-r ssh`. Doing so prompted the following
command:

```shell
ssh cs15lwi23alo@ieng6.ucsd.edu
```

Afterwards, I pressed `enter` to SSH into the `ieng6` machine. I did not need
to enter my password since I had created an SSH key during lab.

![ssh login][0]

## Clone Fork of `lab7` Repository

Keys pressed: `<command-c>`, `git clone <command-v>;cd lab7<enter>`

`<command-c>` allowed me to clone the repository using SSH instead of HTTPS.
Since I am used to using `git` in the CLI, typing out `git clone` was very
quick for me.

`;` then allows me to use another command in the same line.
In this case, I am able to chain `cd lab7` to immediately execute as soon as
the repository is done cloning. Together, these two commands resulted in the
following output:

![clone and change to fork][1]

## Run Failed Tests

Keys pressed: `<ctrl-r>javac<enter>`,
`<up><ctrl-a><alt+right><delete>`,
`<ctrl-e><ctrl-w>org.junit.runner.JUnitCore L<tab>T<tab><delete><enter>`

Since I tend to use `javac` a lot when running JUnit tests during lab, doing
`<ctrl-r>` allow me to search my command history. For this lab, the following
command was the first one to pop up.

```shell
javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
```

Pressing `<enter>` allowed me to compile all the `.java` files in the
repository.

Since the class path is the same for `javac` and `java`, pressing `<up>`
allowed me to access `javac`. Since I want to run `java` instead of `javac`,
`<ctrl-a>` allowed me to go back to the beginning of the command. Afterwards,
`<alt+right>` placed my cursor after `javac` so that `<delete>` could remove
`c`.

After this sequence of keystrokes, this was my command:

```shell
javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
```

Since I need to run JUnit on `ListExamplesTests`, I pressed `<ctrl-e>` to move
my cursor to the end of the command and `<ctrl-w>` to delete `*.java`.
After that, I typed out `org.junit.runner.JunitCore` which will help execute
the JUnit tests.

Since I wanted to run `ListExamplesTests`, I pressed `L<tab>`
to autocomplete `ListExamples`. Note that `<tab>` did not autocomplete to
`ListExamplesTests` since there were 4 files beginning with `L`:
`ListExamples.java`, `ListExamples.class`, `ListExamplesTests.java`, and
`ListExamplesTests.class`. In order to get `ListExamplesTests`, I pressed
`T<tab>` to autocomplete to `ListExamplesTests.` (the `.` is because there is
`.java` and `.class`). Pressing `<delete>` would remove the trailing `.`,
and `<enter>` would allow me to run the command.

Together, these two commands result in the following output:

![run failed tests][2]

Based on the test, we can see that our test timed out. This means that
something in `ListExamples` must be causing an infinite loop. Furthermore,
the issue seems to be somewhere around line 42 as indicated by
`at ListExamples.merge(ListExamples.java:42`).

## Edit Code

Keys Pressed:
`vim L<tab>.<tab><enter>`,
`43<shift+g>lllllli<delete>2<escape>`
`<shift+;>wq<enter>`

If we want to read and edit the contents of the file, we will need to use
a command-line text editor such as `vim` or `nano`. Since I am more comfortable
with vim, I decided to type `vim`. `L<tab>.<tab><enter>` allowed me to
autocomplete `ListExamples.java` as the file I want to edit and gave me
the following command:

```shell
vim ListExamples.java
```

Executing this command gave the following result:

![list examples error][3]

From analyzing the code, we can see that line 43 (the last `index1 += 1`)
results in an infinite loop. This is because the while loop is meant to add
any remaining elements of `list2` using `index2`. However, `index2` is never
getting updated.

To fix this, we can type `43<shift+g>` to have `vim` move our cursor to
line 43. Afterwards, we can press `l` six times to move our cursor to the end
of `index1`. We can then press `i` to place vim in insert mode.
Pressing `<delete>2<escape>` to changes the `1` into s `2` and returns us to
normal mode.

Our resulting code should now look like this:

![list examples fixed][4]

We can then press `<shift+;>wq<enter>` to save our changes and exit out of
the file.

## Run Passing Tests

Keys pressed: `<up><up><up><enter>`, `<up><up><up><enter>`

Since we are rerunning the same tests, we need to recompile all the files
using `javac` and rerun the tests with `java`. We can access the same commands
by pressing `<up>` three times, and then `<enter>` to run the command. Doing
this twice allows us to reuse the `javac` and `java` commands from earlier.
This should provide us with the following output:

![junit pass][5]

## Commit and Push Changes

Keys pressed: `git add L<tab>.<tab>;git commit -m "Update";git push<enter>`

In order to add our changes, we need to type `git add`. Since we only made
changes to `ListExamples.java`, we can type `L<tab>.<tab>` to autocomplete
the file.

Therefore, our first command should look like:

```shell
git add ListExamples.java
```

Afterwards, we can type `;` to execute `git commit` and save our changes to the
branch, which we will need to type out character by character. `-m` allows us
explain the changes we made without entering `vim` or `nano`. While normally
we would want to write a commit message explaining our changes, we can leave it
as "Update" for this activity.  Now our command should look like this:

```shell
git add ListExamples.java;git commit -m "Update"
```

Finally, we can push our changes from our local repository to the remote
repository using `git push`. In order to chain this with our other commands,
we will use `;` again. Our command should now look like this:

```shell
git add ListExamples.java;git commit -m "Update";git push
```

We can then press `<enter>` to execute the command, resulting in the following
output:

![commit and push changes][6]

Furthermore, we can check that our results were pushed to GitHub.

![github results][7]

[0]: ../images/lab4/ssh-login.png
[1]: ../images/lab4/git-clone.png
[2]: ../images/lab4/junit-fail.png
[3]: ../images/lab4/vim-initial.png
[4]: ../images/lab4/vim-edit.png
[5]: ../images/lab4/junit-pass.png
[6]: ../images/lab4/git-commit.png
[7]: ../images/lab4/github-results.png
