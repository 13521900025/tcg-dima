include version.mk
TOPDIR := $(shell pwd)
INSTALL := install
CROSS_COMPILE :=
CC := $(CROSS_COMPILE)gcc
CFLAGS := -D_GNU_SOURCE -std=gnu99 -O2 -Wall -Werror
CFLAGS += -DVERSION=\"$(VERSION)\"

TOPDIR       :=
DESTDIR     :=
DIMADIR     := /usr/sbin/xdja/dima
SERVICEDIR := /usr/lib/systemd/system

SERVICE_NAME := dima-xdja.service
BIN_NAME        := dima-d
CONF_NAME     := dima.conf

CTL_NAME    := dimactl
CTLDIR         := /usr/sbin

CFLAGS += -DCONF_PATH=\"$(DIMADIR)/$(CONF_NAME)\"

OBJS_$(BIN_NAME) = \
		   main.o \
		   dimad.o \
		   subcommand.o \
		   build_info.o

OBJS_$(CTL_NAME) = \
		   dimactl.o \
		   subcommand.o

all: $(BIN_NAME) $(CTL_NAME) Makefile

clean:
	@$(RM) $(BIN_NAME) $(OBJS_$(BIN_NAME)) $(CTL_NAME) $(OBJS_$(CTL_NAME)) \
	    build_info.c

install: all
	$(INSTALL) -d -m 751 $(DESTDIR)$(DIMADIR)
	$(INSTALL) -d -m 751 $(DESTDIR)$(CTLDIR)
	$(INSTALL) -d -m 751 $(DESTDIR)$(SERVICEDIR)
	$(INSTALL) -m 770 $(BIN_NAME) $(DESTDIR)$(DIMADIR)
	$(INSTALL) -m 755 $(CTL_NAME) $(DESTDIR)$(CTLDIR)
	$(INSTALL) -m 660 $(CONF_NAME) $(DESTDIR)$(DIMADIR)
	$(INSTALL) -m 644 $(SERVICE_NAME) $(DESTDIR)$(SERVICEDIR)

$(BIN_NAME): $(OBJS_$(BIN_NAME)) 
	$(CC) $^ -o $@ $(CFLAGS) /usr/lib64/libimaevm.so

$(CTL_NAME): $(OBJS_$(CTL_NAME)) 
	$(CC) $^ -o $@ $(CFLAGS)

build_info.c: build_info.c.in
	sed  -e "s~@@DIMAD_BUILD_MACHINE@@~$(shell bash -c 'whoami | tr -d "\n"; echo -n @; uname=`uname -a`; echo -n $${uname//\~/_} | tr -d "\n"')~" \
		   -e "s~@@DIMAD_CONF_DIGEST@@~$(shell evmctl ima_hash dima.conf -n)~" \
	       < $< > $@
