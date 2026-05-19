VERSION = 4
PATCHLEVEL = 9
SUBLEVEL = 190
EXTRAVERSION =
NAME = Roaring Lionus

# *DOCUMENTATION*
# To see a list of typical targets execute "make help"
# More info can be located in ./README
# Comments in this file are targeted only to the developer, do not
# expect to learn how to build the kernel reading this file.

# o Do not use make's built-in rules and variables
#   (this increases performance and avoids hard-to-debug behaviour);
# o Look for make include files relative to root of kernel src
MAKEFLAGS += -rR --include-dir=$(CURDIR)

# Avoid funny character set dependencies
unexport LC_ALL
LC_COLLATE=C
LC_NUMERIC=C
export LC_COLLATE LC_NUMERIC

# Avoid interference with shell env settings
unexport GREP_OPTIONS

# We are using a recursive build, so we need to do a little thinking
# to get the ordering right.
#
# Most importantly: sub-Makefiles should only ever modify files in
# their own directory. If in some directory we have a dependency on
# a file in another dir (which doesn't happen often, but it's often
# unavoidable when linking the built-in.o targets which finally
# turn into vmlinux), we will call a sub make in that other dir, and
# after that we are sure that everything which is in that other dir
# is now up to date.
#
# The only cases where we need to modify files which have global
# effects are thus separated out and done before the recursive
# descending is started. They are now explicitly listed as the
# prepare rule.

# Beautify output
# ---------------------------------------------------------------------------
#
# Normally, we echo the whole command before executing it. By making
# that echo $($(quiet)$(cmd)), we now have the possibility to set
# $(quiet) to choose other forms of output instead, e.g.
#
#         quiet_cmd_cc_o_c = Compiling $(RELDIR)/$@
#         cmd_cc_o_c       = $(CC) $(c_flags) -c -o $@ $<
#
# If $(quiet) is empty, the whole command will be printed.
# If it is set to "quiet_", only the short version will be printed.
# If it is set to "silent_", nothing will be printed at all, since
# the variable $(silent_cmd_cc_o_c) doesn't exist.
#
# A simple variant is to prefix commands with $(Q) - that's useful
# for commands that shall be hidden in non-verbose mode.
#
#	$(Q)ln $@ :<
#
# If KBUILD_VERBOSE equals 0 then the above command will be hidden.
# If KBUILD_VERBOSE equals 1 then the above command is displayed.
#
# To put more focus on warnings, be less verbose as default
# Use 'make V=1' to see the full commands

ifeq ("$(origin V)", "command line")
  KBUILD_VERBOSE = $(V)
endif
ifndef KBUILD_VERBOSE
  KBUILD_VERBOSE = 0
endif

ifeq ($(KBUILD_VERBOSE),1)
  quiet =
  Q =
else
  quiet=quiet_
  Q = @
endif

# If the user is running make -s (silent mode), suppress echoing of
# commands

ifneq ($(filter 4.%,$(MAKE_VERSION)),)	# make-4
ifneq ($(filter %s ,$(firstword x$(MAKEFLAGS))),)
  quiet=silent_
  tools_silent=s
endif
else					# make-3.8x
ifneq ($(filter s% -s%,$(MAKEFLAGS)),)
  quiet=silent_
  tools_silent=-s
endif
endif

export quiet Q KBUILD_VERBOSE

# kbuild supports saving output files in a separate directory.
# To locate output files in a separate directory two syntaxes are supported.
# In both cases the working directory must be the root of the kernel src.
# 1) O=
# Use "make O=dir/to/store/output/files/"
#
# 2) Set KBUILD_OUTPUT
# Set the environment variable KBUILD_OUTPUT to point to the directory
# where the output files shall be placed.
# export KBUILD_OUTPUT=dir/to/store/output/files/
# make
#
# The O= assignment takes precedence over the KBUILD_OUTPUT environment
# variable.

# KBUILD_SRC is set on invocation of make in OBJ directory
# KBUILD_SRC is not intended to be used by the regular user (for now)
ifeq ($(KBUILD_SRC),)

# OK, Make called in directory where kernel src resides
# Do we want to locate output files in a separate directory?
ifeq ("$(origin O)", "command line")
  KBUILD_OUTPUT := $(O)
endif

# That's our default target when none is given on the command line
PHONY := _all
_all:

# Cancel implicit rules on top Makefile
$(CURDIR)/Makefile Makefile: ;

