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

TARGET = Monkey2-FFAS
VER = v1.5
NAME_PREFIX = Monkey2 - 

all: mi2_01.adf mi2_01_alt.adf
	rm -rf $(TARGET)*
	mkdir -p $(TARGET)
	xdftool "$(TARGET)/$(NAME_PREFIX)Save.adf" format "Monkey2 Save"
	cp mi2_01.adf "$(TARGET)/$(NAME_PREFIX)01.adf"
	cp mi2_01_alt.adf "$(NAME_PREFIX)01.adf"
	zip "$(TARGET)/monkey2_alt_boot.zip" "$(NAME_PREFIX)01.adf"
	rm -f "$(NAME_PREFIX)01.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk2.adf "$(TARGET)/$(NAME_PREFIX)02.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk3.adf "$(TARGET)/$(NAME_PREFIX)03.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk4.adf "$(TARGET)/$(NAME_PREFIX)04.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk5.adf "$(TARGET)/$(NAME_PREFIX)05.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk6.adf "$(TARGET)/$(NAME_PREFIX)06.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk7.adf "$(TARGET)/$(NAME_PREFIX)07.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk8.adf "$(TARGET)/$(NAME_PREFIX)08.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk9.adf "$(TARGET)/$(NAME_PREFIX)09.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk10.adf "$(TARGET)/$(NAME_PREFIX)10.adf"
	cp MonkeyIsland2Kixx/MonkeyIsland2Disk11.adf "$(TARGET)/$(NAME_PREFIX)11.adf"
	cp 00-README.txt $(TARGET)/.
	zip -r $(TARGET)-$(VER).zip $(TARGET)

mi2_01_alt.adf: mi2_01.adf monkey2_alt.zip
	xdftool $< unpack .
	unzip -o monkey2_alt.zip -d "Monkey2 Disk 1"
	rm -rf $@
	xdftool $@ pack "Monkey2 Disk 1"
	rm -rf "Monkey2 Disk 1"*

mi2_01.adf: MonkeyIsland2Kixx/MonkeyIsland2Disk1.adf mi2_autoswap
	rm -rf "Monkey2 Disk 1"*
	xdftool MonkeyIsland2Kixx/MonkeyIsland2Disk1.adf unpack .
	cp mi2_autoswap "Monkey2 Disk 1/c/."
	sed -e 's/stack/echo "Monkey2 FlashFloppy AutoSwap $(VER)"\nc:mi2_autoswap\nstack/' -i "Monkey2 Disk 1/s/startup-sequence"
	$(PYTHON) ../pack/pack_exe.py "Monkey2 Disk 1/monkey2" monkey2.pack
	mv monkey2.pack "Monkey2 Disk 1/monkey2"
	rm -rf $@
	xdftool $@ pack "Monkey2 Disk 1"
	rm -rf "Monkey2 Disk 1"*

mi2_autoswap: mi2_autoswap_1 mi2_autoswap_2
	$(PYTHON) ../shared/hunk_cat.py -o $@ $^

mi2_autoswap_1: mi2_autoswap_entry.o
	$(CC) -o $@ $^ $(LDFLAGS)

mi2_autoswap_2: mi2_autoswap.o libnix.o crc.o
	$(CC) -o $@ $^ $(LDFLAGS)

libnix.c: ../shared/libnix.c
	ln -sf $< $@

crc.c: ../shared/crc.c
	ln -sf $< $@

mi2_autoswap_entry.S: ../shared/amigados_autoswap_entry.S
	ln -sf $< $@

%.o: %.S
	$(CC) -o $@ -c $< $(AFLAGS)

%.o: %.c
	$(CC) -o $@ -c $< $(CFLAGS)

clean:
	rm -rf *.o *.adf mi2_autoswap startup-sequence $(DEPS) $(TARGET)* mi2_autoswap_* crc.c libnix.c

write: all
	sudo mount /dev/sdd1 /mnt
	sudo rm -rf /mnt/$(TARGET)
	sudo cp -r $(TARGET) /mnt/.
	echo -n "$(TARGET)/$(NAME_PREFIX)01.adf" >IMAGE_A.CFG
	sudo cp IMAGE_A.CFG /mnt/.
	rm -f IMAGE_A.CFG
	sudo umount /mnt
	sudo fatsort /dev/sdd1

.PHONY: all clean write

-include $(DEPS)
