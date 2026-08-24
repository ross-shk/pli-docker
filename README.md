# pli-docker

Unofficial Docker image for PL/I (Iron Spring PL/I compiler) with the `plicc` compile-and-link toolchain. The compiler is a 32-bit binary so it requires the `linux/386`
platform under Docker. Not affiliated with Iron Spring.

```bash
docker pull ghcr.io/ross-shk/pli:1.4.1
```

## Setup

```bash
docker compose build pli
```

Build once; thereafter the image is ready.

## Quick start

Compile and run a PL/I program in one step:

```bash
echo ' main: proc options(main); display("Hello, world!"); end;' > hello.pli
docker compose run --rm -v "$PWD":/work -w /work pli plicc run hello.pli
```

Start a bash session by default, mount your current working directory as `/work`:

```bash
docker compose run --rm -v $PWD:/work pli
```

Replace `$PWD` with an absolute path if you want to mount a different directory.

Show toolchain help:

```bash
docker compose run --rm pli plicc help
```