ifneq ($(words $(subst :, ,$(CURDIR))), 1)
  $(error main directory cannot contain spaces nor colons)
endif

ifneq ($(KBUILD_OUTPUT),)
# Invoke a second make in the output directory, passing relevant variables
# check that the output directory actually exists
saved-output := $(KBUILD_OUTPUT)
KBUILD_OUTPUT := $(shell mkdir -p $(KBUILD_OUTPUT) && cd $(KBUILD_OUTPUT) \
								&& /bin/pwd)
$(if $(KBUILD_OUTPUT),, \
     $(error failed to create output directory "$(saved-output)"))

PHONY += $(MAKECMDGOALS) sub-make

$(filter-out _all sub-make $(CURDIR)/Makefile, $(MAKECMDGOALS)) _all: sub-make
	@:

sub-make:
	$(Q)$(MAKE) -C $(KBUILD_OUTPUT) KBUILD_SRC=$(CURDIR) \
	-f $(CURDIR)/Makefile $(filter-out _all sub-make,$(MAKECMDGOALS))

# Leave processing to above invocation of make
skip-makefile := 1
endif # ifneq ($(KBUILD_OUTPUT),)
endif # ifeq ($(KBUILD_SRC),)

# We process the rest of the Makefile if this is the final invocation of make
ifeq ($(skip-makefile),)

# Do not print "Entering directory ...",
# but we want to display it when entering to the output directory
# so that IDEs/editors are able to understand relative filenames.
MAKEFLAGS += --no-print-directory

# Call a source code checker (by default, "sparse") as part of the
# C compilation.
#
# Use 'make C=1' to enable checking of only re-compiled files.
# Use 'make C=2' to enable checking of *all* source files, regardless
# of whether they are re-compiled or not.
#
# See the file "Documentation/sparse.txt" for more details, including
# where to get the "sparse" utility.

ifeq ("$(origin C)", "command line")
  KBUILD_CHECKSRC = $(C)
endif
ifndef KBUILD_CHECKSRC
  KBUILD_CHECKSRC = 0
endif

# Use make M=dir to specify directory of external module to build
# Old syntax make ... SUBDIRS=$PWD is still supported
# Setting the environment variable KBUILD_EXTMOD take precedence
ifdef SUBDIRS
  KBUILD_EXTMOD ?= $(SUBDIRS)
endif

ifeq ("$(origin M)", "command line")
  KBUILD_EXTMOD := $(M)
endif

# If building an external module we do not care about the all: rule
# but instead _all depend on modules
PHONY += all
ifeq ($(KBUILD_EXTMOD),)
_all: all
else
_all: modules
endif

ifeq ($(KBUILD_SRC),)
        # building in the source tree
        srctree := .
else
        ifeq ($(KBUILD_SRC)/,$(dir $(CURDIR)))
                # building in a subdirectory of the source tree
                srctree := ..
        else
                srctree := $(KBUILD_SRC)
        endif
endif
objtree		:= .
src		:= $(srctree)
obj		:= $(objtree)

VPATH		:= $(srctree)$(if $(KBUILD_EXTMOD),:$(KBUILD_EXTMOD))

export srctree objtree VPATH

# SUBARCH tells the usermode build what the underlying arch is.  That is set
# first, and if a usermode build is happening, the "ARCH=um" on the command
# line overrides the setting of ARCH below.  If a native build is happening,
# then ARCH is assigned, getting whatever value it gets normally, and
# SUBARCH is subsequently ignored.

SUBARCH := $(shell uname -m | sed -e s/i.86/x86/ -e s/x86_64/x86/ \
				  -e s/sun4u/sparc64/ \
				  -e s/arm.*/arm/ -e s/sa110/arm/ \
				  -e s/s390x/s390/ -e s/parisc64/parisc/ \
				  -e s/ppc.*/powerpc/ -e s/mips.*/mips/ \
				  -e s/sh[234].*/sh/ -e s/aarch64.*/arm64/ )

# Cross compiling and selecting different set of gcc/bin-utils
# ---------------------------------------------------------------------------
ARCH		?= $(SUBARCH)
CROSS_COMPILE	?= $(CONFIG_CROSS_COMPILE:"%"=%)
CROSS_COMPILE_ARM32	?= $(CONFIG_CROSS_COMPILE_ARM32:"%"=%)

# Architecture as present in compile.h
UTS_MACHINE 	:= $(ARCH)
SRCARCH 	:= $(ARCH)

