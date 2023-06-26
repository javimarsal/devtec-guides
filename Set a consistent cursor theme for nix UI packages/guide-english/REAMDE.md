# Set a consistent cursor theme for Nix UI packages

(leer esta guía en Español 🇪🇸)

Some aspects to know before reading or following this guide:
- All these steps has been tested in a "out of the box" Vanilla OS installation in order to write this guide.
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

This will show this dialog before running the installation. Just press the `Y` key (yes option).

```bash
This will create a '.nix' folder in your home directory and set up some SystemD units to mount that folder at /nix before running the installation. Confirm 'y' to continue. [y/N]:
```

When the installation has finished, it is necessary to reboot.

(NO INCLUIR)
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
- obtenido de: https://discord.com/channels/1023243680829681704/1097468317477912646/1097786733485101088

```bash
nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
```

```bash
nix-channel --update
```

```bash
export NIX_PATH=$HOME/.nix-defexpr/channels:/nix/var/nix/profiles/per-user/root/channels${NIX_PATH:+:$NIX_PATH}
```

```bash
nix-shell '<home-manager>' -A install
```

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

This file can also be edited using the command `home-manager edit`, but the EDITOR variable must be recognize in order to execute that command. As this environment variable is not set on Vanilla OS, a quick solution would be to execute the command after setting the variable:

```bash
EDITOR=nano home-manager edit
```

After opening the `home.nix` file with nano editor, we can see that its content is similar to this:

```
{ config, pkgs, ... }:

{
    [CONTENT HERE]...
}
```

The last piece of code contains the line `[CONTENT HERE]...`, this is where we must add the following configuration and it can be placed at the end of the file, but inside the braces (`{ }`):

```bash
{ config, pkgs, ... }:

{
    home.pointerCursor = {
        name = "Adwaita";
        package = pkgs.gnome.adwaita-icon-theme;
    };

    home.file.".icons/default".source = "${pkgs.gnome.adwaita-icon-theme}/share/icons/Adwaita";
}
```

Now, a breaf explanation is here:
- The first part corresponds to `home.pointerCursor` and it contains:
    - `name`, it is the name of the cursor theme.
    - `package`, it is the name of the package that contains the cursor theme. So if the package is not installed, Home Manager will it install it for us.
- The second part beginning with `home.file` indicates the route of the cursor theme that we want to set.

Here's another example to apply the "Bibata-Modern-Amber" theme from the "pkgs.bibata-cursors" package:

```bash
{
    home.pointerCursor = {
        name = "Bibata-Modern-Amber";
        package = pkgs.bibata-cursors;
    };
    
    home.file.".icons/default".source = "${pkgs.bibata-cursors}/share/icons/Bibata-Modern-Amber";
}
```


To finish, downloads  the  Nix  expressions of all subscribed channels:
```bash
nix-channel --update
```

And build and activate configuration:
```bash
home-manager switch
```


Install gnome-tweaks from Nix repositories:

```bash
apx install --nix gnome.gnome-tweaks
```


## Atención

Al realizar esta configuración, por ejemplo, en gnome utilizando la aplicación gnome-tweaks, aparecen el resto de cursores del tema bibata, es decir, se han instalado desde los paquetes de Nix utilizando home-manager, pero al seleccionarlos desde gnome-tweaks (si no es el indicado según la configuración realizada) no surtirá efecto.

Algo similar ocurre con algunas aplicaciones flatpak, no es capaz de identificar el cursor seleccionado.

Para solucionar esto, debemos instalar los cursores de forma convencional, es decir, añadirlos a la carpeta `/usr/share/icons`. Así se puede conseguir tener el mismo cursor tanto en el sistema como en aplicaciones flatpak y de la paquetería Nix. Incluso si se desea, ahora se puede elegir un cursor distinto para las aplicaciones Nix y otro distinto para el resto de aplicaciones (esta parte igual no la pongo).


## References

La parte de Troubleshooting: https://nixos.wiki/wiki/Sway

https://nixos.wiki/wiki/Cursor_Themes