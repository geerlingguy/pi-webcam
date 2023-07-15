# Raspberry Pi Webcam

[![CI](https://github.com/geerlingguy/pi-webcam/workflows/CI/badge.svg?branch=master)](https://github.com/geerlingguy/pi-webcam/actions?query=workflow%3ACI)

<p align="center"><img src="https://raw.githubusercontent.com/geerlingguy/pi-webcam/master/files/pi-webcam-tripod.jpg" width="400" height="400" alt="Raspberry Pi Zero W with HQ Camera and wide-angle lens on Tripod" /></p>

Inspired by David Hunt's blog post showing how to use a [Raspberry Pi Zero with a Pi Camera as a USB Webcam](http://www.davidhunt.ie/raspberry-pi-zero-with-pi-camera-as-usb-webcam/), as well as [justinschuldt's gist](https://gist.github.com/justinschuldt/36469e2a89d95ef158a8c4df091e9cb4), I wanted to make my Raspberry Pi do the same thing, but automated and with all the scripts wrapped in version control, since the blog post was a little bit vague in some areas.

This Ansible playbook can be run on _most_ Raspberry Pis to set it up as a USB webcam.

What does that mean? Well, after running the playbook:

  - You plug the Pi into your computer
  - You wait until it boots
  - **Bingo!** Webcam usable in any software (Zoom, Teams, Meet, OBS, QuickTime, etc.)

It works on Mac, Windows, and all the flavors of Linux I've tested so far. It even works on another Raspberry Pi (I've tested it on a Pi 4 and Pi 400).

## Video overview of this project

I published a video in November 2020 detailing how to set up your Pi Webcam using this project. Click the thumbnail below to view it:

<p align="center"><a href="https://www.youtube.com/watch?v=8fcbP7lEdzY"><img src="https://raw.githubusercontent.com/geerlingguy/pi-webcam/master/files/pi-video-youtube.jpg" width="497" height="280" alt="Raspberry Pi Zero is a REAL USB webcam" /></a></p>

## Caveats

The playbook is meant to run on a brand new installation of Raspbian that has not had any configuration changes via `raspi-config` or any other tools, though it _should_ work correctly with an existing installation.

Note: **This modifies your boot config**, and as such you _should not run this playbook on a microSD or other boot volume you're not ready to reformat and re-flash!_

## Setting up the Hardware

It doesn't matter if you set up the software or hardware first, you just need to do both to have a functional webcam.

There are a number of different components you can purchase instead of these, but here's the list of what I'm using in my 'official' version that is well-tested and that I use frequently on my Mac and Windows laptops:

  - [Raspberry Pi Zero W](https://www.raspberrypi.org/products/raspberry-pi-zero-w/) (can also use Zero)
  - [Raspberry Pi High Quality Camera](https://www.raspberrypi.org/products/raspberry-pi-high-quality-camera/)
  - [Raspberry Pi Zero camera cable](https://thepihut.com/products/raspberry-pi-zero-camera-adapter)
  - [6mm wide-angle lens for HQ Camera](https://thepihut.com/products/raspberry-pi-high-quality-camera-lens)
  - [Pi Hut Pi Zero HQ Camera mounting plate](https://thepihut.com/products/mounting-plate-for-high-quality-camera)

Once you have everything, it's a matter of putting it all together.

Check out my video on YouTube for a detailed assembly and setup guide: [Raspberry Pi Zero is a PRO HQ webcam for less than $100!](https://www.youtube.com/watch?v=8fcbP7lEdzY)

### Mounting the webcam

The HQ Camera includes a female tripod socket, so any tripod or mounting arm will do. I've used a variety of mounts depending on the purpose, for example:

  - [SmallRig Ballhead Clamp Mount](https://amzn.to/33828gy): To mount the cam above my monitor on my monitor arm
  - [Joby GorillaPod Magnetic Mini](https://amzn.to/3nNw8pX): Useful in a huge variety of situations
  - [Joby GorillaPod 5K stand and ballhead](https://amzn.to/2IZa4Kd): Rock solid and a little taller, but it's definitely overkill
  - [25 inch flexible gooseneck webcam desk mount](https://amzn.to/3nOrxDO): Can mount the camera any which way using this, especially great for top-down shots

## Setting up the Software

There are two ways you can run this automated setup. You can either run everything on the Raspberry Pi itself (e.g. if you plug in a keyboard, mouse, and monitor), or you can run it from another computer.

### Setup on the Raspberry Pi

> **Pro Tip**: If you want to use a Pi Zero for the webcam, and don't want to wait half an hour for it to run all this automation, you can put the flashed microSD card into a newer Pi (like the Pi 4) and do all this work there, then when it's done pull the card and pop it into a Pi Zero.

  1. Flash the latest Raspberry Pi OS to a microSD card.
  1. Once Raspbian is loaded on the card, insert the card in your Pi, and plug in your Pi to boot it up.
  1. Follow the setup wizard, and if you want to easily be able to log into the Pi later, connect to a WiFi network, and also run the `raspi-config` tool and enable SSH.
  1. The Raspberry Pi should ask to be restarted. Go ahead and restart now, and wait for it to boot back up.
  1. Open the Terminal application (in the launcher or in Menu > Accessories > Terminal).
  1. Install Ansible and Git: `sudo apt update && sudo apt install -y python3-dev python3-pip libyaml-dev libffi-dev git && sudo pip3 install cryptography==3.1.1 && sudo pip3 install --no-binary pyyaml ansible` (**Warning**: this can take a while, especially on slower Pis!).
  1. Clone this repository to your Pi: `git clone https://github.com/geerlingguy/pi-webcam.git`
  1. Go into the repository directory: `cd pi-webcam`
  1. Use the local inventory file: `cp inventory-local.example inventory`
  1. Run the Ansible playbook: `ansible-playbook main.yml`
  1. You can shutdown the Pi at this point.

### Setup from another Computer

  1. Make sure you have [Ansible installed](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) on your computer.
  1. Flash the latest Raspberry Pi OS a microSD card. Make sure you added an `ssh` file to the boot volume so SSH is enabled on first boot.
  1. Once Raspbian is loaded on the card and you have the `ssh` file in the boot volume, insert the card in your Pi, and plug in your Pi to boot it up.
  1. Make sure you can log into the Pi via SSH. Ideally, add your SSH key to the Pi using `ssh-copy-id`. If you don't, make sure to add the `-k` parameter to the `ansible-playbook` command later so you can enter the SSH password.
  1. Clone or download this repository to your computer.
  1. Use the ssh inventory file: `cp inventory-ssh.example inventory`
  1. Update the IP address in `inventory` to match the IP address or hostname of your Raspberry Pi.
  1. Edit the `config.yml` file to your liking (the defaults should be fine though).
  1. Run the Ansible playbook:

     ```
     ansible-playbook main.yml
     ```

  1. You can shutdown the Pi at this point (log in via SSH then `sudo shutdown now`).

## Plugging in the Camera

You could've done this earlier, but if you haven't yet, plug the camera module into the Raspberry Pi using the appropriate camera connector.

## Using the Pi as a webcam

At this point, it _should_ (assuming everything worked) be set up as a USB webcam.

All you need to do is grab a USB cable and plug the Pi into any USB port on your computer. But which port do you plug into on the Pi? Well, I have a handy little table here:

| Pi Model | Which USB Port for device mode Webcam use? |
| --- | --- |
| Pi Zero / Pi Zero W | 'USB' port (next to 'PWR IN') |
| Pi A/A+ | 'USB' port (you need a non standard Type-A to Type-A USB cable) |
| Pi 4 | USB-C 'Power' port |

Once it's plugged in, you need to wait 30 seconds or so before the Pi will be booted and fully ready, then you can open any video recording/conferencing software, go to the camera selection, and BOOM! Select the 'Pi Webcam'.

> _What about the other Pi models?_ Unfortunately, other Pi models do not have full support for Device mode, so they cannot be used as a webcam.

### Powering down the Pi

If you want, and you still have WiFi enabled, or the Pi is otherwise connected to your network, you can log into it via SSH and run `sudo shutdown now` to power it off cleanly.

But I like living on the edge... it's not like the thing is writing a ton of data to the microSD card. When I'm finished using it as a webcam, I just unplug it. Simple as that.

If, for some strange reason, it did end up getting corrupted, I could just re-run this automation to set it up again. No big deal! I haven't had that happen yet, though.

## Known issues

Well... most of the known issues have to do with the other projects _this_ project relies on:

  - If you're not using a Mac, you may need to adjust the brightness setting in the `uvc-gadget.c` file and re-compile it manually. I'm looking into a better way to allow this to be configured.
  - The upstream repository providing the `uvc-gadget` application currently defaults to 720p resolution, which is great for most use cases. You can stream at 1080p, though the Pi Zero and older Pis may drop frames at that resolution. Follow [this issue](https://github.com/geerlingguy/pi-webcam/issues/4) for progress making resolution more easily configurable.
  - The Pi 4 model B currently locks up when you try to use USB Device mode and enable the webcam for some reason. You can run the [2020-02-07 Raspbian release](http://downloads.raspberrypi.org/raspbian/images/raspbian-2020-02-07/) to work around this bug for now. Follow [this issue](https://github.com/geerlingguy/pi-webcam/issues/5) for progress resolving this bug.
  - This stuff is a little bit of a complicated ball of string, so future Raspberry Pi OS and kernel updates could cause issues. I would not run this on a Raspberry Pi that is controlling a breathing machine or something like that.

Yadda, yadda, standard "if something breaks don't blame me" disclaimer. The worst thing I've done to my Pi in testing this so far is accidentally breaking off the locking connector for the camera cable. Oops.

## License

MIT.

## Author

I'm [Jeff Geerling](https://www.jeffgeerling.com), and I approve of this repository.
