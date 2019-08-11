# Setup WinMerge in Visual Studio 2019 with Git

Add the following to the .gitconfig in you user folder (%userprofile%\.gitconfig).

`
[diff]
	tool = winmerge
[difftool "winmerge"]
	cmd = \"C:\\Program Files\\WinMerge\\WinMergeU.exe\" -e \"$LOCAL\" \"$REMOTE\"
[mergetool "winmerge"]
	cmd = \"C:\\Program Files\\WinMerge\\WinMergeU.exe\" \"$MERGED\"
`