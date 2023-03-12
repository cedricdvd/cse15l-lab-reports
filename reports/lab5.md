# Lab Report 5

This report is intended to show the step by step process in creating a bash
script that can grade a student's programming assignment submission.

## Purpose

This shell script is intended to test a student-submitted `ListExamples` file
and print the score similar to the manner below:

```bash
$ bash grade.sh https://github.com/some-username/some-repo-name
...
message about submission
...
```

The test file for `ListExamples` can be found [in this repository][0]. Links
to the example submissions can be found on the [CSE 15L website][1].

## Tasks

In order to implement the auto-grader, we need to accomplish the following
tasks:

1. Clone student's repository into current working directory.
2. Verify that `ListExamples.java` exists within the folder.
3. Verify that `filter()` and `merge()` are implemented in `ListExamples`.
4. Verify that `ListExamples` compiles without errors.
5. Run `ListExamples` on our tests.
6. Return the student's score.

## Cloning Student Repository

In order to clone the repository, we will need to use `git clone` along with
a link to the repository. First, we will need to verify that the student
provided a link to the repository as an argument.

```shell
if [[ $# -eq 0 ]]
then
    echo 'Missing repository argument'
    exit 1
fi
```

`$#` tells us the number of input arguments used in the script (excluding
`grade.sh`). Therefore, if the user does not include an argument
(or `$#` is equal to 0), the script will exit immediately.

```shell
$ bash grade.sh
Missing repository argument
```

Afterwards, we can attempt to clone the repository as follows:

```bash
git clone $1 student-submission
if [[ $? -eq 0 ]]
then
    echo 'Finished cloning'
else
    exit 1
fi
```

`$?` will help us determine the error code (or in most cases if the command
executed successfully). If we provided a valid repository url, then `$?` will
be equal to 0 and `echo 'Finished cloning'` will run.

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3
Cloning into 'student-submission'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
Finished cloning
...
```

Otherwise, `git clone` will throw an error message meaning that `$?` equals 1
and the script will exit with error code 1.

```bash
$ bash grade.sh https://github.com/real-user/fake-repo
Cloning into 'student-submission'...
remote: Repository not found.
fatal: repository 'https://github.com/real-user/fake-repo/' not found
```

## Check File Exists

In order to check if the student's submission contains `ListExamples.java`, we
will need to use an if-statement along with the `-f` option as so:

```bash
if [[ -f student-submission/ListExamples.java ]]
then
    echo 'ListExamples.java found'
    cp student-submission/ListExamples.java ./
else
    echo 'Missing file ListExamples.java'
    exit 1
fi
```

In bash, `-f` will return true if the path following it exists and it is a
file. If `student-submissions/ListExamples.java` does exist, then the script
look as follows:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3
...
ListExamples.java found
...
```

Afterwards, it will copy `ListExamples.java` into the current directory so that
it can be compiled with the tester.

The else statement is used to handle cases where either the file is under a
different name or is nested within another directory. For either of those
cases, the script will echo a message informing the student that
`ListExamples.java` is missing and exit with code 1 as follows:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-filename
...
Missing file ListExamples.java
```

Note: There is a way to search for a file within nested directories that
involves using `-e` and `**`. For the sake of brevity, this case will not be
considered.

## Check Method Signatures

In order to determine if the class was implemented correctly, we need to check
if the method signatures are correctly formatted. Since different students may
have written these methods on different lines, we can use `grep` to match
within the file for our expected method signatures as follows:

```bash
grep -q 'static List<String> filter(List<String> list, StringChecker sc)' ListExamples.java
if [[ $? -eq 0 ]]
then
    echo 'filter method header found'
else
    echo 'filter method header missing'
    exit 1
fi

grep -q 'static List<String> merge(List<String> list1, List<String> list2)' ListExamples.java
if [[ $? -eq 0 ]]
then
    echo 'merge method header found'
    mkdir out out/logs
else
    echo 'merge method header missing'
    exit 1
