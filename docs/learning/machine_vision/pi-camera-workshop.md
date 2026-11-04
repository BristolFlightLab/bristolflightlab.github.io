# Pi 5 Setup Workshop

* Hostname: pi5-group1
* Username: pi
* Pwd: flightlab

## 1. Flash the SD card

1. Install [Raspberry Pi Imager](https://www.raspberrypi.com/software/) on your laptop
2. Choose **Raspberry Pi 5**, then **Raspberry Pi OS (64-bit)**, then your SD card.
3. On the customisation screen, set:
   - hostname, username and password (from the table above)
   - Wi-Fi SSID, password and country
   - locale and timezone
   - **Enable SSH** (password authentication is fine for now)
4. Write the card. You will probably need to reformat the sd card at this step. This will take a few minutes.

While it writes, connect the camera to the Pi. The Pi 5 uses the smaller 22-pin camera connector. Check the ribbon orientation against the guide before you power up, because a ribbon in the wrong way round is the most common reason the camera isn't found. [Camera documentation](https://www.raspberrypi.com/documentation/accessories/camera.html).


## 2. First boot

Plug in the screen, keyboard and SD card, then power on. The first boot takes a couple of minutes and may restart once.

Open a terminal:

```bash
hostname                 # should match your table
hostname -I              # your IP address, so write it down
ping -c 3 google.com     # check you're online
sudo apt update && sudo apt full-upgrade -y
```

## 3. SSH from your laptop (Only if using autonomous network - won't work on eduroam)

Windows, macOS and Linux all have `ssh` built in. Open a terminal (PowerShell on Windows):

```bash
ssh <username>@<hostname>.local
```

If that can't find the Pi, use the IP address instead: `ssh <username>@<ip>`. Type `yes` the first time it asks about the fingerprint.

### Optional SSH Step - Set up a key so you don't have to type your password every time

On your **laptop**:

```bash
ssh-keygen -t ed25519            # press Enter to accept the defaults
ssh-copy-id <username>@<hostname>.local
```

Windows doesn't have `ssh-copy-id`, so use this in PowerShell:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh <username>@<hostname>.local "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

Log out and back in. You shouldn't be asked for a password.

**Recommended:** install the VS Code Remote-SSH extension so you can edit files on the Pi directly: https://code.visualstudio.com/docs/remote/ssh

**Checkpoint:** before moving on, you should be able to log in over SSH without typing a password.

## 4. Camera from the command line

Plug the camera in with the ribbon cable. Do this when the pi is not powered on.

Try these on the Pi's own screen first:

```bash
rpicam-hello --list-cameras       # is the camera detected?
rpicam-hello -t 0                 # live preview, Ctrl+C to stop
rpicam-still -o test.jpg          # take a photo
rpicam-vid -t 5000 -o test.h264   # 5 seconds of video
```

If using SSH there's no screen to show a preview on, so add `-n`:

```bash
rpicam-still -n -o test.jpg
```

Then copy the image to your laptop to view it. Run this **on the laptop**:

```bash
scp <username>@<hostname>.local:~/test.jpg .
```

The images are probably blurry, this is a good point to play with the focus settings (physical not software) as you will need to adjust this in future.

## 5. Python: Picamera2 and OpenCV

```bash
sudo apt install -y python3-opencv
python3 -m venv --system-site-packages ~/cv-env
source ~/cv-env/bin/activate
```

Don't skip `--system-site-packages`. Picamera2 is installed system-wide, so a plain venv won't find it.

Save this as `capture.py` and run it with `python capture.py`:

```python
from picamera2 import Picamera2
import cv2

picam2 = Picamera2()
config = picam2.create_preview_configuration(main={"format": "RGB888", "size": (640, 480)})
picam2.configure(config)
picam2.start()

frame = picam2.capture_array()
print(frame.shape)
cv2.imwrite("frame.jpg", frame)

picam2.stop()
```

Note: Picamera2's `RGB888` format is stored in BGR order, which is what OpenCV expects. So if your colours look swapped somewhere, this is probably why.

**Stretch goal:** convert the frame to HSV, threshold it on a cube colour, and draw a bounding box around the largest blob. Save the result to a file.

## 6. Shut down properly

```bash
sudo poweroff
```

Wait for the green LED to stop flashing before you pull the power. Pulling the plug while it's running is the easiest way to corrupt the SD card.

---

## If something's not working

| Problem | Try |
|---|---|
| `No cameras available` | Power off, reseat the ribbon, check orientation and that it's the 22-pin cable |
| `Could not resolve hostname` | Use the IP address instead of `.local` |
| `Connection refused` | SSH isn't enabled: `sudo raspi-config` → Interface Options → SSH |
| `REMOTE HOST IDENTIFICATION HAS CHANGED` | You re-flashed the card. Run `ssh-keygen -R <hostname>.local` on your laptop |
| `No module named picamera2` | Your venv is missing `--system-site-packages`. Delete it and recreate it |
| Lightning bolt icon on screen | The power supply isn't good enough, so use the official 27W one |

## Links

- [Getting started](https://www.raspberrypi.com/documentation/computers/getting-started.html)
- [Remote access (SSH, VNC, Connect)](https://www.raspberrypi.com/documentation/computers/remote-access.html)
- [Camera software](https://www.raspberrypi.com/documentation/computers/camera_software.html)
- [Picamera2 manual](https://datasheets.raspberrypi.com/camera/picamera2-manual.pdf)
- [Picamera2 examples](https://github.com/raspberrypi/picamera2/tree/main/examples)
