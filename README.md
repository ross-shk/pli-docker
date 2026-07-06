# pli-docker

Docker image for [Iron Spring PL/I](http://www.iron-spring.com/) on Debian 12 (32-bit).

The `plic` compiler is a 32-bit binary so it requires the `linux/386` platform under Docker.

This image has been tested on Linux, macOS, and Windows (via Docker Desktop's bundled QEMU).

## Usage

Build and run using docker compose (recommended):

```bash
docker compose build pli
```

```bash
docker compose run --rm pli
```

or using Docker directly:

```bash
docker build --platform linux/386 -t pli-dev pli/
```
```bash
docker run --platform linux/386 --security-opt seccomp=unconfined -it pli-dev
```

## Test

```bash
plic -V
echo 'main: proc options(main); display("Hello"); end;' > test.pli
plic -C -dELF test.pli
gcc -m32 -no-pie -z muldefs -o test test.o -lprf
./test
```

## Mount a local directory

Mount your source code into the container to edit on the host and compile inside (replace ```</path/to/code>``` with an actual absolute path to your code):

```bash
docker compose run --rm -v </path/to/code>:/pli-dev/projects pli
```

Or with plain Docker:

```bash
docker run --platform linux/386 --security-opt seccomp=unconfined -v </path/to/code>:/pli-dev/projects -it pli-dev
```
