How to set a battery charge stop threshold for ASUS laptops on Linux
1. Find out your ASUS' laptop battery name.

Let's start by figuring out the battery name for your ASUS laptop. This can be done by using the following command:
```
ls /sys/class/power_supply
```
This command should output something like this:
```
AC0  BAT0
```
In this example (from my ASUS notebook), the name of the battery is BAT0, but like I mentioned above, for you it may also be BAT1 or BATT, these being the only supported battery names by the kernel for ASUS laptops.

2. Create a systemd service to set the battery charge stop threshold on boot.

Before creating the systemd service, check if your laptop actually has charge_control_end_threshold in /sys/class/power_supply/BAT* (without it, this won't work):
```
ls /sys/class/power_supply/BAT*/charge_control_end_threshold
```
If this command returns the path to charge_control_end_threshold, then your ASUS notebook supports limiting battery charging. If the command returns an error, saying there's no such file or directory, then your laptop doesn't support setting a charge threshold.

If your ASUS laptop has this file, we can continue. Create a file which we'll call battery-charge-threshold.service in /etc/systemd/system.

You can open this file with your default console text editor using:
```
sudo editor /etc/systemd/system/battery-charge-threshold.service
```
Or use Gedit or whatever graphical editor you want to use (e.g. for Gedit to open this file as root: gedit admin:///etc/systemd/system/battery-charge-threshold.service)

In this file you'll need to paste the following:
```
[Unit]
Description=Set the battery charge threshold
After=multi-user.target
StartLimitBurst=0

[Service]
Type=oneshot
Restart=on-failure
ExecStart=/bin/bash -c 'echo CHARGE_STOP_THRESHOLD > /sys/class/power_supply/BATTERY_NAME/charge_control_end_threshold'

[Install]
WantedBy=multi-user.target
```
Here, change BATTERY_NAME with the name of the battery (BAT0, BAT1 or BATT), and CHARGE_STOP_THRESHOLD with the battery charge stop threshold you want to use (ranging between 1 and 100). Note that I've read that one user couldn't set the charge threshold to any value, but only to 60, 80 and 100.

From what I've read, for best battery lifespan when the laptop is connected to AC most of the time, set the battery charge stop threshold at around 50 or 60. If the battery is used somewhat frequently, set the battery charge stop threshold at about 90.

3. Enable and start the battery-charge-threshold systemd service.

Now let's enable and start the newly created battery-charge-threshold systemd service:
```
sudo systemctl enable battery-charge-threshold.service

sudo systemctl start battery-charge-threshold.service
```
With systemd 220, it's possible to enable and start a service directly using systemctl enable --now, but I prefer to use 2 commands in case some readers use an older systemd version.

If you want to change the battery charge stop threshold level, you'll need to edit the /etc/systemd/system/battery-charge-threshold.service file, and change the number from the ExecStart line (after echo) to the new value you want to use, then reload systemd (because the file contents have changed) and restart the systemd service using the following commands:
```
sudo systemctl daemon-reload

sudo systemctl restart battery-charge-threshold.service
```
4. Verify that the battery charge stop threshold is working.

If your ASUS laptop supports it, the battery charging limit should now be set to the value you've used in step 2.

You can check this by charging your laptop to the threshold set in battery-charge-threshold.service, and then use this command:
```
cat /sys/class/power_supply/BATTERY_NAME/status
```
Replace BATTERY_NAME with the name of the battery, as explained under step 1.

If the battery charge stop threshold is working, the command should show the following output:
```
Not Charging
```
If it says "Discharging", make sure your ASUS laptop is actually on AC power, and not running on battery. However, if it says "Charging", well... then your laptop is charging past the charge stop threshold, so this is not working for you, either because your ASUS laptop doesn't support it (which is weird if you have charge_control_end_threshold as mentioned in step 2), you're using a Linux version that's too old for limiting battery charging on your ASUS notebook, or because of human error 😁️.
