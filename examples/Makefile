# Alternative to plicc: raw plic + gcc
# Usage (inside container): make -C examples hello && ./examples/hello
PLIC ?= plic
CCLD ?= gcc
PLIINC ?= -i/usr/lib/pli/include
LDFLAGS ?= -m32 -no-pie -z muldefs
LIBS ?= -lprf
CINTEROP_LIBS ?= /usr/lib/pli/alt/fhs.o /usr/lib/pli/alt/ghs.o

all: hello multi_app cinterop_app flags_app

hello: hello.o
	$(CCLD) $(LDFLAGS) -o $@ $^ $(LIBS)

hello.o: hello.pli
	$(PLIC) -C -dELF -ls $(PLIINC) $< -o $@

multi_app: multi/main.o multi/greet.o
	$(CCLD) $(LDFLAGS) -o $@ $^ $(LIBS)

multi/%.o: multi/%.pli
	$(PLIC) -C -dELF -ls $(PLIINC) $< -o $@

cinterop_app: cinterop/prog.o cinterop/helper.o
	$(CCLD) $(LDFLAGS) -o $@ $^ $(CINTEROP_LIBS) $(LIBS)

cinterop/prog.o: cinterop/prog.pli
	$(PLIC) -C -dELF -ls $(PLIINC) $< -o $@

cinterop/helper.o: cinterop/helper.c
	$(CCLD) -m32 -c $< -o $@

flags_app: flags/payroll.o
	$(CCLD) $(LDFLAGS) -o $@ $^ $(LIBS)

flags/payroll.o: flags/payroll.pli flags/include/defs.inc
	$(PLIC) -C -dELF -ls $(PLIINC) -i./flags/include $< -o $@

clean:
	rm -f hello multi_app cinterop_app flags_app *.o *.lst multi/*.o multi/*.lst cinterop/*.o cinterop/*.lst flags/*.o flags/*.lst

.PHONY: all clean hello multi_app cinterop_app flags_app
