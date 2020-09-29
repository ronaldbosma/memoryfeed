# Azure DevOps Build Server Issues

## Slow builds and 'the file exists' errors

In some cases builds failed with a 'The file exists' error or builds were very slow. The solution for these problems was to clear the temp folder of the build user. E.g. `C:\Users\<build-user>\AppData\Local\Temp`. Also see [https://michaelellerbeck.com/2012/02/14/when-creating-a-temp-file-system-io-ioexception-the-file-exists-error/](https://michaelellerbeck.com/2012/02/14/when-creating-a-temp-file-system-io-ioexception-the-file-exists-error/).