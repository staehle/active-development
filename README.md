# The Principles of Active Development

## Forward

**Active Development** is the concept of how easy it is to actively develop code when using your build tools.

Here, we're not discussing or comparing individual build tools like Make, Autotools, CMake, Bazel, Yocto/OpenEmbedded, Buildroot, Visual Studio Projects, etc., but rather _how_ to compare them by using _The Principles of Active Development_. 

Do they allow you, as a software developer, to have a streamlined workflow to efficiently do what you want these tools to do?  Our tools are not perfect -- They get in our way.  They frustrate us.  They don't do what we want them to do.  They delay us from actually solving the issues we want them to solve by making us debug the tools instead of our code.

There are many different scenarios in which we can think of _Active Development_ differently.  If you have a single application with all your source code in one spot, you're using libraries from your host system, and you're compiling just a local application -- then "Active Development" means something different for you than a software engineer that is working with multiple (maybe a hundred) different code bases that are being cross-compiled together to build an entire embedded Linux distribution.  You're using entirely different tools and different workflows -- maybe the only commonality between these two developers is the programming language they're using and their editor! 

Regardless of the situation, _The Principles of Active Development_ boils this down into some simple metrics that can be used to evaluate those tools in your workflow: How are they affecting your ability to iterate on code changes, the speed in which you can build and test those changes, and the reliability of your tools in doing so.  The Principles have been written from the viewpoint of a software developer who primarily uses build tools for embedded OS development, but they are intended to be generic enough for any development workflow scenario.

## The Principles of Active Development

1) The source code for a project should be in a well-defined and easily accessible area.  During a build, that source code should be utilized, but build artifacts should also be in a separate, well-defined, and easily accessible area.  A developer should be able to make changes to the source code, but the build should not.

2) Build tools typically need configuration files to build your project.  A developer should be able to open these files, immediately understand what they are doing, and be able to make changes to them.

3) When building a project, a change in the source code or build configuration should only cause those changed areas, and any dependent areas, to be re-built.

4) Every point of the build should be reproducible.  Re-running any area of a build should produce the same results given the same source code.  This also means a developer should be able to re-build any area.

5) A build tool that also manages source code and dependencies should do so in a distinctly separate stage from the build stage, and place them in the common source location as defined in Principle #1. If you build something, but you don't already have the source, how do you know what you're building?


## Shorthand of Active Development

Each Principle can be redefined as these phrases:

1) Single Source Tree, Single Build Tree
2) Ease of Inspection
3) Incrementally Safe
4) Reproducible Builds
5) Know the Source before the Build

## Tests of Active Development

Here are some simple tests you can run on your build tool to test for each of the principles:

1) Single Source Tree, Single Build Tree:

    * If I'm working with multiple repositories, they should be in a common place, say a "sources" directory.  When I use my build tools to build these, the build artifacts should be in an entirely separate "factory" directory.  It doesn't have to use these names exactly, but the general idea.
    * Did running a build cause something to be downloaded?
    * Did running a build place artifacts in the same directory as your source?
    * Did your build tool place your build artifacts in some magical location you can't find easily?
    * If you answered YES to any of these questions, then your build tool has failed this Principle.


2) Ease of Inspection:

    * If you need to change _how_ your build tool is building your source, you might need to edit your build tool's configuration files for that source.
    * Can you read this file without digging into documentation?
    * Can you easily figure out what individual commands are being generated and ran as a result of this configuration file?  (Ideally, these would be placed in some log file)
    * Can you make edits to this file without it causing unintended side-effects for other parts in your build tool?
    * If you want to add a new item to your build, with a new build configuration file, is it easy to do so?
    * If you answered NO to any of these questions, then your build tool has failed this Principle.


3) Incrementally Safe:

    * If you build your project once, then make a change to a single source file, what does your build tool need to do in order to build only that change?
    * Does your build tool need to re-build unrelated areas as a result of your change?
    * Does it FAIL to rebuild any other objects/areas that are dependent (use this object as a dependency)?  What about dependents of dependents?
    * Does it re-build areas that you have not requested? (e.g. If you only request your tool to build this object, it should only build this object. If you request your tool to build the whole project, then it should build this object and any dependents)
    * If you answered YES to any of these questions, then your build tool has failed this Principle.

4) Reproducible Builds:

    * If your build tool, given the same source code, environment, and configuration to build that source code, it should produce the exact same artifact.
    * If it does not, then your build tool has failed this Principle.
    * Smart build tools will extend their reproducible build support with Caching. They can do so on an object-level (a single compilation unit), or a component-level basis (an entire source tree) if you're using that type of tool and are using multiple repositories.  It can be extended even further if you're working with a team of developers and can _share_ that cache among themselves from a centralized artifact server so two different machines never have to compile the same code more than once.

5) Know the Source before the Build:

    * If your tool does this properly, there should be two distinct commands a developer should run when doing a fresh build:  First, downloading the source.  Second, running the build.
    * When downloading the source, is it always downloaded to a single common location? (Principle #1)
    * Is your build tool only downloading the source you need for what you intend to build?
    * Can you make changes to any point of the source code easily?
    * If you answered NO to any of these questions, then your build tool has failed this Principle.
    * Exception: If your build tool utilizes caching and uses a remote server to store cached artifacts, then it does NOT need to download source at any point in your build if you can simply re-use a pre-built cached artifact. This assumes your build tool has a good object identification/hashing system.

