
PROJECT = blink

TOOLPREFIX = arm-none-eabi
CC = $(TOOLPREFIX)-g++
OBJCOPY = $(TOOLPREFIX)-objcopy

CMSISDEVICE = Libraries/CMSIS/CM3/DeviceSupport/ST/STM32F10x
CMSISCORE = Libraries/CMSIS/CM3/CoreSupport
COMPORT = /dev/tty.SLAB_USBtoUART
PERIPH_INC = Libraries/STM32F10x_StdPeriph_Driver/inc
PERIPH_SRC = Libraries/STM32F10x_StdPeriph_Driver/src

OBJS = src/main.o \
	src/stm32f10x_it.o \
	src/system.o \
	src/startup.o \
	src/gpio.o \
	src/systick.o \
	$(PERIPH_SRC)/misc.o \
	$(PERIPH_SRC)/stm32f10x_rcc.o \
	$(PERIPH_SRC)/stm32f10x_flash.o \
	$(PERIPH_SRC)/stm32f10x_tim.o \
	$(PERIPH_SRC)/stm32f10x_usart.o \

CPU = -mcpu=cortex-m3
MCU = $(CPU) -mthumb

CFLAG = -Xlinker --gc-sections -Wl,-Map,$(PROJECT).map $(MCU) -fno-exceptions -fno-rtti -ffunction-sections
MACRO = -DSTM32F10X_MD -DUSE_STDPERIPH_DRIVER # For stm32f100xx 
# MACRO = -DSTM32F10X_MD  # For stm32f103c8
INCLUDE = \
	-I$(CMSISDEVICE) \
	-I$(CMSISCORE) \
	-I$(PERIPH_INC) \
	-Isrc/include

all: $(PROJECT).hex

.PHONY: clean

$(PROJECT).hex: $(PROJECT).elf
	@echo 'Making $@'
	$(OBJCOPY) -O ihex blink.elf $@
	@echo 'Finished building target: $@'

$(PROJECT).elf: $(OBJS)
	@echo 'Building : $@'
	$(CC) -specs=nosys.specs -specs=nano.specs -T"stm32_flash.ld" $(INCLUDE) $(MACRO) $(CFLAG) -o $@ $(OBJS) $(LIBS)
	@echo 'Finished building target: $@'

.c.o:
	$(CC) $(CFLAG) $(INCLUDE) $(MACRO) -c -o $@ $<

dl: $(PROJECT).hex
	stm32isp -term 9600 -verbose -bootp RTS -reset DTR $(PROJECT).hex $(COMPORT) 115200

term: $(PROJECT).hex
	stm32isp -term 9600 -termonly -verbose -bootp RTS -reset DTR $(PROJECT).hex $(COMPORT) 115200

clean:
	-rm $(OBJS) blink.*