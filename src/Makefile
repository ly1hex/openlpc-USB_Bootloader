GCC_BIN =
PROJECT = bootloader
CMSIS_PATH = ./CDL/CMSISv2p00_LPC17xx
DRIVER_PATH = ./CDL/LPC17xxLib
INCLUDE_PATHS = -I. -I./lpcusb/target -I./lpcusb/target/examples \
				-I$(DRIVER_PATH)/inc -I$(CMSIS_PATH)/inc
LINKER_SCRIPT = LPC1768.ld

LOCAL_C_SRCS = $(CMSIS_PATH)/src/core_cm3.c $(CMSIS_PATH)/src/system_LPC17xx.c \
			   $(wildcard $(CMSIS_PATH)/source/*.c) $(wildcard *.c) \
			   $(wildcard lpcusb/target/*.c) \
			   lpcusb/target/examples/msc_bot.c
OBJECTS = $(LOCAL_C_SRCS:.c=.o)

###############################################################################
CC = $(GCC_BIN)arm-none-eabi-gcc
CPP = $(GCC_BIN)arm-none-eabi-g++
CC_FLAGS = -c -fno-common -fmessage-length=0 -Wall -fno-exceptions -mcpu=cortex-m3 -mthumb -ffunction-sections -fdata-sections
ONLY_C_FLAGS = -std=gnu99
ONLY_CPP_FLAGS = -std=gnu++98
CC_SYMBOLS = -DTARGET_LPC1768 -DTOOLCHAIN_GCC_ARM \
			 -DUSB_DEVICE_ONLY -DLPC17xx -D__LPC17XX__ -DBOARD=9


AS = $(GCC_BIN)arm-none-eabi-as

LD = $(GCC_BIN)arm-none-eabi-gcc
LD_FLAGS = -mcpu=cortex-m3 -mthumb -Wl,--gc-sections,-Map=$(PROJECT).map
LD_SYS_LIBS = -lstdc++ -lsupc++ -lm -lc -lgcc

ifdef DEBUG
CC_FLAGS += -g -ggdb
else
CC_FLAGS += -Os
endif

OBJCOPY = $(GCC_BIN)arm-none-eabi-objcopy

all: $(PROJECT).bin

clean:
	rm -f $(PROJECT).bin $(PROJECT).elf $(OBJECTS) *.o

.s.o:
	$(AS)  $(CC_FLAGS) $(CC_SYMBOLS) -o $@ $<

.c.o:
	$(CC)  $(CC_FLAGS) $(CC_SYMBOLS) $(ONLY_C_FLAGS)   $(INCLUDE_PATHS) -o $@ $<

.cpp.o:
	$(CPP) $(CC_FLAGS) $(CC_SYMBOLS) $(ONLY_CPP_FLAGS) $(INCLUDE_PATHS) -o $@ $<


$(PROJECT).elf: $(OBJECTS)
	$(LD) $(LD_FLAGS) -T$(LINKER_SCRIPT) -o $@ $^ $(LD_SYS_LIBS)

$(PROJECT).bin: $(PROJECT).elf
	$(OBJCOPY) -O binary $< $@

flash: all
	@openocd -f flash.cfg

gdb: all
	@openocd -f gdb.cfg
