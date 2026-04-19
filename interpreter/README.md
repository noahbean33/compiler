

### Prerequisites

I develop on an OS X machine, but any POSIX system should work too. With a
little extra effort, you should be able to get this working on Windows as well,
though I can't help you out much.

Most of the work is orchestrated by make. The build scripts, test runner, and
other utilities are all written in [Dart][]. Instructions to install Dart are
[here][install]. Once you have Dart installed and on your path, run:

```sh
$ make get
```

[dart]: https://dart.dev/
[install]: https://dart.dev/get-dart

This downloads all of the packages used by the build and test scripts.

In order to compile the two interpreters, you also need a C compiler on your
path as well as `javac`.

### Building

Once you've got that setup, try:

```sh
$ make
```

If everything is working, that will generate the site for the book as well as
compiling the two interpreters clox and jlox. You can run either interpreter
right from the root of the repo:

```sh
$ ./clox
$ ./jlox
```

### Hacking on the book

The Markdown and snippets of source code are woven together into the final HTML
using a hand-written static site generator that started out as a [single tiny
Python script][py] for [my first book][gpp] and somehow grew into something
approximating a real program.

[py]: https://github.com/munificent/game-programming-patterns/blob/master/script/format.py
[gpp]: http://gameprogrammingpatterns.com/

The generated HTML is committed in the repo under `site/`. It is built from a
combination of Markdown for prose, which lives in `book/`, and snippets of code
that are weaved in from the Java and C implementations in `java/` and `c/`. (All
of those funny looking comments in the source code are how it knows which
snippet goes where.)

The script that does all the magic is `tool/bin/build.dart`. You can run that
directly, or run:

```sh
$ make book
```

That generates the entire site in one batch. If you are incrementally working
on it, you'll want to run the development server:

```sh
$ make serve
```

This runs a little HTTP server on localhost rooted at the `site/` directory.
Any time you request a page, it regenerates any files whose sources have been
changed, including Markdown files, interpreter source files, templates, and
assets. Just let that keep running, edit files locally, and refresh your
browser to see the changes.

### Building the interpreters

You can build each interpreter like so:

```sh
$ make clox
$ make jlox
```

This builds the final version of each interpreter as it appears at the end of
its part in the book.

You can also see what the interpreters look like at the end of each chapter. (I
use this to make sure they are working even in the middle of the book.) This is
driven by a script, `tool/bin/split_chapters.dart` that uses the same comment
markers for the code snippets to determine which chunks of code are present in
each chapter. It takes only the snippets that have been seen by the end of each
chapter and produces a new copy of the source in `gen/`, one directory for each
chapter's code. (These are also an easier way to view the source code since they
have all of the distracting marker comments stripped out.)

Then, each of those can be built separately. Run:

```sh
$ make c_chapters
```

And in the `build/` directory, you'll get an executable for each chapter, like
`chap14_chunks`, etc. Likewise:

```sh
$ make java_chapters
```

This compiles the Java code to classfiles in `build/gen/` in a subdirectory for
each chapter.

## Testing

I have a full Lox test suite that I use to ensure the interpreters in the book
do what they're supposed to do. The test cases live in `test/`. The Dart
program `tool/bin/test.dart` is a test runner that runs each of those test
files on a Lox interpreter, parses the result, and validates that that the test
does what it's expected to do.

There are various interpreters you can run the tests against:

```sh
$ make test       # The final versions of clox and jlox.
$ make test_clox  # The final version of clox.
$ make test_jlox  # The final version of jlox.
$ make test_c     # Every chapter's version of clox.
$ make test_java  # Every chapter's version of jlox.
$ make test_all   # All of the above.
```

### Testing your implementation

You are welcome to use the test suite and the test runner to test your own Lox
implementation. The test runner is at `tool/bin/test.dart` and can be given a
custom interpreter executable to run using `--interpreter`. For example, if you
had an interpreter executable at `my_code/boblox`, you could test it like:

```sh
$ dart tool/bin/test.dart clox --interpreter my_code/boblox
```

You still need to tell it which suite of tests to run because that determines
the test expectations. If your interpreter should behave like jlox, use "jlox"
as the suite name. If it behaves like clox, use "clox". If your interpreter is
only complete up to the end of one of the chapters in the book, you can use
that chapter as the suite, like "chap10_functions". See the Makefile for the
names of all of the chapters.

If your interpreter needs other command line arguments passed to use, pass them
to the test runner using `--arguments` and it will forward to your interpreter.

## Repository Layout

*   `asset/` – Sass files and jinja2 templates used to generate the site.
*   `book/` - Markdown files for the text of each chapter.
*   `build/` - Intermediate files and other build output (except for the site
    itself) go here. Not committed to Git.
*   `c/` – Source code of clox, the interpreter written in C. Also contains an
    XCode project, if that's your thing.
*   `gen/` – Java source files generated by GenerateAst.java go here. Not
    committed.
*   `java/` – Source code of jlox, the interpreter written in Java.
*   `note/` – Various research, notes, TODOs, and other miscellanea.
*   `note/answers` – Sample answers for the challenges. No cheating!
*   `site/` – The final generated site. The contents of this directory directly
    mirror craftinginterpreters.com. Most content here is generated by build.py,
    but fonts, images, and JS only live here. Everything is committed, even the
    generated content.
*   `test/` – Test cases for the Lox implementations.
*   `tool/` – Dart package containing the build, test, and other scripts.
