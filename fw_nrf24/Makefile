OPENCM3_PATH         ?= ../lib/libopencm3
OPENCM3_LIBRARY_PATH ?= $(OPENCM3_PATH)/lib
OPENCM3_INCLUDE_PATH ?= $(OPENCM3_PATH)/include

CC      := arm-none-eabi-gcc
OBJCOPY := arm-none-eabi-objcopy
OBJDUMP := arm-none-eabi-objdump
SIZE    := arm-none-eabi-size

CFLAGS  = -Wall -g -std=gnu11 -Os
CFLAGS += -mlittle-endian -mthumb
CFLAGS += -mcpu=cortex-m3
CFLAGS += -mfix-cortex-m3-ldrd
CFLAGS += -msoft-float
#CFLAGS += -ffunction-sections -fdata-sections -Wl,--gc-sections
CFLAGS += -Wl,-Map=main.map
CFLAGS += -Tstm32f103c8t6.ld
CFLAGS += -I$(OPENCM3_INCLUDE_PATH)
CFLAGS += -L$(OPENCM3_LIBRARY_PATH)
CFLAGS += -DSTM32F1
CFLAGS += --static -nostartfiles
CFLAGS += -I../lib/NRF24L01P/src
CFLAGS += -I../lib/NRF24L01P/hal

LDFLAGS += -Wl,--start-group -lc -lgcc -lnosys -Wl,--end-group
LDFLAGS += -lopencm3_stm32f1 -lm

vpath %.c ../lib/NRF24L01P/src
vpath %.c ../lib/NRF24L01P/hal/stm32f1/src

all: main.elf

hal_stm32f1.o: ../lib/NRF24L01P/hal/stm32f1/hal_stm32f1.c
	$(CC) $(CFLAGS) -c ../lib/NRF24L01P/hal/stm32f1/hal_stm32f1.c -o hal_stm32f1.o

nrf24l01p.o: ../lib/NRF24L01P/src/nrf24l01p.c
	$(CC) $(CFLAGS) -c ../lib/NRF24L01P/src/nrf24l01p.c -o nrf24l01p.o

main.o: main.c
	$(CC) $(CFLAGS) -c -o main.o main.c

main.elf: main.o nrf24l01p.o hal_stm32f1.o
	$(CC) $(CFLAGS) -o main.elf main.o nrf24l01p.o hal_stm32f1.o $(LDFLAGS)

	$(OBJCOPY) -O ihex $@ $(@:.elf=.hex)
	$(OBJCOPY) -O binary $@ $(@:.elf=.bin)
	$(OBJDUMP) -St $@ >$(@:.elf=.lst)
	$(SIZE) $@

flash: main.elf
	openocd -f openocd.cfg -c "program $< verify reset exit"

clean:
	rm -f *.o
	rm -f *.hex
	rm -f *.lst
	rm -f *.map
	rm -f *.elf
	rm -f *.bin

.PHONY: flash clean

