
TRGT =arm-none-eabi-

# Program names.
CC		= $(TRGT)gcc
CP		= $(TRGT)objcopy
AS		= $(TRGT)as
OD		= $(TRGT)objdump
SZ  	= $(TRGT)size
LD      = $(TRGT)ld
NM  	= $(TRGT)nm



all: example.o example.elf example.bin
example.o :
	$(AS) -g -mcpu=cortex-m4 -mthumb example.s -o example.o
example.elf :
	$(LD) -Ttext 0x08000000 example.o -o example.elf
example.bin :
	$(CP) -O binary example.elf example.bin
clean:
	rm -f *.bin *.elf *.o
flash: 
	st-flash write example.bin 0x08000000
