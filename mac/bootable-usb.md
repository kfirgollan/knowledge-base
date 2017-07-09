# Bootable usb

The task of creating a bootable usb on a mac system is rather straight forward.
We start by converting the .iso image into a UDRW format with the following command

```
hdiutil convert -format UDRW -o destination_file.img source_file.iso
```

We now need to identify the disk name of the usb that we want to use.
We can see a list of all the disks using the following command

```
diskutil list
```

Now all that is left to do is to copy our converted image to the usb stick.
This can be easily accomplished using the dd tool.

```
dd if=destination_file.img.dmg of=/dev/disk2 bs=1m
```

If you want to see progress of the operation you can use the following command
```
progress -m -p $(ps -ef | grep "dd if" | grep -v "grep" | grep -v "sudo" | sed -E "s/ +/ /g" | cut -d" " -f 3)
```

Based on [create bootable usb stick from iso in mac os x](https://blog.tinned-software.net/create-bootable-usb-stick-from-iso-in-mac-os-x/)