# Additional ARCH settings for x86
ifeq ($(ARCH),i386)
        SRCARCH := x86
endif
ifeq ($(ARCH),x86_64)
        SRCARCH := x86
endif

# Additional ARCH settings for sparc
ifeq ($(ARCH),sparc32)
       SRCARCH := sparc
endif
ifeq ($(ARCH),sparc64)
       SRCARCH := sparc
endif

# Additional ARCH settings for sh
ifeq ($(ARCH),sh64)
       SRCARCH := sh
endif

# Additional ARCH settings for tile
ifeq ($(ARCH),tilepro)
       SRCARCH := tile
endif
ifeq ($(ARCH),tilegx)
       SRCARCH := tile
endif

# Where to locate arch specific headers
hdr-arch  := $(SRCARCH)

KCONFIG_CONFIG	?= .config
export KCONFIG_CONFIG

# SHELL used by kbuild
CONFIG_SHELL := $(shell if [ -x "$$BASH" ]; then echo $$BASH; \
	  else if [ -x /bin/bash ]; then echo /bin/bash; \
	  else echo sh; fi ; fi)

HOSTCC       = gcc
HOSTCXX      = g++
HOSTCFLAGS   := -Wall -Wmissing-prototypes -Wstrict-prototypes -O2 -fomit-frame-pointer -std=gnu89
HOSTCXXFLAGS = -O2

# Decide whether to build built-in, modular, or both.
KBUILD_MODULES :=
KBUILD_BUILTIN := 1

ifeq ($(MAKECMDGOALS),modules)
  KBUILD_BUILTIN := $(if $(CONFIG_MODVERSIONS),1)
endif

ifneq ($(filter all _all modules,$(MAKECMDGOALS)),)
  KBUILD_MODULES := 1
endif

ifeq ($(MAKECMDGOALS),)
  KBUILD_MODULES := 1
endif

export KBUILD_MODULES KBUILD_BUILTIN
export KBUILD_CHECKSRC KBUILD_SRC KBUILD_EXTMOD

# We need some generic definitions (do not try to remake the file).
scripts/Kbuild.include: ;
include scripts/Kbuild.include

# Determine compiler name early for conditional assignments
cc-name := $(shell $(CONFIG_SHELL) $(srctree)/scripts/cc-version.sh $(CC) | cut -d' ' -f1)

# Make variables (CC, etc...)
AS		= $(CROSS_COMPILE)as
LD		= $(CROSS_COMPILE)ld
LDGOLD		= $(CROSS_COMPILE)ld.gold
CC		= $(CROSS_COMPILE)gcc
CPP		= $(CC) -E
AR		= $(CROSS_COMPILE)ar
NM		= $(CROSS_COMPILE)nm
STRIP		= $(CROSS_COMPILE)strip
OBJCOPY		= $(CROSS_COMPILE)objcopy
OBJDUMP		= $(CROSS_COMPILE)objdump
AWK		= awk
GENKSYMS	= scripts/genksyms/genksyms
INSTALLKERNEL  := installkernel
DEPMOD		= /sbin/depmod
PERL		= perl
PYTHON		= python
CHECK		= sparse

CHECKFLAGS     := -D__linux__ -Dlinux -D__STDC__ -Dunix -D__unix__ \
		  -Wbitwise -Wno-return-void $(CF)
NOSTDINC_FLAGS  =
CFLAGS_MODULE   =
AFLAGS_MODULE   =
LDFLAGS_MODULE  =
CFLAGS_KERNEL	=
AFLAGS_KERNEL	=
LDFLAGS_vmlinux =

# Use USERINCLUDE when you must reference the UAPI directories only.
USERINCLUDE    := \
		-I$(srctree)/arch/$(hdr-arch)/include/uapi \
		-I$(objtree)/arch/$(hdr-arch)/include/generated/uapi \
		-I$(srctree)/include/uapi \
		-I$(objtree)/include/generated/uapi \
                -include $(srctree)/include/linux/kconfig.h

# Use LINUXINCLUDE when you must reference the include/ directory.
LINUXINCLUDE    := \
		-I$(srctree)/arch/$(hdr-arch)/include \
		-I$(objtree)/arch/$(hdr-arch)/include/generated/uapi \
		-I$(objtree)/arch/$(hdr-arch)/include/generated \
		$(if $(KBUILD_SRC), -I$(srctree)/include) \
		-I$(objtree)/include

LINUXINCLUDE	+= $(filter-out $(LINUXINCLUDE),$(USERINCLUDE))

