# Windows Terminal: Add customized prompt with Powerline

Source: https://docs.microsoft.com/en-us/windows/terminal/tutorials/powerline-setup

Themes
- Avit works well with Meslo
- Paradox works wel with the Cascadia font


Steps

1. Install Windows Terminal from Store
2. Install a Powerline font like
    - Meslo (https://github.com/ryanoasis/nerd-fonts / https://github.com/powerline/fonts)
    - Cascadia Code PL or Cascadia Mono PL (https://github.com/microsoft/cascadia-code)
3. Install the posh-git and oh-my-posh-git PowerShell modules

    ```powershell
    Install-Module posh-git -Scope CurrentUser
    Install-Module oh-my-posh -Scope CurrentUser
    ```

4. Update your PowerShell profile script with the following statements to import the posh modules. (Use `code $profile` to open the profile script in VS Code.)

    ```powershell
    Import-Module posh-git
    Import-Module oh-my-posh
    Set-PoshPrompt -Theme Avit
    ```

5. Configure a Powerline font for PowerShell or as the default in the Windows Terminal settings

    ```json
    "defaults":
    {
        "fontFace": "MesloLGMDZ Nerd Font Mono",
        "fontSize": 11
    },
    ```
            