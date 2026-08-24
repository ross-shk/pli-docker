# Examples

Runnable via `plicc` inside the container. Each directory is self-contained.

```bash
docker compose run --rm -v "$PWD":/work -w /work pli bash
```

## 1. hello — single file (`hello.pli`)

```bash
plicc run examples/hello.pli
# or: plicc examples/hello.pli --out hello && ./hello
```

Minimal `proc options(main)` + `display`.

## 2. multi — multiple sources (`multi/main.pli` + `multi/greet.pli`)

```bash
plicc multi/main.pli multi/greet.pli --out multi_app
./multi_app
# compile-only then link:
plicc compile multi/main.pli multi/greet.pli
plicc multi/main.o multi/greet.o --out multi_app
```

Tests basename collision guard and multi-object link.

## 3. cinterop — PL/I + C (`cinterop/prog.pli` + `cinterop/helper.c`)

```bash
gcc -m32 -c examples/cinterop/helper.c -o helper.o
plicc examples/cinterop/prog.pli helper.o --cinterop --out cinterop_app
./cinterop_app  # -> cinterop demo - PL/I + C linked
```

Requires `--cinterop` (links `fhs.o`/`ghs.o` from `pli/Dockerfile:48`). Without it, mixing PL/I and C objects fails to link (heap mismatch). Prog does not call `add` to avoid PL/I↔C calling-convention pitfalls — see `plicc run --verbose` for link step.

## 4. flags — includes & listings (`flags/payroll.pli` + `flags/include/defs.inc`)

```bash
# Note: Iron Spring `%include` uses ` %include defs; ` (no quotes/extension), searched via -i
plicc -i./examples/flags/include -lsiaxgmo examples/flags/payroll.pli --out payroll
./payroll  # -> x=42
# try: plicc compile -i./examples/flags/include examples/flags/payroll.pli && cat payroll.lst
```

Shows `-i` include path and `-ls` listing flags (keeps `.lst`). Try `plicc run --verbose` to see compile/link steps.

## Makefile alternative

`examples/Makefile` is the `make` equivalent of the above (no `plicc` needed):

```bash
make -C examples hello && ./examples/hello
make -C examples multi_app cinterop_app flags_app
./examples/multi_app && ./examples/cinterop_app && ./examples/flags_app
make -C examples clean
```

Use `plicc` for quick scripts, `make` for incremental/CI builds.

## Agent notes

Agents should use `pli-agent` (non-tty) and parse diagnostics via `*.lst`:

```bash
docker compose --profile agent run --rm -v "$PWD":/work -w /work pli-agent plicc run --verbose examples/hello.pli
grep -E '\(ERR[0-9]+\)|\(WRN[0-9]+\)' hello.lst || echo "clean"
```

Requires `linux/386` + `seccomp=unconfined` (`docker-compose.yml:5,8`) — on GH Actions use `docker/setup-qemu-action`.
