#----------------------------------------------------------#
THIS_LIBNAME := ved

API := 0
REV := 1
VER := 1

THISDIR := $(shell realpath ../..)

THIS_LIBVERS  :=  $(API).$(REV)

THIS_HEADER   :=  lib$(THIS_LIBNAME).h
THIS_LIBSRC   :=  lib$(THIS_LIBNAME).c
THIS_LIB      :=  lib$(THIS_LIBNAME)-$(THIS_LIBVERS)

CC            := gcc
CC_STD        := -std=c11

SYSKERNEL     := $(shell uname -s)
SYSARCH       := $(shell uname -m)
SYS           := $(SYSKERNEL)-$(SYSARCH)

SYSDIR     := $(THISDIR)/sys
SYSLIBDIR   = $(SYSDIR)/lib
SYSBINDIR   = $(SYSDIR)/bin
SYSINCDIR   = $(SYSDIR)/include
SYSDATADIR := $(SYSDIR)/data
SYSTMPDIR  := $(SYSDIR)/tmp

#----------------------------------------------------------#
LIBNAME        := $(THIS_LIBNAME)-$(THIS_LIBVERS)

THIS_SHAREDLIB := $(SYSLIBDIR)/$(THIS_LIB).so
THIS_STATICLIB := $(SYSLIBDIR)/$(THIS_LIB).a

BASE_FLAGS  := -g -O2 -march=native -fvisibility=hidden -fdollars-in-identifiers

DEBUG_FLAGS := -Wextra -Wno-override-init -Werror -Wshadow -Wall -Wunused-function -Wunused-macros
# -fsanitize=undefined
# -Wpedantic

SHARED_CFLAGS := -shared -fPIC
STATIC_CFLAGS := -static

FLAGS         := $(BASE_FLAGS)

CHECK_PTR_BOUNDS_FLAGS := -mmpx -fcheck-pointer-bounds

CHECK_PTR_BOUNDS := 0

ifneq ($(CHECK_PTR_BOUNDS), 0)
  FLAGS += $(CHECK_PTR_BOUNDS_FLAGS)
endif

ifeq (-$(CC), -gcc)
  FLAGS += -Wimplicit-fallthrough=0
endif

DEBUG := 0

ifneq ($(DEBUG), 0)
  FLAGS += $(DEBUG_FLAGS)
endif
#----------------------------------------------------------#
# libved
LIBVED_NAME := libved
LIBVED_VERS := 0.1
LIBVED_SRCDIR := $(THISDIR)

LIBVED_SRC    = $(LIBVED_SRCDIR)/$(LIBVED_NAME).c
LIBVED_HEADER = $(LIBVED_SRCDIR)/$(LIBVED_NAME).h
LIBVED_HEADER_EXT = $(LIBVED_SRCDIR)/$(LIBVED_NAME)+.h

LIBVED_SYSHEADER = $(SYSINCDIR)/$(LIBVED_NAME).h
LIBVED_SYSHEADER_EXT = $(SYSINCDIR)/$(LIBVED_NAME)+.h

LIBVED = $(LIBVED_NAME)-$(LIBVED_VERS)
LIBVED_SHARED_OBJ = $(SYSLIBDIR)/$(LIBVED).so
LIBVED_SHARED_OBJ_LINK = $(SYSLIBDIR)/$(LIBVED_NAME).so

LIBVED_STATIC_OBJ = $(SYSLIBDIR)/$(LIBVED).a
LIBVED_STATIC_OBJ_LINK = $(SYSLIBDIR)/$(LIBVED_NAME).a

LIBVED_FLAGS  = $(FLAGS)

TABWIDTH := 8
UNDO_NUM_ENTRIES := 40
RLINE_HISTORY_NUM_ENTRIES := 20
CLEAR_BLANKLINES := 1
TAB_ON_INSERT_MODE_INDENTS := 0
CARRIAGE_RETURN_ON_NORMAL_IS_LIKE_INSERT_MODE := 1
SPACE_ON_NORMAL_IS_LIKE_INSERT_MODE := 1
SMALL_E_ON_NORMAL_GOES_INSERT_MODE := 1
BACKSPACE_ON_FIRST_IDX_REMOVE_TRAILING_SPACES := 1
BACKSPACE_ON_NORMAL_IS_LIKE_INSERT_MODE := 1

