# zedBoot

### Upute za bootanje

Temeljito prebrisati SD karticu:

	$ sudo dd if=/dev/zero of=/dev/mmcblk0

(Uz pretpostavku da je kartica na lokaciji /dev/mmcblk0)

Stvoriti 2 partiticije (radilo je s omjerom 325 MB : ostalo):

	$ sudo fdisk /dev/mmcblk0
	$ sudo mkfs -t vfat -n ZED_BOOT /dev/mmcblk0p1
	$ sudo mkfs -t ext4 -L ROOT_FS /dev/mmcblk0p2

Jedna FAT32, jedna ext4

Na FAT32 partiticiju treba staviti potrebne datoteke za pokretanje i karticu ubaciti u ploču (radi s priloženom verzijom boot.bin-a, nova baca „No cores found”)

Konfiguracija UART veze:
 
	Port: (/dev/ttyACM0)
	Baud Rate: 115200
	Parity: none
	Bits: 8
	StopBits: 1
	Flow control: none

Spriječiti automatski boot, inače se pojavi „No init found” error.

U u-boot:

	$ $sdboot=echo Copying Linux from SD to RAM... && mmcinfo && fatload mmc 0 0x3000000 ${kernel_image} && fatload mmc 0 0x2A00000 ${devicetree_image} && fatload mmc 0 0x2000000 ${ramdisk_image} && bootm 0x3000000 0x2000000 0x2A00000

Podešavanje cross-compilera:

	$ git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
	$ ./configure --with-arch=rv64g --disable-multilib --prefix=apsolutni put do željenog direktorija u kojem će se postaviti
	$ make

U direktoriju {prefix}/bin, za prevođenje programa

	$ ./riscv64-unknown-elf-gcc main.c -o main
