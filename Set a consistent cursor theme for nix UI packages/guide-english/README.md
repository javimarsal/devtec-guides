# Set a consistent cursor theme for Nix UI packages

(leer esta guía en Español 🇪🇸)

<a id="table-of-contents">Table of contents:</a>

1. [Why this guide?](#why-this-guide)
1. [Initializing Nix (specific to Vanilla OS)](#initializing-nix-specific-to-vanilla-os)
1. [Installing Home Manager](#installing-home-manager)
1. [Setting a cursor theme for Nix apps](#setting-a-cursor-theme-for-nix-apps)
1. [Setting another theme (different from the system default one) for Nix and system](#setting-another-theme-different-from-the-system-default-one-for-nix-and-system)
    1. [Setting "Bibata Modern Ice" cursor theme for Nix apps](#setting-bibata-modern-ice-cursor-theme-for-nix-apps)
    1. [Setting "Bibata Modern Ice" cursor theme for the system (specific to Vanilla OS)](#setting-bibata-modern-ice-cursor-theme-for-the-system-specific-to-vanilla-os)
1. [Conclusion](#conclusion)
1. [Acknowledgments](#acknowledgments)
1. [References](#references)

<br />

These are some aspects you need to know before reading this guide:
- All these steps has been tested in an "out of the box" Vanilla OS 22.10-r9 installation.
- As I just mentioned, I'm using Vanilla OS, so some parts might be different (or not necessary to do) in other Linux distros. This parts are marked with `(specific to Vanilla OS)`.
- Some paths contain `[your-user-name]`, so you should change that part with the user name that you used to login on your system.
    - For example, if I find the path `/home/[your-user-name]` in this guide, I should change it by `/home/javimarsal` in my case, because `javimarsal` is the user name I'm using on my computer.
- Other paths start with `~/` (as `~/.local/share/icons`). The `~` symbol corresponds to `/home/[your-user-name]`.
    - I think it is necessary to know, maybe you have learnt this right now!
- If you already had initialized Nix, you can start following this guide from the ["Installing Home Manager"](#installing-home-manager) section.
- And if you also had installed Home Manager, you can jump into the ["Setting a cursor theme for Nix apps"](#setting-a-cursor-theme-for-nix-apps) section directly.
- If you didn't perform any of those steps, then start reading this guide from the beginning.

The most configuration is done using the terminal, specifically "Console", this is completly valid and the default one on Vanilla OS and GNOME.

Finally, I recommend reading this guide, in its entirety, before following the steps. In that way, you will realize if it fits your needs.

<br />

#### [(back to index)](#table-of-contents)

## Why this guide?

In some cases, installing a Nix app is the best choice to avoid installing the same app using [abroot](https://documentation.vanillaos.org/docs/ABRoot/) on Vanilla OS. Some of these apps must be used with an UI (User Interface) and it may be integrated with the system perfectly, but some apps may not. I mean, the app works as intended, but the cursor theme is not the same as the system one.

For that reason, I wanted to solve this visual problem, and I found a solution that works! So, if you want the same cursor theme for Nix apps and the system, follow this guide and you will learn how to do it.

<br />

#### [(back to index)](#table-of-contents)

## Initializing Nix (specific to Vanilla OS)

The first step is to initialize Nix. If you haven't do it before, execute this command:

```bash
apx init --nix
```

This will request your password, and after that a message will appear letting you know that a `.nix` folder will be created in your home directory. Just press the `Y` key (yes option) to proceed with the installation.

When the installation has finished, it is necessary to reboot in order to apply all changes in the future partition (a Vanilla OS feature).

<br />

#### [(back to index)](#table-of-contents)

## Installing Home Manager

**Home Manager** [[1]](#references) allows you to manage your user environment like installing packages or setting some configuration for Nix. This is so useful, so we are using this tool for our cursor configuration.

If you just finished initializing Nix, let's continue (if not, go to the [last section](#initializing-nix-specific-to-vanilla-os) before following this step).

Now, let's continue with the Home Manager installation [[2]](#references).

The first step is to add the `home-manager` channel:
```bash
nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
```

Then we update and download the latest version for the channels, including the `home-manager` channel that we have just added:
```bash
nix-channel --update
```

After that, we can install `home-manager`:
```bash
nix-shell '<home-manager>' -A install
```

Don't worry about some red text, this is just some warnings of Nix (due to `optionsDocBook is deprecated since 23.11 and will be removed in 24.05`) and it is a bug that will be fixed in a future version [[3]](#references).

After installing Home Manager, we can use this tool to set the cursor theme for Nix apps. In the next section we will use the `home-manager edit` command to achieve that.

<br />

#### [(back to index)](#table-of-contents)

## Setting a cursor theme for Nix apps

Before following this section make sure you have [initialized Nix](#initializing-nix-specific-to-vanilla-os) and [installed Home Manager](#installing-home-manager). If so, let's set the configuration to show the same cursor theme as the system one (Adwaita by default) for Nix UI applications.

To make some configuration, we need to edit the file:
- `/home/[your-user-name]/.config/home-manager/home.nix`

It can be done by using your favourite editor. Instead, after installing Home Manager, the `home-manager edit` command is now available for us, so let's use it to edit that file.

If you execute the command directly, maybe a similar error will be shown:
```bash
Please set the $EDITOR environment variable
```

That means that the `EDITOR` variable must be recognize in order to execute that command. As this environment variable is not set on Vanilla OS by default, a quick solution would be to execute the command after setting the variable temporarily. The `export` command will create this variable for the terminal sesion only (if you close the terminal, you will have to execute this command again). So, in this case, we will be using the "nano" editor:
```bash
export EDITOR=nano
```

And now enter into the configuration:
```bash
home-manager edit
```

(In order to understand how to define environment variables, you can take a look at this [guide](https://linuxhandbook.com/export-command/) later).

After opening the `home.nix` file, we can see that its content is similar to this:

```nix
{ config, pkgs, ... }:

{
    [CONTENT HERE]...
}
```

The last piece of code contains the line `[CONTENT HERE]...`, this is just to show you where to add the cursor theme configuration. There may be some different content already, so we must maintain the current one.

So, following this guide [[4]](#references) from [NixOS Wiki](https://nixos.wiki/wiki/Main_Page), we need to add the following line in order to apply the "Adwaita" cursor theme for Nix apps that work with an UI:

```bash
home.file.".icons/default".source = "${pkgs.gnome.adwaita-icon-theme}/share/icons/Adwaita";
```

Now, save and exit the file. If you're using nano, then press `Ctrl` + `O` and `enter` to save the file, and `Ctrl` + `X` to exit the file.

After researching for a while, I noticed that this is the configuration that fits the best on Vanilla OS.
- You could apply a different configuration and use a cursor theme URL instead (that will work), but it may add some cursor theme conflicts while using Vanilla OS apps inside containers. So, this will help you if you are using containers right now or in the future.

Now, a brief explanation about this configuration [[5]](#references). We can identify two parts:
- Before equals `=` symbol: this is the option that corresponds to the path containing the cursor theme.
- After equals `=` symbol: this is the path where the first option points to. We need two things to form this path:
    - **The package name**: this is the actual cursor theme and it will be downloaded from the Nix package collection (pkgs). So, in this case, the package name for the "Adwaita" cursor theme is `gnome.adwaita-icon-theme`. You can look for another cursor themes [here](https://search.nixos.org/packages?channel=23.05&from=0&size=50&sort=relevance&type=packages&query=gnome.adwaita-icon-theme).
    - **The cursor theme name**: in this case, the name is "Adwaita". If you wish, you can [download](https://github.com/GNOME/adwaita-icon-theme/releases/tag/43) the cursor theme to check the name (just extract the file and look for the name of the folder containing the "cursors" subfolder, it should be "Adwaita").

To finish, execute the following command to download the Nix expressions of all subscribed channels (it might no be necessary because we did it [previously](#installing-home-manager)):
```bash
nix-channel --update
```

Then build and activate the configuration:
```bash
home-manager switch
```

Taking into account:
- If you had a Nix application running and you don't see any change, try dragging the app window, or close and open it again.
- If any application shows the cursor in a different size than the normal one, just reboot your system. That should fixed the correct size and now everything should look nice!

<br />

#### [(back to index)](#table-of-contents)

## Setting another theme (different from the system default one) for Nix and system

At this point, you have set the same cursor theme for Nix apps and the system if "Adwaita" was your default cursor theme, and this may be enough for you.

In the following sections I will show you how to set a different cursor theme (Bibata Modern Ice) for Nix apps and the system. So, if you keep reading you will learn how to configure both parts.

<br />

### Setting "Bibata Modern Ice" cursor theme for Nix apps

If you closed your terminal after completing the previous sections, you will have to export the `EDITOR` variable as [we did before](#setting-a-cursor-theme-for-nix-apps).

The configuration remains the same for Nix apps, so only change the related "Adwaita" parts by the "Bibata" ones.

First, we open the Home Manager configuration:
```bash
home-manager edit
```

And then, change the "Adwaita" configuration by the "Bibata Modern Ice" cursor theme from the ["bibata-cursors" package](https://search.nixos.org/packages?channel=23.05&show=bibata-cursors&from=0&size=50&sort=relevance&type=packages&query=bibata):
```bash
home.file.".icons/default".source = "${pkgs.bibata-cursors}/share/icons/Bibata-Modern-Ice";
```

Update Nix expressions of channels:
```bash
nix-channel --update
```

And finally, build and activate the configuration:
```bash
home-manager switch
```

<br />

### Setting "Bibata Modern Ice" cursor theme for the system (specific to Vanilla OS)

This section is focused on Vanilla OS, that means that we will keep the "read-only" way for system files, so the changes will be applied on the home directory. If you use a different Linux OS:
- The cursor theme should be paste into `/usr/share/icons`.
- The GNOME Tweaks app should be installed using your OS package manager.

The first thing to do is downloading the [Bibata Modern Ice](https://github.com/ful1e5/Bibata_Cursor/releases/download/v2.0.3/Bibata-Modern-Ice.tar.gz) (you can also look for it at "gnome-look.org").

Go to the folder where you have downloaded the file and extract it. When the extraction has finished, check that you have one "Bibata-Modern-Ice" folder and it contains the "cursors" folder in addition to other files.

The next step is to copy and paste the "Bibata-Modern-Ice" folder to `~/.local/share/icons`, but first let's make sure that the `icons` folder exists:

- If this folder does not exist and we paste the cursor theme folder there, the `icons` folder will be created during the process, but it will contain the cursor theme folder content and not the folder itself ("Bibata-Modern-Ice" folder). This would not work because we need a folder with the name of the cursor theme, "Bibata-Modern-Ice" in this case.

- If this folder already exists, the following execution will show you this error `mkdir: cannot create directory ‘/home/[your-user-name]/.local/share/icons’: File exists`, so don't worry about this, we have what we wanted.

So, let's first create the "icons" folder:

```bash
mkdir ~/.local/share/icons
```

Now, with your terminal, go to the path where you extracted the cursor theme `.tar.gz` (or open that folder, then mouse left click and select "Open in Console") and execute this command:

```bash
cp -r ./Bibata-Modern-Ice ~/.local/share/icons
```

With that, the cursor theme has been added to the system, but we need to set it. In order to do that, we will use the "gnome-tweaks" app, but we have to install it first (if you didn't before). In Vanilla OS you can follow these steps.

So, let's install "gnome-tweaks" from Nix repository.
- If you install this app from a different repository (in Vanilla OS), you may not have the same result or it may not work.
- We could install this app using Home Manager, but let's use `apx` to make it more simple.

Execute this command to install the app.
```bash
apx install --nix gnome.gnome-tweaks
```

- We use `--nix` flag to specify the Nix package manager.
- To know the correct name for the package "gnome-tweaks", you can check it on [here](https://search.nixos.org/packages?channel=23.05&show=gnome.gnome-tweaks&from=0&size=50&sort=relevance&type=packages&query=gnome-tweaks).
- After installing gnome-tweaks, the application menu shortcut (called "Tweaks") will not appear until we reboot the system. If you don't want to reboot right now, you can execute `gnome-tweaks` command in your terminal to open the app.

Now, open gnome-tweaks and select the "Appearance" menu from the left menu (see image below). From there, look for the "Cursor" option from the "Theme" section, then select the drop-down menu (it should contain "Adwaita *(default)*") and change it by "Bibata Modern Ice".

![Cursor option is marked from Gnome Teaks app in order to show you where it is in the "Appearence" section](/Set%20a%20consistent%20cursor%20theme%20for%20nix%20UI%20packages/img/gnome-tweaks-appearance-clipped.png)

There you go! 👏 you have just change the cursor theme for both parts, Nix and system apps.

<br />

#### [(back to index)](#table-of-contents)

## Conclusion

After applying this configuration, now we get a consistent cursor theme.

We have learnt about Home Manager and GNOME Tweaks for this specific configuration, but they can be used for different settings, for example:

- We can configure the **Home Manager** file in order to set a list of applications to install. It is a more comfortable way for managing apps, and you can replicate this configuration if you move or copy the file content to a different machine.
- With every update of the GNOME desktop, we can find more additions to the system configuration. But some of them are not possible to change unless we use **GNOME Tweaks** (if we don't want to do it via terminal), like managing startup applications, changing system fonts or customizing mouse actions.

As we mentioned at the beginning, we have used Vanilla OS alongside Nix because they are the main pillars of this guide. The developers of Vanilla OS are focusing on immutability, so they achive that with read-only system files (alongside with other functionalities and tool) and we are respecting that porpouse while using Nix. You can learn more about Vanilla OS [here](https://vanillaos.org/).

If you want to set a cursor theme that is different to the ones seen on this guide, you can do it. The process will be the same, just look for the correct cursor package at the [Nix repository list](https://search.nixos.org/packages). And when you download a cursor theme zip (or .tar.gz), look for the folder the contains the "cursors" subfolder.

Finally, and just to let you know, if you are using containers on Vanilla OS, some **apt** apps use a different cursor theme and we are not able to configure it. So, this is something that I will check in newer versions of Vanilla OS (Orchid) and I will update this guide as soon as it is fixed.

<!-- Let me know if you think this guide is useful, you had any dificulty, or something is not working properly. I would improve this guide with your feedback 😉 -->

<!-- Si te gustó esta guía no dudes en dejar un comentario. También me gustaría saber si has tenido alguna dificultad, no entiendes algo o no te funciona. Así podré mejorar esta guía 😉 -->

<br />

#### [(back to index)](#table-of-contents)

## Acknowledgments

Some parts of this guide wouldn't be possible without the help of the [Vanilla OS Discord Community](https://discord.gg/vanilla-os-1023243680829681704), so I would like to thank:
- Feng Lengshun for making me discover Home Manager.
- "Monster" for letting me know how to initialize Nix in Vanilla OS.
- "Monster" and Pulkit Krishna for showing me how to add a cursor theme for the system in Vanilla OS (avoid abroot when possible).

<br />

#### [(back to index)](#table-of-contents)

## References

Here, you can find direct links to all the external information I have checked to complete this guide.

[1] Home Manager project repo: https://github.com/nix-community/home-manager

[2] Home Manager installation guide from Julian Hofer: https://julianhofer.eu/blog/01-silverblue-nix/#home-manager

[3] `optionsDocBook` warning message bug issue: https://github.com/nix-community/home-manager/issues/4273

[4] Configuration needed for setting a cursor theme using Home Manager: https://nixos.wiki/wiki/Cursor_Themes

[5] `home.file.".icons/default".source` meaning: https://mipmip.github.io/home-manager-option-search/?query=home.file.%3Cname%3E.source