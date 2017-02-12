---
description: ""
title: "Load rJava in RStudio using macOS 10.12"
date: "2017-02-12T16:55:11+01:00"
categories: RStudio R rJava macOS
comments: true
draft: false
url: /2017-02-12-fix-onload-failed-rjava-macos/
---

This post describes a fix of the `.onLoad failed in loadNamespace() for 'rJava'` error, which occurs when loading `rJava` in `RStudio` using `macOS`.

# The '2015' workaround stopped working

In beginning of [2015 I wrote a blog post that shows you how to run `rJava` in `RStudio` using `OSX 10.10`](http://paulklemm.com/blog/2015-02-20-run-rjava-with-rstudio-under-osx-10-dot-10/). The fix stopped working. [Toontje](https://disqus.com/by/disqus_yW0scDAhvn/), a reader of my blog, came for the rescue.

The workaround was tested using the following software:

- `R 3.3.2`
- `RStudio 1.0.136`
- `macOS 10.12.3 (16D32)`
- `jdk 1.8.0_112`
- `rJava 0.9-8`

# Loading rJava yields ".onload failed ... for rJava" error

I installed `rJava` using `install.packages('rJava')` without errors. Loading the library in an `R` session launched in the terminal works. Loading `rJava` in `RStudio` yields the following error:

{{< highlight r >}}
library('rJava')
Error : .onLoad failed in loadNamespace() for 'rJava', details:
  call: dyn.load(file, DLLpath = DLLpath, ...)
  error: unable to load shared object '/Users/paul/Desktop/rJava_test/packrat/lib/x86_64-apple-darwin15.6.0/3.3.2/rJava/libs/rJava.so':
  dlopen(/Users/paul/Desktop/rJava_test/packrat/lib/x86_64-apple-darwin15.6.0/3.3.2/rJava/libs/rJava.so, 6): Library not loaded: @rpath/libjvm.dylib
  Referenced from: /Users/paul/Desktop/rJava_test/packrat/lib/x86_64-apple-darwin15.6.0/3.3.2/rJava/libs/rJava.so
  Reason: image not found
Error: package or namespace load failed for ‘rJava’
{{< /highlight >}}

# Use "dyn.load(\<your_jre_path\>)" to fix the problem

Launching `RStudio` using a custom call specifying the `jre` library [doesn't work anymore](http://paulklemm.com/blog/2015-02-20-run-rjava-with-rstudio-under-osx-10-dot-10/). In the new approach we will load it directly to the running `R` session.

## Check if everything works as expected

At first we check if everything else works as expected.

{{< highlight bash >}}
# `which java` should yield something like this: `/usr/bin/java`
$ which java
/usr/bin/java
# `/usr/libexec/java_home` should yield the current jdk home folder
$ /usr/libexec/java_home
/Library/Java/JavaVirtualMachines/jdk1.8.0_31.jdk/Contents/Home
{{< /highlight >}}

You can also use [`/usr/libexec/java_home`](https://stackoverflow.com/questions/21964709/how-to-change-default-java-version) to [switch your java version](https://stackoverflow.com/questions/21964709/how-to-change-default-java-version) if you wish. You can download the newest `jre` from the Oracle [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

If the output is similar to the one above you should be fine!

## Run "dyn.load(\<your_jre_path\>)" in RStudio

Open a new `RStudio` session. Now, load the `jre` library using the command:

{{< highlight r >}}
dyn.load(paste0(system2('/usr/libexec/java_home', stdout = TRUE), '/jre/lib/server/libjvm.dylib'))
{{< /highlight >}}

This command executes `/usr/libexec/java_home` and concatinates the stdout to the library path `/jre/lib/server/libjvm.dylib`.

That's it. Now you should be able to load `library(rJava)` without errors.
