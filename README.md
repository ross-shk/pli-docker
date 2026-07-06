# pli-docker

Docker image for [Iron Spring PL/I](http://www.iron-spring.com/) on Debian 12 (32-bit).

The `plic` compiler is a 32-bit binary so it requires the `linux/386` platform under Docker.

This images has been tested on Linux.

## Usage

# Build
```bash
docker build --platform linux/386 -t pli-dev pli/
```

# Run
```
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
