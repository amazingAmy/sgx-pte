SYS ?= $(shell gcc -dumpmachine)

CC	= gcc
CFLAGS	= -Wall -O2 -std=gnu99 -fgnu89-inline -fno-builtin -nostdlib \
	  -I../include/pal -I../lib

default: all
include ../src/Makefile.Test

executables = HelloWorld File Failure Thread Fork Event Process Exception \
	      Memory Pipe Tcp Udp Yield Broadcast Ipc Server Wait HandleSend \
	      Select Segment Sleep Cpuid Pie
manifests = manifest

target = $(executables) $(manifests)

graphene_lib = .lib/graphene-lib.a
pal_lib = ../src/libpal.so
headers = $(wildcard ../include/pal/*.h)

all:	$(call expand_target,$(target))

ifeq ($(DEBUG),1)
CC += -g
endif
export DEBUG

manifest: manifest.template
	cp -f $< $@

%.manifest: %.manifest.template
	cp -f $< $@

ifeq ($(SYS),x86_64-linux-gnu)
$(executables): %: %.c $(graphene_lib) $(pal_lib) ../src/user_start.o
	@echo [ $@ ]
	@$(CC) $(CFLAGS) $(if $(filter Pie,$@),-fPIC -pie,) $^ -o $@

$(graphene_lib):
	$(MAKE) -C ../lib target=$(shell pwd)/.lib/

.PHONY: pack
pack: $(executables)
	@../../Scripts/pack_binaries.sh test $^
else
$(executables): .packed/test.tar.gz
	tar -xmozf $< $@
endif

clean:
	rm -rf $(call expand_target,$(target)) .lib *.cached
