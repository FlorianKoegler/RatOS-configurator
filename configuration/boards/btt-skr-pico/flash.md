# Flash for USB connection

Connect the SKR Pico via USB. Add the USB power jumper.

1. Compile firmware manually with "Flash Manually Via SD Card".

2. Place SKR Pico into flash mode.

3. Use `lsblk` to find the volume name:

    ```bash
    pi@TicTac:~ $ lsblk
    NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sdb           8:16   1  128M  0 disk
    └─sdb1        8:17   1  128M  0 part
    mmcblk0     179:0    0 59.5G  0 disk
    ├─mmcblk0p1 179:1    0  256M  0 part /boot
    └─mmcblk0p2 179:2    0 59.2G  0 part /
    ```

    Here, the volume is `/dev/sdb1`

4. Mount the volume and copy the firmware to it:

    ```bash
    sudo mkdir -p /mnt/pico
    sudo mount /dev/sdaXYZ /mnt/pico
    sudo cp printer_data/ratos/firmware-skr-pico.uf2 /mnt/pico/
    ```

    Replace `dev/sdaXYZ` with the volume. The mounted volume will eject itself.

5. Remove the boot jumper and reset the SKR Pico.

# Flash for UART

Connect the SKR Pico via USB and the UART header. Remove the USB power jumper.

1. Copy the firmware.config into the klipper repository:

    ```bash
    cp -f /home/pi/printer_data/config/RatOS/boards/btt-skr-pico/firmware.config /home/pi/klipper/.config
    pushd /home/pi/klipper
    ```

2. Use `make_menuconfig` to change the communication interface:

    ```bash
    make menuconfig
    ```

    Choose `UART0 on GPIO0/GPIO1` instead of `USBSERIAL`.

3. Build the firmware:

    ```bash
    make clean
    make
    ```

4. Mount the volume and copy the firmware to it:

    ```bash
    sudo mkdir -p /mnt/pico
    sudo mount /dev/sdaXYZ /mnt/pico
    sudo cp /home/pi/klipper/out/klipper.uf2 /mnt/pico/
    ```

    Replace `dev/sdaXYZ` with the volume. The mounted volume will eject itself.

5. Remove the boot jumper and reset the SKR Pico.

6. Add the following section to `printer.cfg`:

````
[mcu]
serial: /dev/ttyAMA0
restart_method: command
```
