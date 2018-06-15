# finger print device in Linux system on Thinkpad

- Firstly thanks to the amazing work that nmikhailov done in [Validity90](https://github.com/nmikhailov/Validity90) and Trevisan done in [libfprint](https://github.com/3v1n0/libfprint)

- The device had both tested on Linuxmint & Arch
- my example in device ID: 138:0090

## 1.Check your finger print device in terminal

```bash
lsusb
```

- and show device ID (example for me) `Bus xxx Device xxx: ID 138a:0090 Validity Sensors, Inc.`

- here are some integrated fingerprint Reader in [www.thinkwiki.org](https://www.thinkwiki.org/wiki/Integrated_Fingerprint_Reader)

|USB ID|Reader|Software|
|---|---|---|
|08ff:1600|Authentec AES1610|fprint|
|08ff:2580|Authentec AES2501|fprint|
|08ff:2810|Authentec 2810|supported by libfprint v0.5.0 onwards released December 2012|
|138a:0017|Validity Sensors, Inc.|Fingerprint Reader	(to test) Driver Patch as use case with T440 or gist sample Supported by libfprint v0.6.0|
|**138a:0090**|Validity Sensors|RE work for validity90 fingerprint reader|

## 2.Install packages(two methods)

- 2.1 Fingerprint GUI for Ubuntu 14.04, 16.04, 17.10 and any distribution based thereupon
  - you can download the `fingerprint-gui-1.09.tar.gz` in repository or find the release packages in [www.ullrich-online.cc]((http://www.ullrich-online.cc/fingerprint/))
  - **! check if your device is in [supported readers](https://launchpad.net/~fingerprint/+archive/ubuntu/fingerprint-gui) then install**

---

- 2.2 Install fprintd (for me in device 138a:0090)

  - read the guide: [Validity90](https://github.com/nmikhailov/Validity90) and [libfprint](https://github.com/3v1n0/libfprint) in github

  - for ubuntu/linuxmint

    ```bash
    sudo apt install fpirntd
    sudo add-apt-repository -y ppa:3v1n0/libfprint-vfs0090
    sudo apt update
    sudo apt install libpam-fprintd
    ```
  - for archlinux

    ```bash
    sudo pacman -S fprintd
    yaourt -S libfprint-vfs0090-git
    ```

## 3.Enroll your finger print

- add your signature for a finger (touch your finger device)

  ```bash
  fprintd-enroll
  ```

  - ! use `sudo fprintd-enroll` may have some bug

- if not work

  ```bash
  for finger in {left,right}-{thumb,{index,middle,ring,little-finger}; do fprintd-enroll -f "$finger" "$USER"; done
  ```

  - e.g `for finger in {left,right}-{thumb,{index,middle,ring,little-finger}; do fprintd-enroll -f "right-index-finger" "kevin"; done`

  - ! here has a bug, you may need to add 10x4 times to add finger signature

## 4.Login configuration

- read the guide in [archlinux wiki](https://wiki.archlinux.org/index.php/Fprint)

- 4.1 For linuxmint
  - Add `pam_fprintd.so` as `sufficient` to the top of the auth section of `/etc/pam.d/runuser`

  ```bash
  sudo nano /etc/pam.d/runuser
  ---
  auth            sufficient      pam_fprintd.so
  auth            sufficient      pam_rootok.so
  session         optional        pam_keyinit.so revoke
  ...
  ```

- 4.2 For archlinux
  - Add `pam_fprintd.so` as `sufficient` to the top of the auth section of `/etc/pam.d/system-local-login`

## 5.Reboot

- check the light of finger device
- in linuxmint, print finger then click sign in
- in archlinux, click `enter` then print finger