READ_FROM_SHELL = 1
SPELL_DICTIONARY := $(SYSDATADIR)/spell/spell.txt
SPELL_DICTIONARY_NUM_ENTRIES := 10000

VED_FLAGS = -lm

LIBVED_FLAGS +=  $(VED_FLAGS)
LIBVED_FLAGS += -DENABLE_WRITING
LIBVED_FLAGS += -DVED_DIR='"$(SYSDIR)"'
LIBVED_FLAGS += -DVED_DATADIR='"$(SYSDATADIR)"'
LIBVED_FLAGS += -DVED_TMPDIR='"$(SYSTMPDIR)"'
LIBVED_FLAGS += -DTABWIDTH=$(TABWIDTH)
LIBVED_FLAGS += -DUNDO_NUM_ENTRIES=$(UNDO_NUM_ENTRIES)
LIBVED_FLAGS += -DRLINE_HISTORY_NUM_ENTRIES=$(RLINE_HISTORY_NUM_ENTRIES)
LIBVED_FLAGS += -DCLEAR_BLANKLINES=$(CLEAR_BLANKLINES)
LIBVED_FLAGS += -DTAB_ON_INSERT_MODE_INDENTS=$(TAB_ON_INSERT_MODE_INDENTS)
LIBVED_FLAGS += -DCARRIAGE_RETURN_ON_NORMAL_IS_LIKE_INSERT_MODE=$(CARRIAGE_RETURN_ON_NORMAL_IS_LIKE_INSERT_MODE)
LIBVED_FLAGS += -DSPACE_ON_NORMAL_IS_LIKE_INSERT_MODE=$(SPACE_ON_NORMAL_IS_LIKE_INSERT_MODE)
LIBVED_FLAGS += -DSMALL_E_ON_NORMAL_GOES_INSERT_MODE=$(SMALL_E_ON_NORMAL_GOES_INSERT_MODE)
LIBVED_FLAGS += -DBACKSPACE_ON_FIRST_IDX_REMOVE_TRAILING_SPACES=$(BACKSPACE_ON_FIRST_IDX_REMOVE_TRAILING_SPACES)
LIBVED_FLAGS += -DREAD_FROM_SHELL=$(READ_FROM_SHELL)

ifeq (-$(SYSKERNEL), -Linux)
  LIBVED_FLAGS += -DSYS_IS_LINUX
endif

LIBVED_FLAGS += -DSYS_NAME='"$(SYSKERNEL)"'

LIBVED_CFLAGS += -I. -I$(SYSINCDIR) $(LIBVED_FLAGS)

libved-header: $(LIBVED_SYSHEADER) $(LIBVED_SYSHEADER_EXT)
$(LIBVED_SYSHEADER):
	@$(CP) $(LIBVED_HEADER) $(SYSINCDIR)
$(LIBVED_SYSHEADER_EXT):
	@$(CP) $(LIBVED_HEADER_EXT) $(SYSINCDIR)

libved-static: prereq libved-header $(LIBVED_STATIC_OBJ)
$(LIBVED_STATIC_OBJ):
	$(CC) -x c $(LIBVED_SRC) $(LIBVED_FLAGS) $(STATIC_CFLAGS) -c -o $(LIBVED).o
	@$(AR) $(LIBVED_STATIC_OBJ) $(LIBVED).o
	@$(RM) $(LIBVED).o
	@$(LN_S) -vf $(LIBVED_STATIC_OBJ) $(LIBVED_STATIC_OBJ_LINK)

libved-shared: prereq libved-header $(LIBVED_SHARED_OBJ)
$(LIBVED_SHARED_OBJ):
	$(CC) -x c $(LIBVED_SRC) $(LIBVED_CFLAGS) $(SHARED_CFLAGS) -o $(LIBVED_SHARED_OBJ)
	@$(LN_S) -vf $(LIBVED_SHARED_OBJ) $(LIBVED_SHARED_OBJ_LINK)

