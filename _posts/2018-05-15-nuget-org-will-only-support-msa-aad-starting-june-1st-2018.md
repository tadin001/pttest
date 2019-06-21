---
ID: 226141
post_title: >
  NuGet.org will only support MSA/AAD
  starting June 1st, 2018
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-org-will-only-support-msa-aad-starting-june-1st-2018/
published: true
post_date: 2018-05-15 00:00:00
---
We had previously announced the [deprecation of NuGet.org's home-grown authentication][1] in favor of Microsoft accounts (MSA) that will allow us to add support for additional security systems such as two-factor authentication (2FA). We will be **disabling the NuGet.org's home-grown authentication mechanism starting June 1st, 2018**. This means that you can only sign in to NuGet.org using a Microsoft account or an Azure AD account from next month. If you have not yet linked your account to MSA/AAD, do it now!

## Linking MSA/AAD to an existing account

If you have not yet linked your NuGet.org account to a MSA/AAD, you can do so from the [`Account Settings`][2] page > `Login Account` > `Link Microsoft account`:

![Add MSA][3]

**Starting June 1st, 2018** when you sign in to NuGet.org with a username/password (NuGet.org authentication), you will be asked to link your MSA account in a blocking dialog as shown below. You won't be able to proceed with any other action on NuGet.org including package upload, changing settings, etc. unless you link MSA/AAD account with NuGet.org.

![Blocking MSA dialog][4]

Once you sign in with your MSA/AAD credentials, your account will be linked to the new authentication and the existing username/password based NuGet.org authentication mechanism will be removed from your account. Going forward you must use the newly linked MSA/AAD account to sign in to NuGet.org.

**To convert your account into an [organization][5]**, you can directly navigate to <https://www.nuget.org/account/transform>. This will prevent you an additional step to first link an MSA before transforming your account to an organization.

## Using two-factor authentication

We recommend you enable two-factor authentication (2FA), with your MSA account, to sign in NuGet.org for enhanced security. This can be easily enabled from the [`Account Settings`][2] page > `Login Account` > `Enable` 2FA:

![2FA setting][6]

**Note**:

*   This setting will **only enable 2FA while signing in to NuGet.org** and not for all the other services you sign in with MSA. You may also choose to globally enable 2FA for MSA at <https://account.live.com/proofs/Manage>.
*   2FA setting is not available in NuGet.org for Azure AD accounts as this setting is managed by the tenant admin of your Azure AD account. 

## We want to hear your feedback!

If you have any question, comment or feedback you can reach out to me at <anangaur@microsoft.com> or by tagging [@nuget][7] in your tweets. You can also create an issue on our [Github repository][8]. We will be sure to announce any changes or updates regarding the key dates on our [NuGet/Announcements][9] repo.

 [1]: https://blog.nuget.org/20180227/Deprecating-NuGet-org-authentication.html
 [2]: https://www.nuget.org/account
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/add-msa.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/link-msa-dialog.png
 [5]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/2fa-setting.png
 [7]: https://twitter.com/nuget
 [8]: https://github.com/NuGet/NuGetGallery/issues/new
 [9]: https://github.com/NuGet/Announcements