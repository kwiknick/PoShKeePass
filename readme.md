# PowerShell KeePass

[PoShKeePass](https://www.powershellgallery.com/packages/PoShKeePass) is a PowerShell module that combines the ease of the PowerShell cli and the extensibility of the [KeePassLib API](http://keepass.info/help/v2/setup.html) to provide a powerful and easy to use management and automating platform for [KeePass](http://keepass.info/) databases.

## Features

1. **Database Configuration Profiles** - Supports mutliple databases and authentication options.
2. **Getting, Creating, Updating, and Removing KeePass Entries and Groups** - All of these perform as much automatic database authentication as possible using the database configuration profile. For databases that use a masterkey (password) it will prompt for it.
3. **Generating KeePass Passwords** - Supports most character sets and advanced keepass options. Also supports creating password profiles that can be specified to create a new password with the same rule set.

## Getting Started

### Install

```powershell
Install-Module -Name PoShKeePass
```

### Documentation

Please check out our [Getting Started](https://github.com/PSKeePass/PoShKeePass/wiki/Getting-Started) documentation on our [wiki](https://github.com/PSKeePass/PoShKeePass/wiki).

## Important Notes & Reminders

1. Please always keep up to date **backups** of your KeePass database files .kdbx and .key files.
2. The module uses the KeePassLib 2.3.x which is embedded in the module file.
3. This module was built and tested in PowerShell 5 on Windows 10 but should work in PowerShell 4 and Windows 8.1 and Server 2012 R2 and up. It may work in some earlier versions but is currently untested and not supported. If you come across issues create an issue and I will look into fixing it or create a pull request.
4. There is an underlying framework that I wrote into the module to make all of the api calls that I will eventually expose for advanced scripting.

## Changelog

Please review the [changelog document](https://github.com/PSKeePass/PoShKeePass/blob/master/changelog.md) for a full history.

## v2.1.1.8

### Many fixes, features and improvements, please note the **Breaking Changes** Below

* Fix [#129](https://github.com/PSKeePass/PoShKeePass/issues/129) - Can now pass Credential Object to `-MasterKey` Parameter
* Fix/Implemented [#69](https://github.com/PSKeePass/PoShKeePass/issues/69) - All primary Functions return a Powershell object rather than a KeePass Object **This Includes Breaking changes!**.
  * **Breaking:**
    * Since a powershell object is now returned, in order to access the keepass object a child property has been added to the ps object, `.KPEntry` and `.KPGroup`.
    * Deprecated the `-AsPlainText` parameter on the `Get-KeePassGroup` function, the call will still work but it will present a warning message. This is being removed as it is no longer necessary.
  * **Non-Breaking:**
    * Moved how database profile name was being added to the ps object for better performance on conversion.
* Implemented [#93](https://github.com/PSKeePass/PoShKeePass/issues/93) - `Get-KeePassEntry` Now supports `-Title` and `-UserName` parameters also via pipeline.
* Normalized Error handling to remove repetitive code
* Converted extraneous logic to parameter splatting
* Code formatting and removed explict parameter attributes where not necessary.
* Updated Object creation to use the `hashtable` method for performance over the `New-Object` + `Add-Memeber`.
* Fix [#44](https://github.com/PSKeePass/PoShKeePass/issues/44) - Pipeline now Works for `Remove-KeePassDatabaseConfiguration`.
* Implemented [#141](https://github.com/PSKeePass/PoShKeePass/issues/141) - Much stronger Pipeline support.
  * `-DatabaseProfileName` no longer needs to be specified to a KPPSObject pipeline recieving function.
    * Example: `Get-KeePassEntry -Title 'test' -DatabaseProfileName 'profile' | Remove-KeePassEntry`
  * All parent and object paths now are recieved by the pipeline which of course can be overridden by specifing the parameter.
* Fixed [#140](https://github.com/PSKeePass/PoShKeePass/issues/140) and [#138](https://github.com/PSKeePass/PoShKeePass/issues/138) - by removing the `EncodeKeePassLib.ps1` script file as it is no longer in use.
* Fixed [#144](https://github.com/PSKeePass/PoShKeePass/issues/144) - Removed Faultly logic which allowed for the KeePass Icon to get set to blank while updating an object.
* Implemented [#143](https://github.com/PSKeePass/PoShKeePass/issues/143) There are no more dynamic parameters! So all of the gitches have left with them. They still support tab completion by using `Register-ArgumentCompleter`.
  * **Breaking Change** as this is only supported in powershell v5 and up, auto complete will not work in older versions.
* Implemented [#118](https://github.com/PSKeePass/PoShKeePass/issues/118) - by adding support for keepasslib version `2.39.1`
  * The new file format version between the previous version of `2.34` and the latest apears to be much slower on some operations.
  * Testing the new Lib version against the previously suported version `2.34` all worked and appears to be backwards compatible. Also it does not upgrade the file format version.
  * Version can easily flipped back by modifying the global variable in the `.psm1` file.
  * This fixes [#131](https://github.com/PSKeePass/PoShKeePass/issues/131).
* Fix [#145](https://github.com/PSKeePass/PoShKeePass/issues/145) - Updating a KeePass Entry now updates the modification time in UTC.
  * **Breaking Change** - Renamed the `LastAccessTime` and `LastModificationTime` properties to `LastAccessTimeUtc` and `LastModificationTimeUtc` to reflect that they are in UTC.
* Addressed [#88](https://github.com/PSKeePass/PoShKeePass/issues/88) - `Get-KeePassEntry`
  * Since a Ps object is now always returned, all fields but the password are in plaintext. Now specifying the `-AsPlainText` will decode the password to plaintext.
    * This gives the user better control over when they expose the password as plaintext if need be.
  * Another improvement is there is now a `-WithCredential` parameter which adds a `.Credential` property to the return Entry PS Object.
    * This is not done by default as it has overhead.
    * This gives the user better options and does not require manual creation of the credential.
    * **Breaking Change** Since this has been implemeneted the `-AsPsCredential` parameter has been removed. The new method is better as it allows for multiple entries to be returned with thier cred objects instead of limiting it to 1 entry.
* **Breaking Change** - `ConvertTo-KPPSObject` and all returned objects the `.FullPath` property now returns the true full path of the object. The `ParentGroup` property still exists and can be used as an alteranative data source for any lost functionality.

### v2.0.5.6

* Update-KeePassEntry no longer creates a new entry, Entry history is retained, UUID is never changed, All time modificiation fields are now updated when appropriate.
  * [#127](https://github.com/PSKeePass/PoShKeePass/issues/127)
  * [#123](https://github.com/PSKeePass/PoShKeePass/issues/123)
  * [#120](https://github.com/PSKeePass/PoShKeePass/issues/120)
* Code clean up in the internal functions
  * Removed unecessary comments.
  * Simplified parameter attributes, and formatting.
  * Updated error handling to use write-error and simplified handling.
* Normalized repetative checks to their own `Test-X` functions and moved error\null handling inside.
  * Test-KPConnection - Checks to see if connection exists and and is open.
  * Test-KPPasswordValue - Correctly checks for supported types and moved error handling inside.
* Fixed Dev Tool AutoVersioning Script, now updates psd1 version again.
* Simplified `Import-KPLibrary` function.
* Updated `ConvertTo-KPPSObject` to be construct PSObject differently and gained 86% speed performance improvement.
* Created a `build.ps1` script to build the module for use and publishing to gallery
* Updated `New-KPConnection` to prompt for user MasterKey (keepass passsword) via console prompt `Read-host` instead of `$Host.ui.PromptForCredential()`, this is much faster than loading the gui.

### v2.0.4.5

* #135 - Restructured Module to a more modular structure. Single file per function, seperate root folders for exported functions vs internal functions, (functions, internal).
* Added global variable `$Global:KeePassConfigurationFile` with the path of the config file and updated all references to file.
* Updated formatting of readme and changelog to abide by md standards.

### v2.0.4.4

* Some Community PR here, great help thank you
* #53, #117 `-AsPSCredential` Support to `Get-KeePassEntry`
* `-Title` Parameter Added to `Get-KeePassEntry`
* General bug fixes #115, #116, #120, #123, #127
* `New-KeePassDatabase` function added

## Known Issues

See the [Known-Issue](https://github.com/PSKeePass/PoShKeePass/issues?q=is%3Aissue+is%3Aopen+label%3AKnown-Issue) tag to get a list of known issues and their status.

## Contributing

* If you are insterested in fixing issues and contributing directly to the code base, please see the documentation on [How to Contribute](https://github.com/PSKeePass/PoShKeePass/blob/master/contribute.md).
* If you come across a bug or have a feature request feel free to create an issue with the appropriate label.

## Shout-Outs

* PSKeePass would like to thank [Jason Fossen](https://github.com/JasonFossen) for his [initial work](https://cyber-defense.sans.org/blog/2015/08/13/powershell-for-keepass-sample-script) with KeePass in PowerShell.
* PSKeePass would like to thank [Andrzej Pilacik](http://www.apdba.com/) (aka @cypisek77) for his review and feedback on documentation and over all rubber ducking.

## License

Copyright (c) [John Klann](https://github.com/jkdba), [Christian Lehrer](https://github.com/chritea). All rights reserved.

Licensed under the [MIT](https://github.com/PSKeePass/PoShKeePass/blob/master/license) License.
