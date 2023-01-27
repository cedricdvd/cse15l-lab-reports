# Lab Report 2

This lab report is intended to show how to:

- Create a Web Server in Java using `URLHandler`
- Test, analyze, and debug code using JUnit

## Part 1: Creating a Web Server

The following implementation demonstrates code that depends on `Server.java`.
Although its implementation is not shown, it can be viewed at [this link][1].
The server created will be capable of handling incoming requests in the format:

```shell
/add-messages?s=<string>
```

### Implementing `StringServer` classes

In order to process incoming requests, we first need to create a `Handler`
class that takes the url as its input and returns a message. It's
implementation is shown below.

```java
public class Handler implements URLHandler {
    String messages = "";
    boolean hasMessage = false;

    public String handleRequest(URI url) {
        String path = url.getPath();
        if (path.equals("/")) {
            return messages;
        }
        else if (path.contains("/add-message")) {
            String[] parameters = url.getQuery().split("=");
            if (parameters[0].equals("s")) {
                if (hasMessage) {
                    messages += "\n" + parameters[1];
                }
                else {
                    messages += parameters[1];
                    hasMessage = true;
                }

                return messages;
            }
        }

        return "404 Not Found!";
    }
}
```

In order to run the server, we need to create a `StringServer` class that takes
the first command line argument (a number) and runs the server on that port.

```java
public class StringServer {
    public static void main(String[] args) throws IOException {
        if (args.length == 0) {
            System.out.println("Missing port number!"
                + "Try any number between 1024 to 49151");
        }

        int port = Integer.parseInt(args[0]);
        Server.start(port, new Handler());
    }
}
```

### Running `StringServer`

To compile and run the server, we can run the two commands below. If executed
correctly, the terminal should look like this:

```shell
$ javac Server.java Handler.java StringServer.java
$ java StringServer 8000
Server Started! Visit http://localhost:8000 to visit.
```

We can then type the path below into the browser's prompt:

```shell
/add-message?s=Hello there, how are you?
```

The server calls `handleRequests()` with argument
`new URI("http://localhost:8000/add-message?s=Hello%20there,%20how%20are%20you?")`.

The method first checks if the path contains `/add-messages`. Since it does,
the method then checks the query for the string message. The method then checks
`hasMessage` to verify if a message has already been created. Since it is
`false`, "Hello there, how are you?" is concatenated to `message` and
`hasMessage` is set to true.

Afterwards, the method will return `message` and the page should look like
this:

![Add hello there][2]

From there, we can type the path below into the browser's prompt:

```shell
/add-message?s=Why I am good. How are you?
```

The server calls `handleRequests()` with argument
`new URI("htt[://localhost:8000/add-message?s=Why%20I%20am%20good.%20How%20are%20you?")`.

The method follows a similar process as last time. However, this time
`hasMessage` is `true`. Therefore, the method will concatenate a new line and
"Why I am good. How are you?" to `message`.

Afterwards, the method will return `message` and the new page should look like
this:

![add][3]

## Part 2: Debugging `FileExample.java`

The original version of `FileExample.java` can be found at [this link][4].
Below is the file structure this method was tested with:

```shell
some-files/
|-  a.txt
|-  more-files/
    |-  b.txt
    |-  c.java
|-  even-more-files/
    |-  d.java
    |-  a.txt
```

### Code in Question

`getFiles()` is intended to return a list of all files contained within a path.
If the input is a file, then the method is expected to return a list
containing just the file. If the input is a directory, then the method is
expected to return a list of all files within the directory and any
subdirectories it may have. Below is the implementation for `getFiles()`.

```java
static List<File> getFiles(File start) throws IOException {
    File f = start;
    List<File> result = new ArrayList<>();
    result.add(start);
    if (f.isDirectory()) {
        File[] paths = f.listFiles();
        for (File subFile: paths) {
            result.add(subFile);
        }
    }
    return result;
}
```

### Failure-Inducing Test Input

This test is intended to check the returned list if given a directory as the
input. The resulting list should contain all files within the parent directory
and within any subdirectories.

