---
description: Customizing my Kali Linux to feel more suitable
icon: palette
---

# Basic Customization

Date: February 3, 2026

After the initial installation, the first priority was transforming the raw OS into a comfortable, functional workspace. These "Quality of Life" (QoL) tweaks ensure the environment is optimized for long study and lab sessions.

## 1. Before Customization

Before I add any tweaks and personalization, my Kali Linux looked like this:

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Standard layout, standard wallpaper, font size is fairly small, and the clock is misaligned. This isn't very suitable for long sessions of scripting and lab work, so I'm gonna make a few tweaks.

## 2. Navigating to the Settings

Intuitively, I clicked on the Kali Linux icon to check out the menu. Afterwards, I clicked on the settings icon to see what settings I could tweak.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Upon opening the settings, I was bombarded by an array of many settings to tweak. After a few minutes of thinking, these are following settings I plan on changing:

1. **The taskbar position and appearance**: The icons on the taskbar are fairly small, I'm also not very used to the taskbar being on the top of the screen so I'd like it to be at the bottom.
2. **The desktop icon set and appearance**: The desktop icons look nice but I'd like to see what other colors or options Kali/Debian has to offer.
3. **The font size**: The font size is so small! This is going to cause me eye strains and probably give me a headache if I'll be scripting for hours.
4. **Disabling the Kali Linux screensaver**: Although I understand this feature has a security purpose - to make sure no one accesses your desktop when you leave your device unattended, I plan on using mostly at home so I'm disabling this feature.

## 3. Customizing Kali

Firstly, I'm going to modify the taskbar, or as they call it here - the Panel. I clicked on the Panel and did the following modifications:

1. I set the panel to the bottom of the screen.
2. I increased the panel row size to 45.
3. I disabled automatic icon size adjustment and set the icons to 30.
4. I added the network monitor for my lab and added the Eyes for fun.

Next, I went to the appearance tab to modify the general appearance of Kali. I did the following:

1. I set my style to Kali-Slate-Light.
2. I set my icon set to Flat-Remix-Slate-Light.
3. I kept both fonts but increased their sizes to 13.

Afterwards, I also disabled the xfce screensaver and changed my system time to be in the correct timezone. While attempting to change my system time, I couldn't figure out where the setting was, so I used the terminal and ran the following command:

```bash
timedatectl list-timezones | grep Yangon
sudo timedatectl set-timezone Asia/Yangon
```

The first command checks what the timezone name for my city is and the second command sets my timezone.

Lastly, I changed the wallpaper to a more simpler one as the default one felt a bit distracting to me. And there you have it, Kali looks more usable and pleasing now!

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

That's all for today!&#x20;
