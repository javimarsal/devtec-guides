# Set a consistent cursor theme for nix UI packages

Realizado en VanillaOS, similar en otros entornos que utilizan Nix.

Lo primero de todo, instalar home-manager: https://julianhofer.eu/blog/01-silverblue-nix/#home-manager

obtenido de: https://discord.com/channels/1023243680829681704/1097468317477912646/1097786733485101088

Poner EDITOR=nano, lo más probable es que esta variable de entorno no forme parte del sistema, así que, en esta ocasión, se declara y después se ejecuta el comando `home-manager edit`.

Ejecutar:
```zsh
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
```zsh
nix-channel --update && home-manager switch
```

Si el paquete del cursor no está instalado se instala al ejecutar el comando anterior.

Responder en:
- https://discourse.nixos.org/t/please-help-fix-ugly-cursor-in-apps-installed-with-nix-env/11797/2
- https://www.reddit.com/r/voidlinux/comments/rnsfpr/does_anyone_know_how_to_fix_inconsistent_cursor/