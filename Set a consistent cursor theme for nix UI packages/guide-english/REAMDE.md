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

## Setting a different theme for the whole system

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

### Setting "Bibata Modern Ice" cursor theme for the system

You can look for the [Bibata Modern Ice](https://www.gnome-look.org/p/1197198) cursor theme from [gnome-look.org](https://www.gnome-look.org). Just go to the "Files" section and download the "Bibata-Modern-Ice.tar.gz" file.

<!-- TODO -->
#### (specific to Vanilla OS)
In other Linux distros you will only have to copy and paste the cursor theme folder to `/usr/share/icons`, but Vanilla OS does not permit to copy the folder to that directory because system files are read only. We could achive that by using the `abroot` command from Vanilla OS, but we would be not following their devs recommendation: "use only abroot if you are an advanced user and if what you want to do can't be done in a different way".

So yeah, we can do it in a different way without writing in the system files. Instead of copying the cursor theme to that path, we will copy it to `~/.local/share/icons`.

<!-- TODO cambiar a '~/.local/share/icons' -->
<!-- crear directorio -->
<!-- copiar carpeta del tema de iconos -->
<!-- así no es necesario acceder al sistema con abroot ni reiniciar -->

<!-- Nueva forma -->
The `~/.local/share` path exists, but the "icons" folder might not exists depending if you previously installed some program that created this folder automatically or not.

So, we need to check if the "icons" folder exists:
- If this folder does not exist and we copy the cursor theme folder to `~/.local/share/icons` the "icons" folder will be created during the process, but it will contain the content of the cursor theme folder and not the folder itself. This would not work because we need a folder with the name of the cursor theme, "Bibata-Modern-Ice" in this case.

So, let's first create the "icons" folder:
- If this folder already exists, the following execution will show you this error `mkdir: cannot create directory ‘/home/[your-user-name]/.local/share/icons’: File exists
`, so don't worry about this, we have what we wanted.
```bash
mkdir ~/.local/share/icons
```

Now, make sure you downloaded the cursor theme that we mentioned previously (if not, this is the [direct link](https://github.com/ful1e5/Bibata_Cursor/releases/download/v2.0.3/Bibata-Modern-Ice.tar.gz) to download it) and extract it, so you have now a "Bibata-Modern-Ice" folder and it contains:
- "cursor.theme" (file)
- "cursors" (folder)
- "index.theme" (file)

With your terminal, go to the path where you have the cursor theme folder and execute the copy command:

```bash
cp -r ./Bibata-Modern-Ice ~/.local/share/icons
```


Now you need to have the "gnome-tweaks" app install. In Vanilla OS you can follow these steps.

This must be installed from nix repository.
- If you use this app from a different repository, you won't have the same result or it won't work.

We could install this app using home-manager, but let's use `apx` to make it more simple:

Funciona
```bash
apx install --nix gnome.gnome-tweaks
```

- We use `--nix` flag to specify the nix package manager.
- To know the correct name for the package "gnome-tweaks" we can search for it [here](https://search.nixos.org/packages?channel=23.05&show=gnome.gnome-tweaks&from=0&size=50&sort=relevance&type=packages&query=gnome-tweaks).

After installing gnome-tweaks, the application menu shortcut will not appear until we reboot. If you don't want to reboot right now, you can execute `gnome-tweaks` command in your terminal to open the app.


<!-- TODO -->
Once we have installed "gnome-tweaks" app, we can proceed...

Open "gnome-teaks" and change the cursor theme in the "Appearance" menu...

After that, move the cursor to the desktop or to the top bar menu to see that the cursor appearance changes.

<!-- TODO: comprobar esto -->
Parece que las aplicaciones del contenedor apt aplican el tema de cursores de la misma forma que las aplicaciones de Nix, es decir, parece que comparten la configuración de Nix.

<!-- Volver a hacer la prueba porque no puedo descargar aplicaciones flatpak en la maquina virtual, y no aparece el cursor en aplicaciones en contenedores, como es el caso de gnome-tweaks -->

<!-- TODO: leer y ver si se puede actualizar o quitar -->
(specific to Vanilla OS)
If you have some Vanilla OS containers running (arch, fedora, ...) maybe this configuration will not work if you have some apps already installed or you install some apps in the future on these containers.

The solution I know is to reinitialize the containers in order to apply the cursor theme configuration.

I don't if there's a better solution, but this is what I recommend to you.

(Probar esto bien, mirar si al hacer esto, después cambiar cursor y ver si sigue funcionando)

**Atención**: si funciona después de instalar nuevas aplicaciones y sin reinicializar los contenedores (ahora no funciona)
- Probar si es cosa de los contenedores de Vanilla OS
    - En contenedores apt: si la app es instalada después de cambiar el tema de cursores desde gnome-tweaks, la app tendrá ese tema. Una vez instalada, si cambiamos el tema (desde gnome-tweaks) no se aplican los cambios a no ser que reinstalemos la aplicación.
    - En otros contenedores como aur o dnf: se aplica el tema de cursores de la configuración realizada para Nix.
- Esto no es un problema de la configuración, tiene que ver más bien con los contenedores de Vanilla OS (pero se puede arreglar?)
- Probar esto en una nueva imagen (VanillaOS-22-limpio 2)
    - Instalar tema de cursores descargando el zip y aplicándolo desde gnome-tweaks.
    - No configurar nada de nix.
    - Mirar cómo se comporta con contenedores de apt y aur
        - Aplicando el tema e instalar apps.
        - Teniendo la app instalada y cambiar el tema.
        - Hacer capturas y pasarlo por el discord de Vanilla.
    - (No funciona)


<!-- TODO: To Change -->
(Desactualizado, recuperar partes)
## Atención

Al realizar esta configuración, por ejemplo, en gnome utilizando la aplicación gnome-tweaks, aparecen el resto de cursores del tema bibata, es decir, se han instalado desde los paquetes de Nix utilizando home-manager, pero al seleccionarlos desde gnome-tweaks (si no es el indicado según la configuración realizada) no surtirá efecto.

Algo similar ocurre con algunas aplicaciones flatpak, no es capaz de identificar el cursor seleccionado.

Para solucionar esto, debemos instalar los cursores de forma convencional, es decir, añadirlos a la carpeta `/usr/share/icons`. Así se puede conseguir tener el mismo cursor tanto en el sistema como en aplicaciones flatpak y de la paquetería Nix. Incluso si se desea, ahora se puede elegir un cursor distinto para las aplicaciones Nix y otro distinto para el resto de aplicaciones (esta parte igual no la pongo).
<!--  -->

Si te gustó esta guía no dudes en dejar un comentario. También me gustaría saber si has tenido alguna dificultad, no entiendes algo o no te funciona. Así podré salvarle para mejorar esta guía 😉

## Acknowledgments
Some parts of this guide wouldn't be possible without the help of the [Vanilla OS Discord Community](https://discord.gg/vanilla-os-1023243680829681704), so I would like to thank:
- Feng Lengshun for making me discover Home Manager.
- "Monster" for letting me know how to initialize Nix in Vanilla OS.
- "Monster" and Pulkit Krishna for showing me how to add a cursor theme in Vanilla OS.

## References

[1] Home Manager project repo: https://github.com/nix-community/home-manager

[2] Home Manager installation guide from Julian Hofer: https://julianhofer.eu/blog/01-silverblue-nix/#home-manager

[3] `optionsDocBook` warning message bug issue: https://github.com/nix-community/home-manager/issues/4273

[4] Configuration needed for setting a cursor theme using Home Manager: https://nixos.wiki/wiki/Cursor_Themes

[5] `home.file.".icons/default".source` meaning: https://mipmip.github.io/home-manager-option-search/?query=home.file.%3Cname%3E.source