clean_libved: clean_libved_shared clean_libved_static clean_libved_header

clean_libved_shared: clean_libved_header
	@$(TEST) ! -f $(LIBVED_SHARED_OBJ) || $(RM) -v $(LIBVED_SHARED_OBJ)
	@$(TEST) ! -L $(LIBVED_SHARED_OBJ_LINK) || $(RM) -v $(LIBVED_SHARED_OBJ_LINK)

clean_libved_static: clean_libved_header clean_libved_static_obj
clean_libved_static_obj:
	@$(TEST) ! -f $(LIBVED_STATIC_OBJ) || $(RM) -v $(LIBVED_STATIC_OBJ)
	@$(TEST) ! -L $(LIBVED_STATIC_OBJ_LINK) || $(RM) -v $(LIBVED_STATIC_OBJ_LINK)

clean_libved_header:
	@$(TEST) ! -f $(LIBVED_SYSHEADER) || $(RM) $(LIBVED_SYSHEADER)
	@$(TEST) ! -f $(LIBVED_SYSHEADER_EXT) || $(RM) $(LIBVED_SYSHEADER_EXT)
#----------------------------------------------------------#

WORD_LEXICON_FILE :=

ifdef WORD_LEXICON_FILE
  FLAGS += -DWORD_LEXICON_FILE='"$(WORD_LEXICON_FILE)"'
endif

#----------------------------------------------------------#
APPNAME := mutt_pager

THIS_APPSRC  := $(APPNAME).c
THIS_APPVERS := $(VER)$(API)$(REV)

APPSHARED    := $(APPNAME)-$(THIS_APPVERS)_shared
APPSTATIC    := $(APPNAME)-$(THIS_APPVERS)_static
SYSAPPSHARED := $(SYSBINDIR)/$(APPSHARED)
SYSAPPSTATIC := $(SYSBINDIR)/$(APPSTATIC)

#----------------------------------------------------------#
REQOBJS   =   $(LIBVED_SRCDIR)/libved+.c
REQOBJS   +=  $(LIBVED_SRCDIR)/lib/argparse/argparse.c
REQOBJS   +=  $(LIBVED_SRCDIR)/lib/map/int_map.c
REQOBJS   +=  $(LIBVED_SRCDIR)/lib/i/i.c

ifneq ($(HAS_JSON), 0)
  REQOBJS += $(LIBVED_SRCDIR)/lib/json/json.c
endif

APPFLAGS  =   $(REQOBJS)
APPFLAGS  +=  -I$(SYSINCDIR) -L$(SYSLIBDIR) -lved -lm

APPLICATION_FLAGS :=

ifdef APPLICATION_FLAGS
  APPFLAGS += $(APPLICATION_FLAGS)
endif

APPFLAGS += $(FLAGS)

ifeq (-$(SYSKERNEL), -Linux)
  APPFLAGS += -DSYS_IS_LINUX
endif

APPFLAGS += -DHAS_LOCAL_EXTENSIONS
APPFLAGS += -DHAS_MOONPHASE
APPFLAGS += -DSYS_NAME='"$(SYSKERNEL)"'
APPFLAGS += -DHAS_SHELL_COMMANDS
APPFLAGS += -DHAS_REGEXP
APPFLAGS += -DHAS_SHELL
APPFLAGS += -DHAS_HISTORY
APPFLAGS += -DHAS_SPELL
APPFLAGS += -DHAS_JSON
APPFLAGS += -DHAS_EXPR
APPFLAGS += -DHAS_USER_EXTENSIONS
APPFLAGS += -DSPELL_DICTIONARY='"$(SPELL_DICTIONARY)"'
APPFLAGS += -DSPELL_DICTIONARY_NUM_ENTRIES=$(SPELL_DICTIONARY_NUM_ENTRIES)
APPFLAGS += -DVED_DIR='"$(SYSDIR)"'
APPFLAGS += -DVED_DATADIR='"$(SYSDATADIR)"'
APPFLAGS += -DVED_TMPDIR='"$(SYSTMPDIR)"'

