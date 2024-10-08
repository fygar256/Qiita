


**************************************************


Arch Linuxでの AMD ATI Mobility Radeon HD 5650/5 デバイスドライバの設定方法


**************************************************


Arch Linuxでの AMD ATI Mobility Radeon HD 5650/5 デバイスドライバの設定方法

まずは基本
```
sudo pacman -S xf86-video-ati
```
```/etc/X11/xorg.conf.d/20-radeon.conf
Section "Device"
    Identifier "Radeon"
    Driver "radeon"
EndSection
```

そして、Advanced settings.

```
yay -S xf86-video-amdgpu mesa-vdpau rocm-opencl-runtime vulkan-radeon
```

```
# /etc/environmentに以下を追加
LIBVA_DRIVER_NAME=radeonsi
LIBVA_DRIVER_PATH=/usr/lib/dri/radeonsi_drv_video.so
VDPAU_DRIVER=radeonsi
DISABLE_LAYER_AMD_SWITCHABLE_GRAPHICS_1=1
VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/radeon_icd.i686.json:/usr/share/vulkan/icd.d/radeon_icd.x86_64.json
RADV_PERFTEST=rt,gpl,nv_ms # Ray Tracing等の有効化
```

これで、動きました。
僕の環境では基本機能しか必要でないため、advanced settingsは無効にし、シンプル化しています。
