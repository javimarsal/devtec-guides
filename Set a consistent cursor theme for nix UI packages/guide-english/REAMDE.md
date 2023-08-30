# Set a consistent cursor theme for Nix UI packages

(leer esta guía en Español 🇪🇸)

Table of contents:
- Why this guide? (TODO)

Some aspects to know before reading or following this guide:
- All these steps has been tested in an "out of the box" Vanilla OS installation.
- As I just mentioned, I'm using Vanilla OS, so some parts might be different or not necessary to do in other Linux distros. This parts are marked with `(specific to Vanilla OS)`.
- Some paths contain `[your-user-name]`, so you should change that part with the user name that you used to login on your system.
    - For example, if I find the path `/home/[your-user-name]` in this guide, I should change it by `/home/javimarsal` in my case because `javimarsal` is my user name on my computer.
- Other paths start with `~/` as `~/.local/share/icons`. The `~` symbol corresponds to `/home/[your-user-name]`.
    - I think it is necessary to know, maybe you have learnt this right now!

The most part of the configuration is done using the terminal, specifically "Console" (the terminal emulator from GNOME), this is completly valid and the default one on Vanilla OS. So, keep your terminal opened and let's start with this guide!

<br />

<!-- TODO -->
## Why this guide?

## Initializing Nix (specific to Vanilla OS)

The first step is to initialize Nix. If you haven't do it before, 

```bash
apx init --nix
```
Te pedirá tu contraseña.

This will show this dialog before running the installation. Just press the `Y` key (yes option).

```bash
This will create a '.nix' folder in your home directory and set up some SystemD units to mount that folder at /nix before running the installation. Confirm 'y' to continue. [y/N]:
```

When the installation has finished, it is necessary to reboot. Maybe the system will require your password in order to proceed.

(NO INCLUIR)
```
Installation finished!  To ensure that the necessary environment
variables are set, either log in again, or type

  . /home/[your-user-name]/.nix-profile/etc/profile.d/nix.sh

in your shell.
 SUCCESS  Installation complete. Reboot to start using nix.
```

<br />

## Installing Home Manager
This project (Home Manager) provides a basic system for managing a user environment using the Nix package manager together with the Nix libraries found in Nixpkgs. It allows declarative configuration of user specific (non global) packages and dotfiles.

Realizado en VanillaOS, similar en otros entornos que utilizan Nix.

Lo primero de todo, instalar home-manager: https://julianhofer.eu/blog/01-silverblue-nix/#home-manager
- obtenido de: https://discord.com/channels/1023243680829681704/1097468317477912646/1097786733485101088

```bash
nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
```

```bash
nix-channel --update
```

<!-- Si es necesario para encontrar los paquetes necesarios de Nix?? Buscar -->
```bash
export NIX_PATH=$HOME/.nix-defexpr/channels:/nix/var/nix/profiles/per-user/root/channels${NIX_PATH:+:$NIX_PATH}
```

```bash
nix-shell '<home-manager>' -A install
```

<!-- TODO: Buscar el error, para explicarlo bien -->
Don't worry about some red text, this is just some warnings of Nix (due to `optionsDocBook is deprecated since 23.11 and will be removed in 24.05`).

<!-- TODO: no poner lo que sale en la terminal (resultado), sino explicarlo -->
After installing Home Manager, this is shown in shell:

```bash
All done! The home-manager tool should now be installed and you can edit

    /home/[your-user-name]/.config/home-manager/home.nix

to configure Home Manager. Run 'man home-configuration.nix' to
see all available options.
```

This means that the `/home/[your-user-name]/.config/home-manager/home.nix` file must be edit to configure Home Manager. It can be done using your favourite editor or just use nano.

```bash
nano /home/[your-user-name]/.config/home-manager/home.nix
```

## Setting cursor theme for Nix apps

So now, let's set the configuration to show the same cursor theme as the system on (Adwaita by default) for Nix UI applications.

As seen before, we need to edit this file:
- `/home/[your-user-name]/.config/home-manager/home.nix`

It can be edited using the nano editor, but by installing Home Manager this command is now available: `home-manager edit`

If you try that, maybe a similar error to this will be shown:
```bash
Please set the $EDITOR environment variable
```

