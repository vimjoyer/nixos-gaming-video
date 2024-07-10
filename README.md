# Gaming video

## enabling opengpl and GPU drivers

```nix
# configuration.nix

{ pkgs, ... }:

{

  hardware.graphics = {
    enable = true;
    enable32bit = true;
    }
  # hardware.opengl has beed changed to hardware.graphics

  services.xserver.videoDrivers = ["nvidia"];
  # services.xserver.videoDrivers = ["amdgpu"];

  hardware.nvidia.modesetting.enable = true;

}
```

## sync

```nix
# configuration.nix

{ pkgs, ... }:

{

  hardware.nvidia.prime = {
    sync.enable = true;

    # integrated
    amdgpuBusId = "PCI:6:0:0"
    # intelBusId = "PCI:0:0:0";

    # dedicated
    nvidiaBusId = "PCI:1:0:0";
  };

}
```

## offload + sync specialization

```nix
# configuration.nix

{ pkgs, ... }:

{

  hardware.nvidia.prime = {
    offload = {
      enable = true;
      enableOffloadCmd = true;
    };

    # integrated
    # intelBusId = "PCI:0:0:0";
    amdgpuBusId = "PCI:6:0:0"
    
    # dedicated
    nvidiaBusId = "PCI:1:0:0";
  };

  specialisation = {
    gaming-time.configuration = {

      hardware.nvidia = {
        prime.sync.enable = lib.mkForce true;
        prime.offload = {
          enable = lib.mkForce false;
          enableOffloadCmd = lib.mkForce false;
        };
      };

    };
  };

}
```

## getting ids

```bash
nix shell nixpkgs#pciutils -c lspci | grep ' VGA '"
```

## wrappers

```nix
# configuration.nix

{ pkgs, ... }:

{

  programs.steam.enable = true;
  programs.steam.gamescopeSession.enable = true;

  environment.systemPackages = with pkgs; [
    mangohud
  ];

  programs.gamemode.enable = true;

}
```


## protonup

```nix
# configuration.nix

{ pkgs, ... }:

{

  environment.systemPackages = with pkgs; [
    protonup
  ];
  
  home.sessionVariables = {
    STEAM_EXTRA_COMPAT_TOOLS_PATHS =
      "\${HOME}/.steam/root/compatibilitytools.d";
  };

}
```

```nix
# home.nix

{ pkgs, ... }:

{

  home.packages = with pkgs; [
    protonup
  ];

  home.sessionVariables = {
    STEAM_EXTRA_COMPAT_TOOLS_PATHS =
      "\\\${HOME}/.steam/root/compatibilitytools.d";
  };

}
```

```bash
protonup -d "~/.steam/root/compatibilitytools.d/"
```

## nixos-hardware

https://github.com/NixOS/nixos-hardware