fi
```

We can use the fact that `grep` returns with an exit status of `1` if no lines
were selected to determine whether or not the method signatures exist. Since
bash stores the most recent exit status in `$?`, we can check if it equals
`0` to determine whether the method signature exists like so:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3
...
filter method header found
merge method header found
...
```

Otherwise, the user would know which method header is missing and the script
would terminate with exit status `1`.

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-signature
...
filter method header missing
```

Notice that when we determine that `merge()` is correctly formatted in the
file, we run `mkdir out out/logs`. What this does is create a directory `out`
and `logs` (which is inside `out`). This will allow us to redirect output of
later commands and make clean up much easier when we rerun the script.

## Compile Student Submission

In order to compile the student submission, we can run the `javac` command
while providing it with the necessary arguments as so:

```bash
javac -d out -cp $CPATH *.java > out/logs/compile.log 2>&1
if [[ $? -eq 0 ]]
then
    echo 'Compile successful'
else
    echo 'Could not compile ListExamples.java'
    exit 1
fi
```

Since we need to run a JUnit test, we will need to have both
`hamcrest-core-1.3.jar` and `junit-4.13.2.jar` in our class path. Since we are
going to use the same classpath later, we can store it in a variable, `CPATH`
like so:

```bash
CPATH='.:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar'
```

Now we can reference the variable later on when we run the class without
having to retype the entire line.

The output is then redirected to `compile.log`. Afterwards, we once again check
`$?` (the exit status) of the command. If it is `0`, then our code compiled
successfully and the user will know as so:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3
...
Compile successful
...
```

Otherwise, the script will terminate with exit status `1` and inform the user
the code could not compile correctly like so:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-compile-error
...
Could not compile ListExamples.java
```

Note that since bash considers a failed compile an error, it will be outputted
to `stderr`. Therefore, we cannot just redirect the output using
`> out/logs/compile.log` since that only redirects `stdout`. We will also have
to use `2>&1` to redirect `stderr` and `stdout` that way it can redirected.

Although `compile.log` is not currently used, the code could be adapted to
provide users hints on how to fix their code by using `grep`.

Note that `-d` is used here to keep `*.class` in a separate directory. While
this is not necessary, it helps keep the working directory organized.

## Run Tests on Student Submission and Return Score

In order to run the code, we can use `java` with a modified version of the
`CPATH` variable since our class files are in the `out` directory.

```bash
java -cp "${CPATH}:out" org.junit.runner.JUnitCore TestListExamples > out/logs/test.log 2>&1
if [[ $? -eq  0 ]]
then
    TESTS=1
    FAILS=0
else
    RESULTS=`grep 'Tests run:' out/logs/test.log`
    TESTS=${RESULTS:11:1}
    FAILS=${RESULTS:25:1}
fi
```

Since `CPATH` does not provide a link to `out`, we will need use the variable
in a way that allows `:out` to be concatenated. This can be done using curly
brackets to protect the variable.

```bash
$ echo "${CPATH}:out"
.:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar:out
```

The output of our tests along with the errors will be redirected to `test.log`
should the submission fail.

If `$?` is `0`, then the student's submission passed all our tests and we can
set `FAILS` to 0.

If any test fails, then `$?` will not equal `0` and we will need to parse our
results to determine how many tests they failed. `grep` is used to find the
output line that contains how many tests were ran and how many tests failed.
Once it is stored in a variable, we can then find the character that contains
the necessary information. This can be done using [substring expansion][2]
if we know the character length and offset.

Afterwards, we can provide the user with their score.

```bash
SCORE=$((($TESTS - $FAILS) * 100 / $TESTS))
echo "Score: $SCORE / 100"
```

Since bash does not provide floating point arithmetic, we cannot divide `FAILS`
by `TESTS` and subtract that from `1` to calculate the final score. (Although
in this case, it would be fine since we only run 1 test). Instead, we have to
multiply the score by `100` to determine the whole number portion of the
percentage correct. If you want more precision, you could multiply it by a
bigger power of 10 and then possibly use substring expansion on the output.

If the user provides a correct implementation, then the output will look
as follows:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-corrected
...
Score: 100 / 100
```

