# Windows Terminal: Add customized prompt with Powerline

Source: https://docs.microsoft.com/en-us/windows/terminal/tutorials/powerline-setup

## Themes
- Avit works well with Meslo
- Paradox works well with the Cascadia font


## Steps

1. Install Windows Terminal from the Microsoft Store
1. Install oh-my-posh from the Microsoft Store

1. Install a Powerline font like
    - Meslo - Meslo LG M DZ Regular Nerd Font Complete Mono (Windows Compatible)  
      https://github.com/ryanoasis/nerd-fonts/releases
    - Cascadia Code PL or Cascadia Mono PL  
      https://github.com/microsoft/cascadia-code
1. Configure the font for PowerShell or as the default in the Windows Terminal settings
    ```json
    "defaults": 
    {
        "font": 
        {
            "face": "MesloLGMDZ Nerd Font Mono",
            "size": 11
        }
    },
    ```

1. Open the PowerShell profile script
   ```powershell
   code $Profile
   ```
1. Add the following to the end of the PowerSehll profile file to set the avit theme.
   ```powershell
   oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\avit.omp.json" | Invoke-Expression
   ```

## Visual Studio Code

Follow these steps to configure the font in the integrated VS Code terminal:

1. Open: File > Preferences > Settings
1. Search for `terminal.integrated.fontFamily`
1. Set `MesloLGMDZ Nerd Font Mono` as the font family