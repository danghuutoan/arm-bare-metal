

TARGET        = $(OBJDIR)/SPI_Nucleo

MCU  		   = cortex-m4

OBJDIR = Output
LSTDIR = Lst

#
# Create output directories
#
$(shell mkdir -p $(OBJDIR)/$(LSTDIR) 2>/dev/null)

#
# Linker Scripts.
#
LDSCRIPT =example.ld
#
# ASM source files here
#
ASRC+= example.s

TRGT =arm-none-eabi-

# Program names.
CC		= $(TRGT)gcc
CP		= $(TRGT)objcopy
AS		= $(TRGT)gcc -x assembler-with-cpp
OD		= $(TRGT)objdump
SZ  	= $(TRGT)size
NM  	= $(TRGT)nm
OPENOCD = openocd
DOXYGEN	= doxygen

# Compile Optimization level, can be [0, 1, 2, 3, s]. 
#     0 = turn off optimization. s = optimize for size.
# 
OPT = 3

################################################################################


#
INCDIR  = $(patsubst %,-I%,$(sort $(DINCDIR) $(UINCDIR)))
LIBDIR  = $(patsubst %,-L%,$(sort $(DLIBDIR) $(ULIBDIR)))

#
# Add -D prefix to defines.
#
CDEFS=$(patsubst %,-D%,$(sort $(DCDEFS) $(UCDEFS)))
ADEFS=$(patsubst %,-D%,$(sort $(DADEFS) $(UADEFS)))

#
# Combine Libraries. 
#
LIBS   +=$(DLIBS)
LIBS   +=$(ULIBS)

#
# MCU Flags
#
MCUFLAGS+=-mthumb
MCUFLAGS+=-mcpu=$(MCU)


#---------------- Assembler Options ----------------
#  -Wa,...:   tell GCC to pass this to the assembler
#  -adhlns:   create listing
#----------------------------------------------------

#
# Assembly Flags
#
ASFLAGS+=$(MCUFLAGS) 

LDFLAGS+=$(MCUFLAGS)
#LDFLAGS+=-nostartfiles
LDFLAGS+=-T$(LDSCRIPT)
LDFLAGS+=-Wl,-Map=$(TARGET).map,--cref,--gc-sections

OBJS	+= $(addprefix $(OBJDIR)/,$(addsuffix .o,$(basename $(SRC))))
OBJS	+= $(addprefix $(OBJDIR)/,$(addsuffix .o,$(basename $(ASRC))))

# Make all
all: build 

# Build process
build: elf hex  bin

elf: $(TARGET).elf
hex: $(TARGET).hex
bin: $(TARGET).bin



# Assemble: create object files from assembler source files
$(OBJDIR)/%.o : %.s
	@echo
	@echo Assembling: $<
	$(AS) -c $(ASFLAGS) $< -o $@
	
# Link: create ELF output file from object files
.SECONDARY: $(TARGET).elf
.PRECIOUS:  $(OBJS)
$(TARGET).elf: $(OBJS)
	@echo
	@echo Linking: $@
	$(CC) $^ $(LDFLAGS) $(LIBS) -o $@

# Create final output files (.hex, .eep) from ELF output file.
%.hex: %.elf
	@echo
	@echo Creating hex file: $@
	$(CP) -O ihex $< $@

# Create bin file :
%.bin: %.elf
	@echo
	@echo Creating bin file: $@
	$(CP) -O binary $< $@

# Flash hex file to the board.
flash: $(TARGET).hex
	@echo Flashing $(TARGET)
	st-flash write $(TARGET).bin 0x08000000

clean:
	@echo Cleaning project:
	rm -rf $(OBJDIR)


# Listing of phony targets
.PHONY: all build flash clean \
        elf
################################################################################