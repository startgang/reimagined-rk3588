> This post is from Armsom forum - https://forum.armsom.org/t/device-tree-settings/

## Device Tree Settings

1. Introduction
Device Tree Overlays make it possible to support multiple hardware configurations with a single kernel, without the need to explicitly load or blacklist kernel modules.

2. Supported Images for Overlay
Currently, the following images support overlays: Ubuntu22.04, Armbian.

3. How to Enable an Overlay
3.1 Enable Overlay for Ubuntu22.04 Image
Overlay files for the Ubuntu22.04 image are located at the board’s path: /boot/firmware/dtbs/rockchip/overlay/*.dtbo.

Find the keyword “overlays=” in the /boot/firmware/ubuntuEnv.txt file. Below is an example of using two overlays for ArmSoM-Sige7:

overlays=armsom-sige7-camera-imx415-4k armsom-sige7-display-10hd
After editing, restart the device to apply the Overlay settings.

3.2 Enable Overlay for Armbian Image
Overlay files for the Armbian image are located at the board’s path: /boot/dtb/rockchip/overlay/*.dtbo.

Find or add the keyword “overlays=” in the /boot/armbianEnv.txt file. Below is an example of using two overlays for ArmSoM-Sige7:

overlays=armsom-sige7-camera-imx415-4k armsom-sige7-display-10hd
After editing, restart the device to apply the Overlay settings.

