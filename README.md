# pli-docker

Docker image for [Iron Spring PL/I](http://www.iron-spring.com/).

The `plic` compiler is a 32-bit binary so it requires the `linux/386` platform under Docker.

This image has been tested on Linux, macOS, and Windows (via Docker Desktop's bundled QEMU).

## Usage

Build and run using ```docker compose```:

```bash
docker compose build pli
docker compose run --rm pli
```

## Test

Inside the container:

```bash
plic -V
```
```bash
echo 'main: proc options(main); display("Hello"); end;' > test.pli
```
```bash
plic -C -dELF test.pli
gcc -m32 -no-pie -z muldefs -o test test.o -lprf
```
```bash
./test
```

## Mount a local directory

Mount your source code into the container to edit on the host and compile inside (replace ```</path/to/code>``` with an actual absolute path to your code):

```bash
docker compose run --rm -v </path/to/code>:/pli-dev/projects pli
```