```java
@Test
public void testParentDirectory() {
    File parent = new File("some-files/");

    // Check method runs properly
    List<File> result = null;
    boolean exceptionThrown = false;
    try {
        result = FileExample.getFiles(parent);
    }
    catch (IOException e) {
        exceptionThrown = true;
    }
    assertFalse(exceptionThrown);

    // Check output
    List<File> expectedOutput = new ArrayList<>(Arrays.asList(
        new File("some-files/a.txt"),
        new File("some-files/more-files/b.txt"),
        new File("some-files/more-files/c.java"),
        new File("some-files/even-more-files/d.java"),
        new File("some-files/even-more-files/a.txt")
    ));
    assertTrue(expectedOutput.containsAll(result));
}
```

<!-- TODO: Fix explanation -->
`assertFalse()` is used to determine if `parent` existed. `assertTrue()` is
used to determine whether the returned list contains all the expected elements.
Note that `assertEquals()` cannot be used here due to `listFiles()` behavior:

> There is no guarantee that the name strings in the resulting array will
> appear in any specific order; they are not, in particular, guaranteed to
> appear in alphabetical order.

### Successful Test Input

This test is intended to check the returned list if given a file as the input.
The resulting list should only contain the input file.

```java
@Test
public void testFiles() {
    File input = new File("some-files/a.txt");
    List<File> result = null;
    boolean exceptionThrown = false;

    try {
        result = FileExample.getFiles(input);
    }
    catch (IOException e) {
        exceptionThrown = true;
    }
    assertFalse(exceptionThrown);

    List<File> expectedOutput = new ArrayList<>(Arrays.asList(input));
    assertTrue(expectedOutput.containsAll(result));
}
```

Once again, `assertFalse()` is used to determine whether the file existed.
`assertTrue()` is used to determine whether the two ArrayLists are similar.

### Symptom

After compiling and running the unit test using the following commands:

```shell
javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar FileTests.java
java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore FileTests
```

We get the following output:

![Test results of buggy program][5]

Based on the output, we can tell that `testParentDirectory()` failed while
`testFiles()` succeed. Based on this information, we can tell that the actual
output of `getFiles(new File("some-files/"))` does not contain all the files
we expect.

If we look closer at the data inside the actual list, we see the following
paths:

```shell
some-files
some-files/even-more-files
some-files/more-files
some-files/a.txt
```

However, we expected `getFiles()` to return the following paths:

```shell
some-files/a.txt
some-files/more-files/b.txt
some-files/more-files/c.java
some-files/even-more-files/d.java,
some-files/even-more-files/a.txt
```

### Code Fix

There are two bugs we need to fix within our code:

1. When `start` is determined to be a directory, it is never removed from
  `result`.
2. When `start` is determined to be a directory, the for-each loop adds all
  elements of `f.listFiles()` to `result`. However, the method never determines
  if `subFile` is a directory or a file.

The first bug can be fixed by adding `result.remove(start)` inside the body
of our if-statement. The second can be fixed by recursively adding all the
elements of `getFiles(subFile))` to `results`, or
`result.addAll(getFiles(subFile))`. If the `subFile` is a directory, the method
will continue to call itself for each of its subfiles until it reaches a file.
Once there, the method will return a list containing only that file that can
be.

After implementing these fixes, the method should be:

```java
static List<File> getFiles(File start) throws IOException {
    File f = start;
    List<File> result = new ArrayList<>();
    result.add(start);
    if (f.isDirectory()) {
        result.remove(start); // Remove starting directory from results
        File[] paths = f.listFiles();
        for (File subFile: paths) {
            result.addAll(getFiles(subFile)); // Recursively add subfiles
        }
    }
    return result;
}
```

We can then recompile and run the tester class to see that our fixes worked:

![Test results of fixed method][6]

## Part 3: Lab 2 and 3 Conclusions

One thing I've learned from the previous two labs is about how basic web
servers might work. I learned about how Java is able to handle requests from
users and parse information from the path. Furthermore, I gained a bit of
insight on how multiple people might interact with a web server hosted on a
remote machine.

[1]: https://github.com/ucsd-cse15l-f22/wavelet/blob/master/Server.java
[2]: ../images/report-2/server-add-1.png
[3]: ../images/report-2/server-add-2.png
[4]: https://github.com/ucsd-cse15l-w23/lab3/blob/main/FileExample.java
[5]: ../images/report-2/test-bug-results.png
[6]: ../images/report-2/test-fix-results.png
