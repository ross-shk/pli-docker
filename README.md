# pli-docker

Docker image for [Iron Spring PL/I](http://www.iron-spring.com/) with
the `plicc` compile-and-link toolchain.

The `plic` compiler is a 32-bit binary so it requires the `linux/386`
platform under Docker.

## Setup

```bash
docker compose build pli
```

Build once; thereafter the image is ready.

## Quick start

Compile and run a PL/I program in one step:

```bash
echo ' main: proc options(main); display("Hello, world!"); end;' > hello.pli
docker compose run --rm -v "$PWD":/work -w /work pli plicc hello.pli
./hello
```

Compile and run in one command:

```bash
docker compose run --rm -v "$PWD":/work -w /work pli plicc run hello.pli
```

Show toolchain help:

```bash
docker compose run --rm pli plicc help
```

Compile with verbose output:

```bash
docker compose run --rm -v "$PWD":/work -w /work pli plicc --verbose hello.pli
```

Compile only (no link):

```bash
docker compose run --rm -v "$PWD":/work -w /work pli plicc compile hello.pli
```

Compile multiple sources and link with a library:

```bash
docker compose run --rm -v "$PWD":/work -w /work pli plicc main.pli aux.pli -lnet
```

## Interactive shell

Start a bash session by default:

```bash
docker compose run --rm pli
```

## Mount a local directory

Mount your source code to edit on the host and compile inside the
container (replace `/path/to/code` with an actual absolute path):

```bash
docker compose run --rm -v /path/to/code:/work -w /work pli plicc myprog.pli
```
