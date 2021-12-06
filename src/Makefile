#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Makefile to build for MSP and Host platform
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.i - Builds <FILE>.i preprocessed file
#      <FILE>.asm - Builds <FILE>.asm file
#      <FILE>.o - Builds <FILE>.o object file
#      build - Builds and links all source files
#      all - Same as build
#      clean - removes all generated files
#
# Platform Overrides:
#      CPU - ARM Cortex Architecture (cortex-m4)
#      ARCH - ARM Architecture (arm, thumb)
#      SPECS - Specs file to give the linker (nosys.specs)
#------------------------------------------------------------------------------

include sources.mk

PLATFORM=HOST
# Architecture Specific Flags
ifeq ($(PLATFORM),HOST)
	ARCH = x86_64
else ifeq ($(PLATFORM),MSP432)
	LINKER_FILE = msp432p401r.lds
	CPU = cortex-m4
	ARCH = arm
	SPECS = nosys.specs 
endif

# Platform Specific Flags
ifneq ($(PLATFORM),MSP432)
	LINKER_FILE = msp432p401.lds
endif


# Compile Defines

TARGET = c1m2

ifeq ($(PLATFORM),HOST)
	CC = gcc
	LD = ld
	CFLAGS = -Wall -Werror -g -O0 -std=c99
	LDFLAGS = -Wl,-Map=$(TARGET).map 
	SIZE = size
else ifeq ($(PLATFORM),MSP432)
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-ld
	CFLAGS = -mcpu=$(CPU) -m$(ARCH) -mthumb -march=armv7e-m -mfloat-abi=hard -mfpu=fpv4-sp-d16 --specs=$(SPECS) -Wall -Werror -g -O0 -std=c99 -MMD
	LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
	SIZE = arm-none-eabi-size
endif

OBJS = $(SOURCES:.c=.o)
ASMS = $(SOURCE:.c=.asm)
PPS  = $(SOURCE:.c=.i)
DEPS = $(SOURCE:.c=.d)

%.i: %.c
	$(CC) -c $< $(CFLAGS) -E $@

%.asm: %.c ${TARGET}.out
	$(CC) -c $< $(CFLAGS) -S $@
	objdump -Sx ${TARGET}.out

%.o : %.c
	$(CC) -c $< $(INCLUDES) $(CFLAGS) -o $@

.PHONY: compile-all
compile-all: %.o

.PHONY: build
build: all 

.PHONY: all
all: $(TARGET).out
	$(SIZE) -Atd $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(LDFLAGS) -o $@

.PHONY: clean
clean:
	rm -f $(OBJS) $(TARGET).out $(TARGET).map $(ASMS) $(PPS) $(DEPS)