KBUILD_AFLAGS   := -D__ASSEMBLY__
KBUILD_CFLAGS   := -Wall -Wundef -Wstrict-prototypes -Wno-trigraphs \
		   -fno-strict-aliasing -fcommon -fshort-wchar \
		   -Werror-implicit-function-declaration -w \
		   -Wno-format-security -Wno-error=format \
		   -std=gnu89
KBUILD_CPPFLAGS := -D__KERNEL__
KBUILD_AFLAGS_KERNEL :=
KBUILD_CFLAGS_KERNEL :=
KBUILD_AFLAGS_MODULE  := -DMODULE
KBUILD_CFLAGS_MODULE  := -DMODULE
KBUILD_LDFLAGS_MODULE := -T $(srctree)/scripts/module-common.lds
GCC_PLUGINS_CFLAGS :=
CLANG_FLAGS :=

# OPPO Vendor Flags Setup
ifeq ($(OPPO_HIGH_TEMP_VERSION),true)
KBUILD_CFLAGS += -DCONFIG_HIGH_TEMP_VERSION
KBUILD_CPPFLAGS += -DCONFIG_HIGH_TEMP_VERSION
endif

KBUILD_CFLAGS +=   -DVENDOR_EDIT
KBUILD_CPPFLAGS += -DVENDOR_EDIT
CFLAGS_KERNEL +=   -DVENDOR_EDIT
CFLAGS_MODULE +=   -DVENDOR_EDIT

ifneq ($(TARGET_BUILD_VARIANT), user)
KBUILD_CFLAGS   += -DOPPO_TARGET_BUILD_DAILY
KBUILD_CPPFLAGS += -DOPPO_TARGET_BUILD_DAILY
CFLAGS_KERNEL   += -DOPPO_TARGET_BUILD_DAILY
CFLAGS_MODULE   += -DOPPO_TARGET_BUILD_DAILY
endif

ifeq ($(OBSOLETE_KEEP_ADB_SECURE),1)
KBUILD_CFLAGS += -DENABLE_LONG_PWKDUMP
endif

$(info @@@@@@@@@@@ OPPO_BUILD_CUSTOMIZE is $(OPPO_BUILD_CUSTOMIZE))
KBUILD_CFLAGS += -DMOUNT_EXSTORAGE_IF
KBUILD_CPPFLAGS += -DMOUNT_EXSTORAGE_IF
CFLAGS_KERNEL += -DMOUNT_EXSTORAGE_IF
CFLAGS_MODULE += -DMOUNT_EXSTORAGE_IF

# Read KERNELRELEASE from include/config/kernel.release (if it exists)
KERNELRELEASE = $(shell cat include/config/kernel.release 2> /dev/null)
KERNELVERSION = $(VERSION)$(if $(PATCHLEVEL),.$(PATCHLEVEL)$(if $(SUBLEVEL),.$(SUBLEVEL)))$(EXTRAVERSION)

export VERSION PATCHLEVEL SUBLEVEL KERNELRELEASE KERNELVERSION
export ARCH SRCARCH CONFIG_SHELL HOSTCC HOSTCFLAGS CROSS_COMPILE AS LD CC
export CPP AR NM STRIP OBJCOPY OBJDUMP
export MAKE AWK GENKSYMS INSTALLKERNEL PERL PYTHON UTS_MACHINE
export HOSTCXX HOSTCXXFLAGS LDFLAGS_MODULE CHECK CHECKFLAGS

export KBUILD_CPPFLAGS NOSTDINC_FLAGS LINUXINCLUDE OBJCOPYFLAGS LDFLAGS
export KBUILD_CFLAGS CFLAGS_KERNEL CFLAGS_MODULE
export CFLAGS_KASAN CFLAGS_KASAN_NOSANITIZE CFLAGS_UBSAN
export KBUILD_AFLAGS AFLAGS_KERNEL AFLAGS_MODULE
export KBUILD_AFLAGS_MODULE KBUILD_CFLAGS_MODULE KBUILD_LDFLAGS_MODULE
export KBUILD_AFLAGS_KERNEL KBUILD_CFLAGS_KERNEL
export KBUILD_ARFLAGS

# Out of tree modules setup
export MODVERDIR := $(if $(KBUILD_EXTMOD),$(firstword $(KBUILD_EXTMOD))/).tmp_versions

