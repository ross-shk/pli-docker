# pli-docker

Unofficial Docker image for PL/I (Iron Spring PL/I compiler) with the `plicc` compile-and-link toolchain. The compiler is a 32-bit binary so it requires the `linux/386`
platform under Docker. Not affiliated with Iron Spring.

```bash
docker pull ghcr.io/ross-shk/pli:1.4.1  # GHCR
docker pull ross-shk/pli:1.4.1          # Docker Hub
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

## AI / Agent usage

Non-interactive service `pli-agent` (`docker-compose.yml:14` `stdin_open: false`/`tty: false`) avoids hangs in coding agents:

```bash
# preferred for opencode / CI — no tty, QEMU for linux/386 handled via setup-qemu-action on GH Actions
docker compose --profile agent run --rm -v "$PWD":/work -w /work pli-agent plicc run examples/hello.pli
echo "rc:$?"  # 0 ok, 4 warn, 8 error
grep -E '\(ERR[0-9]+\)|\(WRN[0-9]+\)' *.lst || true  # structured diagnostics (no --json, see plicc:1)
```

`plicc run --verbose` shows compile/link steps. Image carries OCI labels (`org.opencontainers.image.*` at `pli/Dockerfile:19`) + SBOM/provenance via `buildx` (`.github/workflows/docker.yml`).

