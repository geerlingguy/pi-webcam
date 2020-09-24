# Raspberry Pi Webcam

[![CI](https://github.com/geerlingguy/pi-webcam/workflows/CI/badge.svg?branch=master)](https://github.com/geerlingguy/pi-webcam/actions?query=workflow%3ACI)

<p align="center"><img src="https://raw.githubusercontent.com/geerlingguy/pi-webcam/master/files/pi-webcam-tripod.jpeg" width="285" height="405" alt="Raspberry Pi 4 with HQ Camera and Tamron Lens on Tripod" /></p>

Inspired by David Hunt's blog post showing how to use a [Raspberry Pi Zero with a Pi Camera as a USB Webcam](http://www.davidhunt.ie/raspberry-pi-zero-with-pi-camera-as-usb-webcam/), as well as [justinschuldt's gist](https://gist.github.com/justinschuldt/36469e2a89d95ef158a8c4df091e9cb4), I wanted to make my Raspberry Pi 4 do the same thing, but automated and with all the scripts wrapped in version control, since the blog post was a little bit vague in some areas.

This Ansible playbook can be run on a Raspberry Pi to set it up as a USB OTG webcam.

What does that mean? Well, after running the playbook:

  - You plug the Pi into your computer
  - You wait until it boots
  - Bingo! Webcam usable in any software (Zoom, Teams, Meet, OBS, QuickTime, etc.)

It works on Mac, Windows, and all the flavors of Linux I've tested so far.

## Caveats

The playbook is meant to run on a brand new installation of Raspbian that has not had any configuration changes via `raspi-config` or any other tools, though it _should_ work correctly with an existing installation.

There are a few things you should probably do manually at some point, including:

  - Changing the default password for the `pi` user account.
  - Setting a WiFi country and enabling WiFi if you don't want to keep your Pi plugged into ethernet for remote access.

Note that the playbook modifies your boot config, and as such you _should not run this playbook on a microSD or other boot volume you're not ready to reformat and re-flash!_

## Getting Started

  1. Make sure you have Ansible installed on your computer.
  1. Flash the latest Raspberry Pi OS to your Raspberry Pi. Make sure you added an `ssh` file to the boot volume so SSH is enabled on first boot.
  1. Plug in your boot volume, and plug your camera into the camera connector.
  1. Boot the Raspberry Pi.
  1. Make sure you can log into the Pi via SSH.
  1. Clone or download this repository to your computer.
  1. Update the IP address in `inventory` to match the IP address of your Raspberry Pi.
  1. Edit the `config.yml` file to your liking (the defaults should be fine though).
  1. Run the Ansible playbook:

     ```
     ansible-playbook main.yml
     ```

At the end of the playbook (assuming this is the first time you've run it), the Raspberry Pi should reboot itself. At this point, it _should_ (assuming everything worked) be set up as a USB webcam.

Grab a USB cable and plug the Pi into a port on your computer.

Open up any video recording/conferencing software, and go to the camera selection, and BOOM! Select the Pi.

## Known issues

Well... most of the known issues have to do with the other projects _this_ project relies on:

  - If you're not using a Mac, you may need to adjust the brightness setting in the `uvc-gadget.c` file and re-compile it manually. I'm looking into a better way to allow this to be configured.
  - The Pi 4 currently locks up when you try to use USB OTG and enable the webcam for some reason. (I've only successfully tested this on a Pi Zero and Pi Zero W).
  - This stuff is a little bit of a complicated ball of string, so future Raspberry Pi OS and kernel updates could cause issues. I would not run this on a Raspberry Pi that is controlling a breathing machine or something like that.

Yadda, yadda, standard "if something breaks don't blame me" disclaimer. The worst thing I've done to my Pi in testing this so far is accidentally breaking off the locking connector for the camera cable. Oops.

## License

MIT.

## Author

I'm [Jeff Geerling](https://www.jeffgeerling.com), and I approve of this repository.