# Files to ignore in find statements
export RCS_FIND_IGNORE := \( -name SCCS -o -name BitKeeper -o -name .svn -o    \
			  -name CVS -o -name .pc -o -name .hg -o -name .git \) \
			  -prune -o
export RCS_TAR_IGNORE := --exclude SCCS --exclude BitKeeper --exclude .svn \
			 --exclude CVS --exclude .pc --exclude .hg --exclude .git

# Basic helpers built in scripts/
PHONY += scripts_basic
scripts_basic:
	$(Q)$(MAKE) $(build)=scripts/basic
	$(Q)rm -f .tmp_quiet_recordmcount

scripts/basic/%: scripts_basic ;

PHONY += outputmakefile
outputmakefile:
ifneq ($(KBUILD_SRC),)
	$(Q)ln -fsn $(srctree) source
	$(Q)$(CONFIG_SHELL) $(srctree)/scripts/mkmakefile \
	    $(srctree) $(objtree) $(VERSION) $(PATCHLEVEL)
endif

PHONY += asm-generic
asm-generic:
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.asm-generic \
	            src=asm obj=arch/$(SRCARCH)/include/generated/asm
	$(Q)$(MAKE) -f $(srctree)/scripts/Makefile.asm-generic \
	            src=uapi/asm obj=arch/$(SRCARCH)/include/generated/uapi/asm

version_h := include/generated/uapi/linux/version.h
old_version_h := include/linux/version.h

no-dot-config-targets := clean mrproper distclean \
			 cscope gtags TAGS tags help% %docs check% coccicheck \
			 $(version_h) headers_% archheaders archscripts \
			 kernelversion %src-pkg

config-targets := 0
mixed-targets  := 0
dot-config     := 1

ifneq ($(filter $(no-dot-config-targets), $(MAKECMDGOALS)),)
	ifeq ($(filter-out $(no-dot-config-targets), $(MAKECMDGOALS)),)
		dot-config := 0
	endif
endif

ifeq ($(KBUILD_EXTMOD),)
        ifneq ($(filter config %config,$(MAKECMDGOALS)),)
                config-targets := 1
                ifneq ($(words $(MAKECMDGOALS)),1)
                        mixed-targets := 1
                endif
        endif
endif

ifneq ($(filter install,$(MAKECMDGOALS)),)
        ifneq ($(filter modules_install,$(MAKECMDGOALS)),)
	        mixed-targets := 1
        endif
endif

# Clang Toolchain Target Handling
ifeq ($(cc-name),clang)
ifneq ($(CROSS_COMPILE),)
CLANG_TRIPLE	?= $(CROSS_COMPILE)
CLANG_FLAGS	+= --target=$(notdir $(CLANG_TRIPLE:%-=%))
GCC_TOOLCHAIN_DIR := $(dir $(shell which $(CROSS_COMPILE)elfedit 2>/dev/null || echo /dev/null))
ifneq ($(GCC_TOOLCHAIN_DIR),/dev/)
CLANG_FLAGS	+= --prefix=$(GCC_TOOLCHAIN_DIR)
GCC_TOOLCHAIN	:= $(realpath $(GCC_TOOLCHAIN_DIR)/..)
endif
endif
ifneq ($(GCC_TOOLCHAIN),)
CLANG_FLAGS	+= --gcc-toolchain=$(GCC_TOOLCHAIN)
endif
CLANG_FLAGS	+= -no-integrated-as
CLANG_FLAGS	+= -Werror=unknown-warning-option
KBUILD_CFLAGS	+= $(CLANG_FLAGS)
KBUILD_AFLAGS	+= $(CLANG_FLAGS)
endif

ifeq ($(mixed-targets),1)
PHONY += $(MAKECMDGOALS) __build_one_by_one
$(filter-out __build_one_by_one, $(MAKECMDGOALS)): __build_one_by_one
	@:

__build_one_by_one:
	$(Q)set -e; \
	for i in $(MAKECMDGOALS); do \
		$(MAKE) -f $(srctree)/Makefile $$i; \
	done

else
ifeq ($(config-targets),1)
include arch/$(SRCARCH)/Makefile
export KBUILD_DEFCONFIG KBUILD_KCONFIG

config: scripts_basic outputmakefile FORCE
	$(Q)$(MAKE) $(build)=scripts/kconfig $@

%config: scripts_basic outputmakefile FORCE
	$(Q)$(MAKE) $(build)=scripts/kconfig $@

