O ?= .
TMP ?= .
BL33 ?=

.phony: clean distclean

all: ${O}/u-boot.bin

clean:
	rm -f ${TMP}/fip.bin
	rm -f ${TMP}/boot_new.bin
	rm -f ${TMP}/u-boot.gxb

distclean: clean
	rm -f ${O}/u-boot.bin ${O}/u-boot.bin.sd.bin ${O}/u-boot.bin.usb.bl2 ${O}/u-boot.bin.usb.tpl

${TMP}/fip.bin: bl30.bin bl301.bin bl31.bin ${BL33}
	./fip_create --bl30 bl30.bin --bl301 bl301.bin --bl31 bl31.bin --bl33 ${BL33} ${TMP}/fip.bin

${TMP}/boot_new.bin: ${TMP}/fip.bin bl2.package
	cat bl2.package ${TMP}/fip.bin > ${TMP}/boot_new.bin

${O}/u-boot.bin: ${TMP}/boot_new.bin
	./aml_encrypt_gxb --bootsig --input ${TMP}/boot_new.bin --output ${O}/u-boot.bin
	dd if=${O}/u-boot.bin of=${TMP}/u-boot.gxbb bs=512 skip=96
	dd if=bl1.bin.hardkernel of=${O}/u-boot.bin.sd.bin conv=fsync,notrunc bs=1 count=442
	dd if=bl1.bin.hardkernel of=${O}/u-boot.bin.sd.bin conv=fsync,notrunc bs=512 skip=1 seek=1
	dd if=${TMP}/u-boot.gxbb of=${O}/u-boot.bin.sd.bin conv=fsync,notrunc bs=512 seek=97
