
#nombre del proyecto
APP=mi_suma

#toolchain
CHAIN=arm-none-eabi

# falgs del compilador
CFLAGS=-std=gnu99 -Wall -mcpu=cortex-a8

#estructura de carpetas del proyecto
OBJ=obj/
BIN=bin/
INC=inc/
SRC=src/
LST=lst/

#linker script
LD=td3_memmap.ld

#placa a emular en la maquina virtual de QEMU
BOARD=realview-pb-a8
#otras placas posibles a emular descomentar la que se quiera usar
#BOARD=mcimx6ul-evk
#BOARD=vexpress-a15
# para ver el listado completo usar el comando:
# qemu-system-arm -M help | grep Cortex-A
# se puede usar cualquiera lo que cambia es el vector de reset en la emualcion
# tener en cuenta esto para modificar el linker script acorde

#falgs para el QEMU
PORT=12345
VMFLGS= -M $(BOARD) -no-reboot -nographic -monitor telnet:127.0.0.1:$(PORT),server,nowait

# motor de maquina virtual a utilizar
VME=qemu-system-arm

# binario del kernel a ejecutar en el QEMU
BINF=$(BIN)$(APP).bin

#construccion del proyecto
all: $(BINF) $(OBJ)$(APP).elf

$(BINF): $(OBJ)$(APP).elf
	$(CHAIN)-objcopy -O binary $< $@


$(OBJ)$(APP).elf: $(OBJ)suma_abi.o $(OBJ)suma_c.o
	@echo "Linkeando ... "
	mkdir -p obj
	mkdir -p lst

	$(CHAIN)-ld -T td3_memmap.ld $(OBJ)*.o -o $(OBJ)$(APP).elf -Map $(LST)$(APP).map
	@echo "Linkeo finalizado!!"
	@echo ""
	@echo "Generando archivos de información: mapa de memoria y símbolos"
	readelf -a $(OBJ)mi_suma.elf > $(LST)mi_suma_elf.txt
	$(CHAIN)-objdump -D $(OBJ)$(APP).elf > $(LST)$(APP).lst

$(OBJ)suma_abi.o: $(SRC)suma_abi.s
	@echo ""
	mkdir -p bin
	mkdir -p obj
	mkdir -p lst
	@echo "Ensamblando suma_abi.s ..."
	$(CHAIN)-as $(SRC)suma_abi.s -o $(OBJ)suma_abi.o -a > $(LST)suma_abi.lst

$(OBJ)suma_c.o: $(SRC)suma_c.c
	@echo ""
	mkdir -p bin
	mkdir -p obj
	mkdir -p lst
	@echo "Compilando suma_c.c ..."
	$(CHAIN)-gcc -O3 $(CFLAGS) -c $(SRC)suma_c.c -o $(OBJ)suma_c.o

clean:
	rm -rf $(OBJ)*.o
	rm -rf $(OBJ)*.elf
	rm -rf $(BIN)*.bin
	rm -rf $(LST)*.lst
	rm -rf $(LST)*.txt
	rm -rf $(LST)*.map

run:
	$(VME) $(VMFLGS) -kernel $(BINF)
debug:
	$(VME) $(VMFLGS) -s -S -kernel $(BINF)
connect:
	telnet localhost $(PORT)

