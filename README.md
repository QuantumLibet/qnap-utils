## qnap-utils
Utilities to unpack QNAP firmware images and QPKG files

### extract_qnap_fw.sh

This script unpacks a firmware image. The firmware image can be passed in the
original form (\*.img), decrypted (\*.tgz), or as a source directory.

Usage:

    ./extract_qnap_fw.sh firmware.img destdir
    ./extract_qnap_fw.sh firmware.img.tgz destdir
    ./extract_qnap_fw.sh srcdir destdir

this results in:

    destdir/fw              files extracted from the firmware.img
    destdir/sysroot         unpacked initrd/initramfs, rootfs2, rootfs_ext
    destdir/qpkg            unpacked qpkg.tar

### extract_qpkg.sh

This script unpacks a QPKG file.

Usage:

    ./extract_qpkg.sh package.qpkg [destdir]

Another way to do this would be using the QDK tool.

</br>
</br>
### what is different compared to the original max-boehm tools?

The max-boehm script does not work on macOS for two reasons:

Reason 1:

    SKIP=`wc -c < $DEST/header_script`

On macOS, `wc -c` has a leading tab.
The solution would be:

    SKIP=$(sed -n 's/script_len=\(.*\)/\1/p' $DEST/header_script)

Reason 2:

    od -t x1 -w4 -Ad -v $DEST/payload | grep '1f 8b 08 00' | awk '{print $1}'

The macOS version of `od` does not support the parameter `-w` (--width).
A solution would be:

    printf "%d\n" $(hexdump -C $DEST/payload | grep '1f 8b 08 00' | awk '{print "0x"$1}')

    hexdump displays the position of the grep'ped string in hex.
    'print "0x"$1' will output the position with a leading '0x'
    'printf "%d"' will convert a hexadecimal to decimal number

The `exatract.qpkg` script has been amended.
