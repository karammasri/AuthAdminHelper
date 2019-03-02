# User Guide

The Authentication Administrator Helper PowerShell module includes additional functions that might be required by helpdesk/admins in the Authentication Administrator role that are not currently included in the Azure AD Portal.

## Pre-requisites

This modules uses functions in the [MSOnline](https://docs.microsoft.com/en-us/powershell/module/msonline/?view=azureadps-1.0) PowerShell module.

The MSOnline module can be downloaded and installed from the PowerShell gallery using:

```powershell
Install-Module MSOnline
```

## Importing the Authentication Administrator Helper PowerShell module

Open PowerShell, change to the folder where the module file was downloaded and import the module using:

```powershell
Import-Module .\authadmin.psm1
```

The module can also be [implicitly imported](https://docs.microsoft.com/en-us/powershell/developer/module/importing-a-powershell-module#implicitly-importing-a-module-powershell-30) by PowerShell if stored in a folder that is in the `PSModulePath` environment variable.

## Authenticating to Azure AD

An authentication prompt will be displayed the first time a function is called that requires connectivity to Azure AD. Additional calls to functions in the module will not require re-authentication once the first authentication succeeds.

The authentication prompt relies on the `Connect-MSOLService` function provided by the MSOnline module. Any conditional access policies that apply when calling `Connect-MSOLService` will then also apply to users using this module (e.g. MFA).

### Permissions required by the user using the module

The user calling the functions in this module should be a member of one of the following [Azure AD administrative roles](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles):

- Global Administrator
- Authentication Administrator
- Privileged Authentication Administrator

Assignments to these roles can be managed using [Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-configure). The user will need to complete [activation of the role](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-how-to-activate-role) before using this module.

The module relies on the permissions granted over the target user objects in the tenant by the Azure AD administrative role that the calling user is member of. There are no special additional permissions granted by this module.

## Functions provided by this module

### Get the MFA authentication methods for a user

Run the following command to get the MFA authentication methods for a user:

```powershell
Get-MFAUserAuthenticationMethods -UserPrincipalName <UPN>
```

Where `<UPN>` is the User Principal Name of the user. The command accepts only one User Principal Name per call.

The output will show the authentication methods registered for the user. Methods not registered are not listed.

The methods are shown using the aliases described in [MFA Methods aliases](#mfa-methods-aliases).

A second column named IsDefault is added to the output. The column will contain the value “True” for the default authentication method and “False” for all the other methods.

### Change the default MFA authentication method for a user

Run the following command to change the default MFA authentication method for a user:

```powershell
Set-MFAUserDefaultAuthenticationMethod -UserPrincipalName <UPN> -Method <MethodAlias>
```

Where:

- `<UPN>` is the User Principal Name of the user. The command accepts only one User Principal Name per call
- `<Method>` is the alias of MFA authentication method to set as default for the user as listed in [MFA Methods aliases](#mfa-methods-aliases). PowerShell tab completion can be used to cycle through the possible values.

The user should have already registered this method for the change to take effect. An error is shown if the user doesn’t have the selected method registered.

### Reset the MFA authentication methods for a user

Run the following command to reset the MFA authentication methods for a user:

```powershell
Reset-MFAUserAuthenticationMethods -UserPrincipalName <UPN>
```

Where `<UPN>` is the User Principal Name of the user. The command accepts only one User Principal Name per call.

The cmdlet has an [confirm impact](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-6#confirmpreference) of "High" and will request confirmation before committing changes.

## Display the aliases of MFA authentication methods

Run the following command to get the aliases of the MFA authentication methods:

```powershell
Get-MFAMethodsAliases
```

The output will show the aliases as described in [MFA Methods aliases](#mfa-methods-aliases).

## MFA Methods aliases

The module uses the following aliases to identify MFA methods:

|Alias                      |Description  |
|---------------------------|-------------|
|TwoWayVoiceMobile          | Phone call to authentication phone |
|TwoWayVoiceAlternateMobile | Phone call to alternate authentication phone |
|TwoWayVoiceOffice          | Phone call to office phone |
|OneWaySMS                  | One-time code sent via SMS message to authentication phone |
|PhoneAppOTP                | One-time code generated by the Authenticator app |
|PhoneAppNotification       | Push notification to the Authenticator app |
