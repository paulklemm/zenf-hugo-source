---
categories: RStudio R OSX
comments: true
date: 2015-02-20T14:30:10Z
published: true
title: Run rJava with RStudio using OSX 10.10
url: /2015-02-20-run-rjava-with-rstudio-under-osx-10-dot-10/
---

# Update 2017-02-12: Newer fix available

The workaround described in this post seized working for newer version of `rJava` and `RStudio`. I wrote a new blog post using [Toontje's](https://disqus.com/by/disqus_yW0scDAhvn/) fix here: [Load rJava in RStudio using macOS 10.12](http://paulklemm.com/blog/2017-02-12-fix-onload-failed-rjava-macos/).

The post below refers to older version of `rJava`, `RStudio` and `macOS`.

# The Problem

I've had some trouble getting rJava to run with RStudio. My current solution is a small workaround based on [this post on the RStudio support pages](https://support.rstudio.com/hc/communities/public/questions/203781666-rJava-not-loading-in-RStudio-Mac-OS-X-10-10-but-loading-in-terminal).

The first thing I did was installing and running `rJava` through `RStudio` and attempt to load it.

{{< highlight r >}}
install.packages('rJava')
# a bunch of output follows
# ...
# Now we want to load rJava, which yields an error
library(rJava)
Error : .onLoad failed in loadNamespace() for 'rJava', details:
  call: dyn.load(file, DLLpath = DLLpath, ...)
  error: unable to load shared object '/usr/local/Cellar/r/3.1.2_1/R.framework/Versions/3.1/Resources/library/rJava/libs/rJava.so':
  dlopen(/usr/local/Cellar/r/3.1.2_1/R.framework/Versions/3.1/Resources/library/rJava/libs/rJava.so, 6): Library not loaded: @rpath/libjvm.dylib
  Referenced from: /usr/local/Cellar/r/3.1.2_1/R.framework/Versions/3.1/Resources/library/rJava/libs/rJava.so
  Reason: image not found
Error: package or namespace load failed for ‘rJava’
{{< /highlight >}}

When I've ran `R` through the Terminal and ran up `library(rJava)`, everything worked just fine. Somehow, `RStudio` is not able to properly load the java path. A small workaround helps here.

# The Workaround

We will launch `RStudio` through the terminal with a custom call, which gives it the proper `Java` path!

## Check if everything works as expected

At first we check a couple of things.

{{< highlight bash >}}
# `which java` should yield something like this: `/usr/bin/java`
$ which java
/usr/bin/java
# `/usr/libexec/java_home` should yield the current jdk home folder
$ /usr/libexec/java_home
/Library/Java/JavaVirtualMachines/jdk1.8.0_31.jdk/Contents/Home
{{< /highlight >}}

By the way, you can also use [`/usr/libexec/java_home`](https://stackoverflow.com/questions/21964709/how-to-change-default-java-version) to [switch your java version](https://stackoverflow.com/questions/21964709/how-to-change-default-java-version) if you wish. You can also download the newest `jre` from the Oracle [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

If the output is similar to the one above you should be fine!

## Run `RStudio` through the Terminal using a custom command

Now you open `RStudio` through the terminal using the following command, which sets the `LD_LIBRARY_PATH` by hard. *Make sure you quit all running instances of `RStudio` before you do that!*

{{< highlight bash >}}
$ LD_LIBRARY_PATH=$(/usr/libexec/java_home)/jre/lib/server: open -a RStudio
{{< /highlight >}}

`RStudio` launches. Loading `library(rJava)` should work just fine! *If you want to use `rJava` with `RStudio`, you have to run it using this command (until they hopefully fix it!)*

## Make an Alias for the new Command with your Shell

You can register an alias with your shell. For `bash` you open the bash profile under `~/.bash_profile` and add the following alias:

{{< highlight bash >}}
alias rstudio='LD_LIBRARY_PATH=$(/usr/libexec/java_home)/jre/lib/server: open -a RStudio'
{{< /highlight >}}

# Thats it

I hope this gets fixed soon. Until then, hopefully my workaround works for you guys! See you :).

# Update 2015-12-09

In my current setting of *OSX 10.11.2 (15C50)*, *R version 3.2.2 (2015-08-14)* and *rJava 0.9-8*, this solution does not work. Unfortunately, I have no idea how to fix it. Here is my current situation.

## Installing the rJava from CRAN fails

Since [El Capitan seems to have problems with passing evironment variables](https://stat.ethz.ch/pipermail/r-sig-mac/2015-November/011712.html), the current CRAN version of rJava will crash during the installation process. It yields the following error:

{{< highlight r >}}
install.packages("rJava")
# Output until error skipped
configure: error: One or more JNI types differ from the corresponding native type. You may need to use non-standard compiler flags or a different compiler in order to fix this.
ERROR: configuration failed for package ‘rJava’
* removing ‘/usr/local/lib/R/3.2/site-library/rJava’
{{< /highlight >}}

This can, however, be easily fixed by installing [rJava from RForge.net](https://www.rforge.net/rJava/files/) as stated [here](https://stackoverflow.com/questions/33550437/install-rjava-one-or-more-jni-types-differ-from-the-corresponding-native-type). Running `install.packages('<PATH TO DOWNLOAD>/rJava_0.9-8.tar.gz', repos = NULL, type="source")` installed the current rJava version properly.

## Loading rJava in RStudio fails

I've set up my homebrew R version properly using `R CMD javareconf JAVA_CPPFLAGS="-I/System/Library/Frameworks/JavaVM.framework/Headers -I/Library/Java/JavaVirtualMachines/jdk1.8.0_31.jdk/"` as statet in the caveats of the install. Also, `/usr/libexec/java_home` yields the proper path.

Loading rJava in R through the shell works fine. Loading RStudio with `LD_LIBRARY_PATH=$(/usr/libexec/java_home)/jre/lib/server: open -a RStudio` and loading rJava does not. It yields the same error as before:

{{< highlight r >}}
library(rJava)
Error : .onLoad failed in loadNamespace() for 'rJava', details:
  call: dyn.load(file, DLLpath = DLLpath, ...)
  error: unable to load shared object '/usr/local/lib/R/3.2/site-library/rJava/libs/rJava.so':
  dlopen(/usr/local/lib/R/3.2/site-library/rJava/libs/rJava.so, 6): Library not loaded: @rpath/libjvm.dylib
  Referenced from: /usr/local/lib/R/3.2/site-library/rJava/libs/rJava.so
  Reason: image not found
Error: package or namespace load failed for ‘rJava’
{{< /highlight >}}

## The Solution?

If you have any ideas on how to solve this, please let me know.