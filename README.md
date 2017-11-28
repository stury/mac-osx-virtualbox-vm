# macOS VirtualBox VM Instructions

Current macOS version: *High Sierra (10.13)*, tested with VirtualBox *5.1.30 r118389*

To build a VM running macOS, follow the directions below:

  1. Download the installer from Mac App Store (it should be available in the 'Purchases' section if you've acquired it previously). The installer will be placed in your Applications folder.  (Should work for Yosemite, El Capitan, Sierra and High Sierra - 10.10-10.13.)
  2. Make the script executable and run it: `chmod +x prepare-iso.sh && ./prepare-iso.sh`.

      If the script fails to find the installer you can specify its path as the first parameter. By default, the output is saved as <Yosemite|El Capitan|Sierra|HighSierra>.iso on the Desktop. You can change this using the second parameter.
      Example:

        ```./prepare-iso.sh /Applications/Install\ macOS Sierra\ 2.1\ Beta\ 2.app /Users/Steve/sierra-2.1-b2.iso```

  3. Open VirtualBox and create a new VM.
  4. Set:
      - name: your choice
      - type: `OS X`
      - version: `Mac OS X 10.11 El Capitan (64-bit)` or `macOS Sierra`, if your version of VirtualBox has been updated to include it.
  5. Follow the rest of the VM creation wizard and either leave the defaults or adjust to your liking. You may want to increase the video memory from the VirtualBox default of 16MB to 64MB or 128MB. While the current release of macOS Sierra does boot with just 16MB, interace performance is severly constraint.
  6. In Terminal, run the command `VBoxManage modifyvm "{vmname}" --cpuidset 00000001 000306a9 00020800 80000201 178bfbff` (where `"{vmname}"` is the exact name of the VM set in step 4) so the VM has the right CPU settings for macOS.
  7. To prevent choppiness in the VM, go into settings and uncheck the 'Enable Audio' option under 'Audio'.
  8. Click 'Start' to boot the new VM.
  9. Select the iso created in step 2 when VirtualBox asks for it.
  10. In the installer, select your preferred language.
  11. Go to `Utilities > Disk Utility`. Select the VirtualBox disk and choose `Erase` to format it as a `Mac OS Extended (Journaled)` drive.
  12. Quit Disk Utility, and then continue with installation as normal.


## Troubleshooting & Improvements

- I've noticed that sometimes I need to go in and explicitly mark the iso as a Live CD in the VM settings in order to get the VM to boot from the image.
- Conversly, VirtualBox sometimes does not eject the virtual installer DVD after installation. If your VM boots into the installer again, remove the ISO in `Settings -> Storage`.
- VirtualBox uses the left command key as the "host key" by default. If you want to use it for shortcuts like `command+c` or `command-v` (copy&paste), you need to remap or unset the "Host Key Combination" in `Preferences -> Input -> Virtual Machine`.
- The default Video Memory of 16MB is far below Apple's official requirement of 128MB. Increasing this value may help if you run into problems and is also the most effective performance tuning.
- Depending on your hardware, you may also want to increase RAM and the share of CPU power the VM is allowed to use.
- When the installation is complete, and you have a fresh new macOS VM, you can shut it down and create a snapshot. This way, you can go back to the initial state in the future. I use this technique to test the [`mac-dev-playbook`](https://github.com/geerlingguy/mac-dev-playbook), which I use to set up and configure my own Mac workstation for web and app development.
- High Sierra installation is a bit tricky.  You can do the first part of the installation without any trouble, but the second part is not obvious.  This article on installing [High Sierra](http://tobiwashere.de/2017/10/virtualbox-how-to-create-a-macos-high-sierra-vm-to-run-on-a-mac-host-system/) will walk you through the steps needed. 

## Larger VM Screen Resolution

To control the screen size of your macOS VM:

  1. Shutdown your VM
  2. Run the following VBoxManage command:

`VBoxManage setextradata "{vmname}" VBoxInternal2/EfiGopMode N`  

Replace `{vmname}` with the name of your Virtual Machine.  Replace `N` with one of 0,1,2,3,4,5.  These numbers correspond to the screen resolutions 640x480, 800x600, 1024x768, 1280x1024, 1440x900, 1920x1200 screen resolution respectively.

The video mode can only be changed when the VM is powered off and remains persistent until changed.  The full discussion can be found at this link for the original [`Forum Discussion`](https://forums.virtualbox.org/viewtopic.php?f=22&t=54030).

## Notes

  - Code for this example mostly comes from VirtualBox forums and [this article](http://sqar.blogspot.de/2014/10/installing-yosemite-in-virtualbox.html).
  - Subsequently updated to support Yosemite - Sierra based on [this thread](https://forums.virtualbox.org/viewtopic.php?f=22&t=77068&p=358865&hilit=elCapitan+iso#p358865).
  - I'm currently looking into using Packer (maybe in tandem with Ansible) to automate the process of building a macOS box for VirtualBox. Since the ISO needs to be generated by the end user, it's a bit more involved (i.e. manual download of the original installer image), but not much worse than Packer for linux distros.
    - See also:
      - https://github.com/timsutton/osx-vm-templates
      - https://github.com/AndrewDryga/vagrant-box-osx-mavericks/blob/master/README.md
  - To install command line tools after macOS is booted, open a terminal window and enter `xcode-select --install` (or just try using `git`, `gcc`, or other tools that would be installed with CLI tools).

## Author

This project was created in 2015 by [Jeff Geerling](http://jeffgeerling.com/).
