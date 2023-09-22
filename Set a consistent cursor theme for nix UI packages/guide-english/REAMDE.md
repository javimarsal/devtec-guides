# Set a consistent cursor theme for Nix UI packages

(leer esta guía en Español 🇪🇸)

Table of contents:
1. Why this guide? (TODO)
1. 

Some aspects to know before reading or following this guide:
- All these steps has been tested in an "out of the box" Vanilla OS installation.
- As I just mentioned, I'm using Vanilla OS, so some parts might be different or not necessary to do in other Linux distros. This parts are marked with `(specific to Vanilla OS)`.
- Some paths contain `[your-user-name]`, so you should change that part with the user name that you used to login on your system.
    - For example, if I find the path `/home/[your-user-name]` in this guide, I should change it by `/home/javimarsal` in my case because `javimarsal` is my user name on my computer.
- Other paths start with `~/` as `~/.local/share/icons`. The `~` symbol corresponds to `/home/[your-user-name]`.
    - I think it is necessary to know, maybe you have learnt this right now!

The most part of the configuration is done using the terminal, specifically "Console" (the terminal emulator from GNOME), this is completly valid and the default one on Vanilla OS. So, keep your terminal opened and let's start with this guide!
- If you already have initialized Nix and installed Home Manager, you can start following this guide from the ["Setting cursor theme for Nix apps"](#setting-cursor-theme-for-nix-apps) section.

<br />

<!-- TODO: complete this section -->
## Why this guide?

## Initializing Nix (specific to Vanilla OS)

The first step is to initialize Nix. If you haven't do it before, execute this command:

```bash
apx init --nix
```

This will request your password, and after that a message will appear letting you know that a `.nix` folder will be created in your home directory. Just press the `Y` key (yes option) to process with the installation.

When the installation has finished, it is necessary to reboot in order to apply all changes in the future partition (a Vanilla OS feature).

<br />

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

<!-- TODO: no poner lo que sale en la terminal (resultado), sino explicarlo -->
After installing Home Manager, we can use this tool to set the cursor theme for Nix apps. In the next section we will use the `home-manager edit` command to achieve that.

<br />

## Setting cursor theme for Nix apps

Before following this section make sure you have [initialized Nix](#initializing-nix-specific-to-vanilla-os) and [installed Home Manager](#installing-home-manager). So now, let's set the configuration to show the same cursor theme as the system one (Adwaita by default) for Nix UI applications.

To make some configuration, we need to edit the file:
- `/home/[your-user-name]/.config/home-manager/home.nix`

It can be edited by using your favourite editor, but after installing Home Manager the `home-manager edit` command is now available for us, so let's use it to edit that file.

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

The last piece of code contains the line `[CONTENT HERE]...`, this is just to show you where to add the cursor theme configuration. There may be some different content already, so we must respect it.

So, following this guide [[4]](#references) from [NixOS Wiki](https://nixos.wiki/wiki/Main_Page), we need to add the following line in order apply the "Adwaita" cursor theme for Nix apps that work with an UI:

```bash
home.file.".icons/default".source = "${pkgs.gnome.adwaita-icon-theme}/share/icons/Adwaita";
```

Now, save and exit the file. If you're using nano, then press `Ctrl` + `O` and `enter` to save the file, and `Ctrl` + `X` to exit the file.

After researching for a while, I noticed that this is the configuration that fits the best on Vanilla OS.
- You could apply a different configuration and use a cursor theme URL instead (that will work), but it may add some cursor theme conflicts while using Vanilla OS apps inside containers. So, this will help you if you are using containers right now or in the future.

Now, a brief explanation about this configuration [[5]](#references). We can identify two parts:
- Before equals `=` symbol: this is the option that corresponds to the path containing the cursor theme.
- After equals `=` symbol: this is the path where the first option points to. We need two things to form this path:
    - The package name: this is the actual cursor theme and it will be downloaded from the Nix package collection (pkgs). So, in this case, the package name for the "Adwaita" cursor theme is `gnome.adwaita-icon-theme`. You can look for another cursor themes [here](https://search.nixos.org/packages?channel=23.05&from=0&size=50&sort=relevance&type=packages&query=gnome.adwaita-icon-theme).
    - The cursor theme name: in this case, the name is "Adwaita". You can [download](https://github.com/GNOME/adwaita-icon-theme/releases/tag/43) the cursor theme to check that name (just extract the file and look for the name of the folder containing the "cursors" subfolder, it should be "Adwaita").

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

## Setting a different theme for Nix and system

At this point, you have set the same cursor theme for Nix apps and the system if "Adwaita" was your default cursor theme, and this may be enough for you.

In the following sections I will show you how to set a different cursor theme (Bibata Modern Ice) for Nix apps and the system. So, if you keep reading you will learn how to configure both parts.

### Setting "Bibata Modern Ice" cursor theme for Nix apps

If you closed your terminal after completing the previous sections, you will have to export the `EDITOR` variable as [we did before](#setting-cursor-theme-for-nix-apps).

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

### Setting "Bibata Modern Ice" cursor theme for the system (specific to Vanilla OS)

This section is focused on Vanilla OS, that means that we will keep the "read-only" way for system files, so the changes will be applied on the home directory. If you use a different Linux OS:
- The cursor theme should be paste into `/usr/share/icons`.
- The GNOME Tweaks app should be installed using your OS package manager.

The first thing to do is downloading the [Bibata Modern Ice](https://github.com/ful1e5/Bibata_Cursor/releases/download/v2.0.3/Bibata-Modern-Ice.tar.gz) (you can also look for it at "gnome-look.org").

Go to the folder where you have downloaded the file and extract it. Check you have one "Bibata-Modern-Ice" folder and it contains the "cursors" folder in addition to other files.

The next step is to copy and paste the "Bibata-Modern-Ice" folder to `~/.local/share/icons`, but first let's make sure that `icons` folder exists:
- If this folder does not exist and we paste the cursor theme folder there, the `icons` folder will be created during the process, but it will contain the cursor theme folder content and not the folder itself. This would not work because we need a folder with the name of the cursor theme, "Bibata-Modern-Ice" in this case.

So, let's first create the "icons" folder:
- If this folder already exists, the following execution will show you this error `mkdir: cannot create directory ‘/home/[your-user-name]/.local/share/icons’: File exists`, so don't worry about this, we have what we wanted.
```bash
mkdir ~/.local/share/icons
```

Now, with your terminal, go to the path where you have the cursor theme folder (or open that folder, then mouse left click and select "Open in Console") and execute this command:

```bash
cp -r ./Bibata-Modern-Ice ~/.local/share/icons
```

<!-- por aquí -->
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

Now, open gnome-tweaks and select the "Appearance" menu from the left menu (see image). From there, look for the "Cursor" option from the "Theme" section, then select the drop-down menu (it should contain "Adwaita *(default)*") and change it "Bibata Modern Ice".

<!-- TODO: poner imagen de tweaks -->

There you go! 👏 you have just change the cursor theme for both parts, Nix and system apps.

<br />

<!-- TODO: To Change -->
## Conclusion

Al realizar esta configuración, por ejemplo, en gnome utilizando la aplicación gnome-tweaks, aparecen el resto de cursores del tema bibata, es decir, se han instalado desde los paquetes de Nix utilizando home-manager, pero al seleccionarlos desde gnome-tweaks (si no es el indicado según la configuración realizada) no surtirá efecto.

Algo similar ocurre con algunas aplicaciones flatpak, no es capaz de identificar el cursor seleccionado.

Para solucionar esto, debemos instalar los cursores de forma convencional, es decir, añadirlos a la carpeta `/usr/share/icons`. Así se puede conseguir tener el mismo cursor tanto en el sistema como en aplicaciones flatpak y de la paquetería Nix. Incluso si se desea, ahora se puede elegir un cursor distinto para las aplicaciones Nix y otro distinto para el resto de aplicaciones (esta parte igual no la pongo).
<!--  -->

Si te gustó esta guía no dudes en dejar un comentario. También me gustaría saber si has tenido alguna dificultad, no entiendes algo o no te funciona. Así podré salvarle para mejorar esta guía 😉

<br />

## Acknowledgments
Some parts of this guide wouldn't be possible without the help of the [Vanilla OS Discord Community](https://discord.gg/vanilla-os-1023243680829681704), so I would like to thank:
- Feng Lengshun for making me discover Home Manager.
- "Monster" for letting me know how to initialize Nix in Vanilla OS.
- "Monster" and Pulkit Krishna for showing me how to add a cursor theme in Vanilla OS.

<br />

## References

Here, you can find direct links to all the external information I have checked to complete this guide.

[1] Home Manager project repo: https://github.com/nix-community/home-manager

[2] Home Manager installation guide from Julian Hofer: https://julianhofer.eu/blog/01-silverblue-nix/#home-manager

[3] `optionsDocBook` warning message bug issue: https://github.com/nix-community/home-manager/issues/4273

[4] Configuration needed for setting a cursor theme using Home Manager: https://nixos.wiki/wiki/Cursor_Themes

[5] `home.file.".icons/default".source` meaning: https://mipmip.github.io/home-manager-option-search/?query=home.file.%3Cname%3E.source