else
ifeq ($(KBUILD_EXTMOD),)
PHONY += scripts
scripts: scripts_basic include/config/auto.conf include/config/tristate.conf \
	 asm-generic gcc-plugins
	$(Q)$(MAKE) $(build)=$(@)

init-y		:= init/
drivers-y	:= drivers/ sound/ firmware/
net-y		:= net/
libs-y		:= lib/
core-y		:= usr/
virt-y		:= virt/
endif

ifeq ($(dot-config),1)
-include include/config/auto.conf

ifeq ($(KBUILD_EXTMOD),)
-include include/config/auto.conf.cmd
$(KCONFIG_CONFIG) include/config/auto.conf.cmd: ;

include/config/%.conf: $(KCONFIG_CONFIG) include/config/auto.conf.cmd
	$(Q)$(MAKE) -f $(srctree)/Makefile silentoldconfig
else
PHONY += include/config/auto.conf

include/config/auto.conf:
	$(Q)test -e include/generated/autoconf.h -a -e $@ || (		\
	echo >&2;							\
	echo >&2 "  ERROR: Kernel configuration is invalid.";		\
	echo >&2 "         include/generated/autoconf.h or $@ are missing.";\
	echo >&2 "         Run 'make oldconfig && make prepare' on kernel src to fix it.";	\
	echo >&2 ;							\
	/bin/false)
endif
else
include/config/auto.conf: ;
endif

ifdef CONFIG_TRIM_UNUSED_KSYMS
  KBUILD_MODULES := 1
endif

all: vmlinux

KBUILD_CFLAGS	+= $(call cc-option,-fno-PIE)
KBUILD_AFLAGS	+= $(call cc-option,-fno-PIE)
CFLAGS_GCOV	:= -fprofile-arcs -ftest-coverage -fno-tree-loop-im $(call cc-disable-warning,maybe-uninitialized,)
CFLAGS_KCOV	:= $(call cc-option,-fsanitize-coverage=trace-pc,)
export CFLAGS_GCOV CFLAGS_KCOV

ifdef CONFIG_LTO_CLANG
LDFINAL_vmlinux := $(LD)
LD		:= $(LDGOLD)
LDFLAGS		+= -plugin LLVMgold.so
LLVM_AR		:= llvm-ar
LLVM_DIS	:= llvm-dis
export LLVM_AR LLVM_DIS
endif

ARCH_CPPFLAGS :=
ARCH_AFLAGS :=
ARCH_CFLAGS :=
include arch/$(SRCARCH)/Makefile

KBUILD_CFLAGS	+= $(call cc-option,-fno-delete-null-pointer-checks,)
KBUILD_CFLAGS	+= $(call cc-disable-warning,frame-address,)
KBUILD_CFLAGS	+= $(call cc-disable-warning, format-truncation)
KBUILD_CFLAGS	+= $(call cc-disable-warning, format-overflow)
KBUILD_CFLAGS	+= $(call cc-disable-warning, int-in-bool-context)
KBUILD_CFLAGS	+= $(call cc-disable-warning, address-of-packed-member)
KBUILD_CFLAGS	+= $(call cc-disable-warning, attribute-alias)

ifdef CONFIG_LD_DEAD_CODE_DATA_ELIMINATION
KBUILD_CFLAGS	+= $(call cc-option,-ffunction-sections,)
KBUILD_CFLAGS	+= $(call cc-option,-fdata-sections,)
endif

ifdef CONFIG_LTO_CLANG
lto-clang-flags	:= -flto -fvisibility=hidden
DISABLE_LTO_CLANG := -fno-lto -fvisibility=default
export DISABLE_LTO_CLANG
endif

ifdef CONFIG_LTO
lto-flags	:= $(lto-clang-flags)
KBUILD_CFLAGS	+= $(lto-flags)
DISABLE_LTO	:= $(DISABLE_LTO_CLANG)
export DISABLE_LTO
export LDFINAL_vmlinux LDFLAGS_FINAL_vmlinux
endif

ifdef CONFIG_CFI_CLANG
cfi-clang-flags	+= -fsanitize=cfi
DISABLE_CFI_CLANG := -fno-sanitize=cfi
ifdef CONFIG_MODULES
cfi-clang-flags	+= -fsanitize-cfi-cross-dso
DISABLE_CFI_CLANG += -fno-sanitize-cfi-cross-dso
endif
KBUILD_CFLAGS	+= $(cfi-clang-flags)
export DISABLE_CFI_CLANG
endif

endif
endif
endif

