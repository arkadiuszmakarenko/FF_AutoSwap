PREFIX = m68k-amigaos-
CC = $(PREFIX)gcc
AS = $(PREFIX)as
PYTHON = python3

CFLAGS += -MMD -MF .$(@F).d 
DEPS = .*.d

CFLAGS += -nostdlib -fno-builtin -I..
CFLAGS += -Os -m68000 -Wall -Werror -std=gnu99 -msoft-float
#CFLAGS += -msmall-code
#CFLAGS += -DDEBUG
LDFLAGS += $(CFLAGS) -s
AFLAGS += $(CFLAGS) -D__ASSEMBLY__ -Wa,--register-prefix-optional
AFLAGS += -Wa,-l -Wa,--bitwise-or -Wa,--base-size-default-16
AFLAGS += -Wa,--disp-size-default-16 -Wa,--pcrel

TARGET = GobliiinsADF
VER = v0.1
NAME_PREFIX = gob

all: gob01.adf
	rm -rf $(TARGET)*
	mkdir -p $(TARGET)
	cp $< "$(TARGET)/$(NAME_PREFIX)01.adf"
	cp Orig/Gobliiins02.adf "$(TARGET)/$(NAME_PREFIX)02.adf"
	cp Orig/Gobliiins03.adf "$(TARGET)/$(NAME_PREFIX)03.adf"
	zip -r $(TARGET)-$(VER).zip $(TARGET)

gob01.adf: Orig/Gobliiins01.adf gob_autoswap
	rm -rf Gobliiins1*
	xdftool Orig/Gobliiins01.adf unpack .
	cp gob_autoswap Gobliiins1/.
	cp Echo Gobliiins1/.
	sed -i '1s/^/echo "Flash Floppy Autoswap"\nc:gob_autoswap\n/' Gobliiins1/s/startup-sequence
	rm -rf $@
	xdftool $@ pack Gobliiins1
	rm -rf Gobliiins1*

gob_autoswap: gob_autoswap_1 gob_autoswap_2
	$(PYTHON) ../shared/hunk_cat.py -o $@ $^

gob_autoswap_1: gob_autoswap_entry.o
	$(CC) -o $@ $^ $(LDFLAGS)

gob_autoswap_2: gob_autoswap.o libnix.o crc.o
	$(CC) -o $@ $^ $(LDFLAGS)

libnix.c: ../shared/libnix.c
	ln -sf $< $@

crc.c: ../shared/crc.c
	ln -sf $< $@

gob_autoswap_entry.S: ../shared/amigados_autoswap_entry.S
	ln -sf $< $@

%.o: %.S
	$(CC) -o $@ -c $< $(AFLAGS)

%.o: %.c
	$(CC) -o $@ -c $< $(CFLAGS)

clean:
	rm -rf *.o *.adf gob_autoswap startup-sequence Gobliiins1* $(DEPS) $(TARGET)* gob_autoswap_* crc.c libnix.c

write: all
	sudo mount /dev/sdb1 /mnt
	sudo rm -rf /mnt/$(TARGET)
	sudo cp -r $(TARGET) /mnt/.
	echo -n "$(TARGET)/$(NAME_PREFIX)01.adf" >IMAGE_A.CFG
	sudo cp IMAGE_A.CFG /mnt/.
	rm -f IMAGE_A.CFG
	sudo umount /mnt
	sudo fatsort /dev/sdb1

.PHONY: all clean write

-include $(DEPS)
