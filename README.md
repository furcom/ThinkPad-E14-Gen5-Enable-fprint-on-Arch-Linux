# ThinkPad E14 Gen5 Enable fprint on Arch Linux
## 1 - How to enable fingerprint:
### 1.1 - Install packages (Not as root!):
* Install git:
    ```
    pacman -S git
    ```
* Install yay:
    ```
    git clone https://aur.archlinux.org/yay-bin.git
    ```
    ```
    cd ./yay-bin
    ```
    ```
    makepkg -si
    ```
* Install libfprint-fpcmoh-git (AUR):
    ```
    yay -S libfprint-fpcmoh-git
    ```
* Install and enable fprintd:
    ```
    pacman -S fprintd fprintd-pam
    ```
    ```
    systemctl start fprintd && systemctl enable fprintd
    ```
### 1.2 - Register fingerprint
* Login configuration (Change debug, max-tries and timout to your liking):
    ```
    for file in /etc/pam.d/{login,su,sudo,gdm,lightdm,polkit-1}; do { echo 'auth sufficient pam_fprintd.so [debug=on] [max-tries=10] [timeout=5]'; cat "$file"; } > temp && mv temp "$file"; done
    ```
* Delete existing fingerprint signature
    ```
    fprintd-delete "$USER"
    ```
* Register all fingers (left + right hand)
    ```
    for finger in {left,right}-{thumb,{index,middle,ring,little}-finger}; do fprintd-enroll -f "$finger" "$USER"; done
    ```
### 1.3 - Troubleshooting
* Maybe you'll need to enable fingerprint authentication with authselect (I needed this on e.g. Fedora Linux):
  ```
  authselect enable-feature with-fingerprint
  ```
  ```
  authselect apply-changes
  ```
