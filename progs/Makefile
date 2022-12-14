
topdir=$(shell pwd)/..
include $(topdir)/Make.Rules
#
# Programs: all of the examples that we will compile
#
PROGS=getpcaps capsh getcap setcap

BUILD=$(PROGS)

ifeq ($(DYNAMIC),yes)
LDPATH = LD_LIBRARY_PATH=../libcap
DEPSBUILD = all
else
LDFLAGS += --static
DEPSBUILD = libcap.a
endif

DEPS=../libcap/libcap.a

all: $(BUILD)

$(DEPS):
	make -C ../libcap $(DEPSBUILD)

$(BUILD): %: %.o $(DEPS)
	$(CC) $(CFLAGS) -o $@ $< $(LIBCAPLIB) $(LDFLAGS)

%.o: %.c $(INCS)
	$(CC) $(IPATH) $(CAPSH_SHELL) $(CFLAGS) -c $< -o $@

install: all
	mkdir -p -m 0755 $(FAKEROOT)$(SBINDIR)
	for p in $(PROGS) ; do \
		install -m 0755 $$p $(FAKEROOT)$(SBINDIR) ; \
	done
ifeq ($(RAISE_SETFCAP),yes)
	$(FAKEROOT)$(SBINDIR)/setcap cap_setfcap=i $(FAKEROOT)$(SBINDIR)/setcap
endif

test: $(PROGS)

tcapsh-static: capsh.c $(DEPS)
	$(CC) $(IPATH) $(CAPSH_SHELL) $(CFLAGS) -o $@ $< $(LIBCAPLIB) $(LDFLAGS) --static

sudotest: test tcapsh-static
	sudo $(LDPATH) ./quicktest.sh

clean:
	$(LOCALCLEAN)
	rm -f *.o $(BUILD) tcapsh* privileged ping hack.sh compare-cap
