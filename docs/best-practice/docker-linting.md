### Linting your Dockerfile
There are multiple tools for linting Dockerfiles, including Buddy's official Dockerfile Linter: https://github.com/buddy-works/dockerfile-linter

For the purposes of the guide, however, we'll use hadolint, with a brief mention of dockerfilelint the end.

#### Hadolint

Hadolint parses the Dockerfile into an abstract syntax tree (AST), which is a structured object representing the contents of the Dockerfile. In concept, it's similar to how your browser parses HTML source code into the Document Object Model (DOM).


Hadolint then tests the AST against a list of rules to detect places in the Dockerfile which does not follow best practices. Let's run it against our Dockerfile to see where we can improve.

The easiest way to run hadolint is by running the hadolint/hadolint image using Docker.

```sh 
$ docker pull hadolint/hadolint
$ docker run --rm -i hadolint/hadolint 
 < Dockerfile /dev/stdin:1 DL3006 Always tag the version of an image explicitly
 
# You can notice that Hadolint displayed the DL3006 error, which says that the first line of the Dockerfile (/dev/stdin:1) should # use a tagged image.

```