That means that the `EDITOR`` variable must be recognize in order to execute that command. As this environment variable is not set on Vanilla OS by default, a quick solution would be to execute the command after setting the variable temporarily:

```bash
EDITOR=nano home-manager edit
```

(You can take a look to this [guide](https://linuxhandbook.com/export-command/) later in order to understand how to define environment variables)

After opening the `home.nix` file with nano editor, we can see that its content is similar to this:

```
{ config, pkgs, ... }:

{
    [CONTENT HERE]...
}
```

The last piece of code contains the line `[CONTENT HERE]...`, this is where we must add the following configuration and it can be placed at the end of the file, but inside the braces (`{ }`):

Siguiendo este [enlace](https://nixos.wiki/wiki/Cursor_Themes) (prefireblemente y más fácil):

```bash
home.file.".icons/default".source = "${pkgs.gnome.adwaita-icon-theme}/share/icons/Adwaita";
```

<!-- esto ya no es necesario -->
Cursor (en la sección de "tags" y seleccionando la versión de gnome que tiene el SO): https://github.com/GNOME/adwaita-icon-theme/releases/tag/43
<!--  -->

<!-- TODO: cambiar, ajustarlo a lo actual -->
Now, a breaf explanation:
- The first part corresponds to `home.pointerCursor` and it contains:
    - `name`, it is the name of the cursor theme.
    - `package`, it is the name of the package that contains the cursor theme. So if the package is not installed, Home Manager will it install it for us.
- The second part beginning with `home.file` indicates the path of the cursor theme that we want to set.

Now press `Ctrl` + `O` and `enter` to save the file, and then `Ctrl` + `X` to exit the file. To finish, downloads  the  Nix expressions of all subscribed channels (it might no be necessary because we did it previously):
```bash
nix-channel --update
```

And build and activate configuration:
```bash
home-manager switch
```

HASTA AQUÍ FUNCIONA, y es lo básico y necesario para tener el tema de Adwaita, ya que es el cursor que viene de base.

Taking into account:
- If you had an application running and you don't see any change, try closing and opening it again.
- If any application shows the cursor in a different size than the normal one, just reboot your system. That should fixed the correct size and now everything should look nice so far!
- Some Nix apps will use the System cursor theme, so that's fine if you want it like that.

## Take into account

(This part of the guide is optional, but maybe you would like to know what it's about if this situation happens to you in the future)

At this point, we have set the "Adwaita" cursor theme for Nix apps that use UI as audacity, discord, vscode, ... and everything looks good. We have achive the objective to have the same cursor theme in all our System.

But this is working because the cursor theme we have set to Nix apps was exactly the same as the system default one (Adwaita). So we have to take into account the following:
- If we prefer another cursor theme (different to the System's default) it will be necessary to change the cursor theme for both parts, Nix apps (done previously) and the System itself.

So now, let's see an example where we are going to set the "Bibata Modern Ice" cursor theme across all the System.

### Setting "Bibata Modern Ice" cursor theme for Nix apps

```bash
home.file.".icons/default".source = "${pkgs.bibata-cursors}/share/icons/Bibata-Modern-Ice";
```

y luego
```bash
nix-channel --update
```

And build and activate configuration:
```bash
home-manager switch
```

### Setting "Bibata Modern Ice" cursor theme for the System

You can look for the [Bibata Modern Ice](https://www.gnome-look.org/p/1197198) cursor theme from [gnome-look.org](https://www.gnome-look.org). Just go to the "Files" section and download the "Bibata-Modern-Ice.tar.gz" file.

<!-- TODO -->
#### (specific to Vanilla OS)
In other Linux distros you will only have to copy and paste the cursor theme folder to `/usr/share/icons`, but Vanilla OS does not permit to copy the folder to that directory because System files are read only. We could achive that by using the `abroot` command from Vanilla OS, but we would be not following their devs recommendation: "use only abroot if you are an advanced user and if what you want to do can't be done in a different way".

So yeah, we can do it in a different way without writing in the System files. Instead of copying the cursor theme to that path, we will copy it to `~/.local/share/icons`.

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


<!-- TODO -->
Once we have installed "gnome-tweaks" app, we can proceed...

Open "gnome-teaks" and change the cursor theme in the "Appearance" menu...

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

## References

La parte de Troubleshooting: https://nixos.wiki/wiki/Sway

https://nixos.wiki/wiki/Cursor_Themes