# Set a consistent cursor theme for Nix UI packages

(see this guide in Spanish 🇪🇸)

Some aspects to know before reading or following this guide:
- All these steps has been tested in a fresh Vanilla OS installation in order to write this guide.
- Some routes contain `[your-user-name]`, so you should change that part with the user name that you used to login on your system.
    - For example, if a find the route `/home/[your-user-name]` in this guide, I should change it by `/home/javimarsal` in my case because `javimarsal` is my user name.

Table of contents:
-

<br />

## Specific Vanilla OS Configuration

### Initializing Nix

```bash
apx init --nix
```

This will show this dialog before running the installation. Just press the `Y` key.

```bash
This will create a '.nix' folder in your home directory and set up some SystemD units to mount that folder at /nix before running the installation. Confirm 'y' to continue. [y/N]:
```

When the installation has finished, it is necessary to reboot.

```
Installation finished!  To ensure that the necessary environment
variables are set, either log in again, or type

  . /home/[your-user-name]/.nix-profile/etc/profile.d/nix.sh

in your shell.
 SUCCESS  Installation complete. Reboot to start using nix.
```

<br />

### Installing Home Manager
This project (Home Manager) provides a basic system for managing a user environment using the Nix package manager together with the Nix libraries found in Nixpkgs. It allows declarative configuration of user specific (non global) packages and dotfiles.

Realizado en VanillaOS, similar en otros entornos que utilizan Nix.

Lo primero de todo, instalar home-manager: https://julianhofer.eu/blog/01-silverblue-nix/#home-manager

obtenido de: https://discord.com/channels/1023243680829681704/1097468317477912646/1097786733485101088

After installing Home Manager, this is shown in shell:

```
All done! The home-manager tool should now be installed and you can edit

    /home/[your-user-name]/.config/home-manager/home.nix

to configure Home Manager. Run 'man home-configuration.nix' to
see all available options.
```

This means that to configure Home Manager the `/home/[your-user-name]/.config/home-manager/home.nix` file must be edit. It can be done using your favourite editor or just use nano.

```bash
nano /home/[your-user-name]/.config/home-manager/home.nix
```

This file can also be edited using the command `home-manager edit`, but if the 

Poner EDITOR=nano, lo más probable es que esta variable de entorno no forme parte del sistema, así que, en esta ocasión, se declara y después se ejecuta el comando `home-manager edit`.

Ejecutar:
```bash
EDITOR=nano home-manager edit
```

Y añadir:

```
{ config, pkgs, ... }:

{
    # Para indicar el nombre del tema del cursor y el paquete de donde se obtiene
    home.pointerCursor = {
        name = "Bibata-Modern-Ice";
        package = pkgs.bibata-cursors;
    };

    # Para indicar la ruta donde se encuentra el tema del cursor
    home.file.".icons/default".source = "${pkgs.bibata-cursors}/share/icons/Bibata-Modern-Ice";
}
```

La parte de Troubleshooting: https://nixos.wiki/wiki/Sway

https://nixos.wiki/wiki/Cursor_Themes

Para terminar:
```bash
nix-channel --update
```

y después:
```bash
home-manager switch
```

Si el paquete del cursor no está instalado se instala automáticamente al ejecutar el comando anterior.

Install gnome-tweaks from Nix repositories:

```bash
apx install --nix gnome.gnome-tweaks
```


## Atención

Al realizar esta configuración, por ejemplo, en gnome utilizando la aplicación gnome-tweaks, aparecen el resto de cursores del tema bibata, es decir, se han instalado desde los paquetes de Nix utilizando home-manager, pero al seleccionarlos desde gnome-tweaks (si no es el indicado según la configuración realizada) no surtirá efecto.

Algo similar ocurre con algunas aplicaciones flatpak, no es capaz de identificar el cursor seleccionado.

Para solucionar esto, debemos instalar los cursores de forma convencional, es decir, añadirlos a la carpeta `/usr/share/icons`. Así se puede conseguir tener el mismo cursor tanto en el sistema como en aplicaciones flatpak y de la paquetería Nix. Incluso si se desea, ahora se puede elegir un cursor distinto para las aplicaciones Nix y otro distinto para el resto de aplicaciones (esta parte igual no la pongo).

Responder en:
- https://discourse.nixos.org/t/please-help-fix-ugly-cursor-in-apps-installed-with-nix-env/11797/2
- https://www.reddit.com/r/voidlinux/comments/rnsfpr/does_anyone_know_how_to_fix_inconsistent_cursor/