Otherwise, the output will look something like this:

```bash
$ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3
...
Score: 0 / 100
```

Although our current bash script uses `test.log`, the file can be used further
to show which tests passed and which tests failed by using `grep`. This would
allow the student to see which tests their implementation fails.

## Reusing Script

In order to use the script again, we will want to delete `student-submission`,
`ListExamples.java`, and `out`.

```bash
rm -rf student-submission
rm -rf out
rm -f ListExamples.java
```

`-r` is used to remove the directory along with any files within the directory.
`-f` is used to remove the files without prompting for any confirmation.
Furthermore, it suppresses any messages `rm` might produce (such as if the
file does not exist).

## Script Examples

Now we will look at how our script operates on some example repositories.

- <https://github.com/ucsd-cse15l-f22/list-methods-lab3>

  This repository contains an incorrect implementation of merge (does not add
  remaining items in `list2` correctly). Therefore, we expect it to get a
  0 / 100 and exit with status 0.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  ListExamples.java found
  filter method header found
  merge method header found
  Compile successful
  Score: 0 / 100
  $ echo $?
  0
  ```

- <https://github.com/ucsd-cse15l-f22/list-methods-corrected>

  This repository contains a correct implementation of `ListExamples.java`.
  Therefore, we expect it to return a full score and exit with status 0.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-corrected
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  ListExamples.java found
  filter method header found
  merge method header found
  Compile successful
  Score: 100 / 100
  $ echo $?
  0
  ```

- <https://github.com/ucsd-cse15l-f22/list-methods-compile-error>

  This repository contains an implementation of `ListExamples.java` that
  results in a compiler error. Therefore, our script should let the user know
  that the submission does not compile and not provide a score and exit with
  status 1.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-compile-error
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  ListExamples.java found
  filter method header found
  merge method header found
  Could not compile ListExamples.java
  $ echo $?
  1
  ```

- <https://github.com/ucsd-cse15l-f22/list-methods-signature>

  This repository contains an implementation of `ListExamples.java` that swaps
  the argument order for `filter()`. Therefore, terminate early and let the
  user know it could not find the method header for filter.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-signature
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  ListExamples.java found
  filter method header missing
  $ echo $?
  1
  ```

- <https://github.com/ucsd-cse15l-f22/list-methods-filename>

  This repository contains an implementation of `ListExamples.java` that is
  under a different file name (`ListMethods.java`). Therefore, our script
  should exit with code 1 after it tries to find to the file.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-filename
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  Missing file ListExamples.java
  $ echo $?
  1
  ```

- <https://github.com/ucsd-cse15l-f22/list-methods-nested>

  This repository contains an implementation of `ListExamples.java` that is
  nested within another directory (`pa1`). Since our script only searches for
  `ListExamples.java` at the root of the repository, our code will terminate
  early with exit code 1 and inform the user it could not find the file.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-methods-nested
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  Missing file ListExamples.java
  $ echo $?
  1
  ```

- <https://github.com/ucsd-cse15l-f22/list-examples-subtle>

  This repository contains an incorrect implementation of `merge()` (it
  does have correct behavior for duplicates). Therefore, our script will
  inform the user that they have a score of `0` and exit with code 0.

  ```bash
  $ bash grade.sh https://github.com/ucsd-cse15l-f22/list-examples-subtle
  Cloning into 'student-submission'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Compressing objects: 100% (2/2), done.
  remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
  Finished cloning
  ListExamples.java found
  filter method header found
  merge method header found
  Compile successful
  Score: 0 / 100
  $ echo $?
  0
  ```

[0]: https://github.com/ucsd-cse15l-w23/list-examples-grader
[1]: https://ucsd-cse15l-w23.github.io/week/week6/#student-submissions
[2]: https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html
