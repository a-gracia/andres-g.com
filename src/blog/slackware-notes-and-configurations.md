---
title: "Slackware Notes and Configurations"
pubDate: 2026-04-04
description: "A collection of notes and configurations for Slackware, including battery limits, CPU scaling and elogind hooks."
author: "Andrés Gracia Danies"
image:
  url: "/blog/slackware-tips.png"
  alt: "Slackware logo"
tags: ["linux", "slackware"]
---

Slackware is the oldest Linux distro still maintained and is still my favorite. Actually, I stopped distro hopping with Slackware 10 years ago and the reason is that everything is under your control: no magic, no unexpected automations and no systemd.

Also, even if the documentation is old, it's not outdated so almost everything you find on forums and blogs can be used with no issue. However, as the distro is not so popular, there is not so much info on very specific issues or it may be hard to find, so this post is all about keeping track of the tips, tricks, configurations, etc. that I find useful and somehow took some effort to find.

## Limit battery charge

In order to take care of your laptop battery and extend its life, charging must be limited so it doen't reach 100%.

In my case, for an Asus laptop, I set the charge limit to 80% using the following command as root:

```bash
echo 80 > /sys/class/power_supply/BAT0/charge_control_end_threshold
```

However, this setting is **not persistent** so when you reboot it will go back to 100%. So the way to make it persistent is to add that line to your `/etc/rc.d/rc.local` file.

### Reset charging limit after suspend/hibernate

Given that this is a laptop setting, you might be using suspend and hibernate quite often, and the bug you will notice is that whenever you resume from those states, the charge limit will be ignored (not reset). To solve this, you must use an elogind hook, so create the file `/etc/elogind/system-sleep/battery-threshold` and put the following code in it.

```bash
#!/bin/bash
case $1/$2 in
  pre/*)
    # Pre suspend/hibernate scripts.
    ;;
  post/*)
    # Post suspend/hibernate scripts.
        echo 79 > /sys/class/power_supply/BAT0/charge_control_end_threshold
        sleep 1
        echo 80 > /sys/class/power_supply/BAT0/charge_control_end_threshold
    ;;
esac
```

Finally, allow it to be executed:

```bash
chmod +x /etc/elogind/system-sleep/battery-threshold
```

If you want to learn more about elogind hooks, see the [elogind suspend/hibernate/resume/thaw hook scripts documentation](https://wiki.gentoo.org/wiki/Elogind#Suspend.2FHibernate_Resume.2FThaw_hook_scripts).

## Prevent laptop overheating

After a fresh install of Slackware you might notice your laptop is heating up even when it's idle. That's completely normal, and it's happening because your cpu governor might be set to `performance`, you can check it by running `cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor`.

You can also check if any of your cpu cores is running at a higher clock (which is the expected behavior when the governor is set to `performance`) with this command: `cat /proc/cpuinfo | grep MHz`.

So you will need to check the available governors for your cpu by running the following command:

```bash
$ cpufreq-info

cpufrequtils 008: cpufreq-info (C) Dominik Brodowski 2004-2009
Report errors and bugs to cpufreq@vger.kernel.org, please.
analyzing CPU 0:
  driver: intel_pstate
  CPUs which run at the same hardware frequency: 0
  CPUs which need to have their frequency coordinated by software: 0
  maximum transition latency: 4294.55 ms.
  hardware limits: 400 MHz - 3.90 GHz
  available cpufreq governors: performance, powersave
  current policy: frequency should be within 400 MHz and 3.90 GHz.
                  The governor "powersave" may decide which speed to use
                  within this range.
  current CPU frequency is 1.25 GHz.
```

As you can see, in my case I have bot `performance` and `powersave`, and the latter is the one that I chose.

So in order to set every single cpu governor, I will loop through all of them with `cpufreq-set`:

```bash
for cpu in /sys/devices/system/cpu/cpu[0-9]*; do
        cpufreq-set --cpu "${cpu##*cpu}" --governor powersave
done
```

Of course this setting is also not persistent, so you should put the command in your `/etc/rc.d/rc.local` file.