app-all: app-static app-shared

clean_app: clean_app_static clean_app_shared
clean_app_shared:
	@$(TEST) ! -f $(SYSAPPSHARED) || $(RM) $(SYSAPPSHARED)
clean_veda_static:
	@$(TEST) ! -f $(SYSAPPSTATIC) || $(RM) $(SYSAPPSTATIC)

app-shared: Env libved-shared
	$(CC) -x c $(THIS_APPSRC) $(APPFLAGS) -o $(APPSHARED)
	@$(INSTALL) -v $(APPSHARED) $(SYSBINDIR)
	@$(RM) $(APPSHARED)

app-static: Env libved-static
	$(CC) -x c $(THIS_APPSRC) $(APPFLAGS) $(STATIC_CFLAGS) -o $(APPSTATIC)
	@$(INSTALL) -v $(APPSTATIC) $(SYSBINDIR)
	@$(RM) $(APPSTATIC)

app-shared-preproc:
	$(CC) -x c $(THIS_APPSRC) $(APPFLAGS) -E

#----------------------------------------------------------#
LD_LIBRARY_PATH := $(SYSLIBDIR)
#----------------------------------------------------------#
AR = ar rs
TEST = test
RM = rm -f
RM_R = rm -r -f
RMDIR = rmdir
MKDIR = mkdir
MKDIR_P = $(MKDIR) -p
CP = cp
CP_R = $(CP) -r
LN_S = ln -s
INSTALL = install
MAKE = make
VALGRIND = valgrind
VALGRIND_ARGS = --leak-check=full --show-leak-kinds=all -v --track-origins=yes
GDB = gdb
GDB_ARGS = --quiet -ex "set logging file /tmp/gdb.txt" -ex "set logging on" -ex run --args
#----------------------------------------------------------#

Env: makeenv checkenv
makeenv:
	@$(TEST) -d $(SYSBINDIR)  || $(MKDIR) $(SYSBINDIR)
	@$(TEST) -d $(SYSDATADIR) || $(MKDIR_P) --mode=0700 $(SYSDATADIR)
	@$(TEST) -d $(SYSTMPDIR)  || $(MKDIR_P) --mode=0700 $(SYSTMPDIR)

checkenv:
	@$(TEST) -w $(SYSBINDIR)  || exit 1
	@$(TEST) -w $(SYSDATADIR) || exit 1
	@$(TEST) -w $(SYSTMPDIR)  || exit 1

#----------------------------------------------------------#

VERBOSE := 0
VALGRIND_V :=
ifneq ($(VERBOSE), 0)
  VALGRIND_V = -v
endif

# We want to pass the arguments to the below targets
ARGS = $(filter-out $@,$(MAKECMDGOALS))
# but make interpert command line arguments either as targets or as variables.
# This is a do nothing target, which is executed when there is no matching target.
%:
	@true
# But this is dangerous as silently make will execute, even mispelled targets
# yet legitimate.
# https://stackoverflow.com/a/45003119

# So taking with caution for the sake of simplicity, but is better to use a shell
# script, as described to the above answer to stackoverflow.

run_shared:
	@LD_LIBRARY_PATH=$(LD_LIBRARY_PATH) $(SYSAPPSHARED) $(ARGS)

run_static:
	@$(SYSAPPSTATIC) $(ARGS)

check_app_memory_leaks:
	@LD_LIBRARY_PATH=$(LD_LIBRARY_PATH) $(VALGRIND) $(VALGRIND_ARGS) $(VALGRIND_V) $(SYSAPPSHARED) $(ARGS)

debug_app_static:
	@$(GDB) $(GDB_ARGS) $(SYSAPPSTATIC) $(ARGS)

debug_app_shared:
	@LD_LIBRARY_PATH=$(LD_LIBRARY_PATH) $(GDB) $(GDB_ARGS) $(SYSAPPSHARED) $(ARGS)
