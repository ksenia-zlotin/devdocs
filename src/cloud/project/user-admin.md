---
group: cloud-guide
title: Manage user access to Cloud projects
functional_areas:
  - Cloud
  - Configuration
redirect_from:
  - /cloud/admin/admin-user-admin.html
  - /cloud/project/user-admin.html#cloud-role-acct-owner
  - /cloud/project/user-admin.html#cloud-role-env
  - /cloud/project/user-admin.html#cloud-role-project
---

You can manage access to {{site.data.var.ece}} projects by adding users and assigning roles. Assign project-level roles to provide access to the entire project, and environment-level roles to set permissions per available environment.

| **Role** | **Scope**  | **Access** |
|-|-|-|
| **Account owner** | Project | Perform any task in any project or environment, including deleting it.<br>Magento assigns this role to the License Owner associated with the email address,<br>name, and information of the person who registered the Magento Commerce Cloud account.<br><br>You must submit a Magento Support ticket to modify settings or change the Account owner. |
| **Super user** | Project | Administrator access to all project settings and Cloud environments. Super users<br>can change settings and perform tasks on any environment, including creating<br>and restoring [snapshots][]. |
| **Project reader** | Project | View access to all project environments. Users with this role cannot perform tasks on<br>any environment. However, you can configure environment-level permissions for users with<br>this role to permit write access to a specific environment. |
| **Admin** | Environment | Change settings and perform tasks on an environment, including merging with the parent<br>environment |
| **Contributor** | Environment | Push code and branch the environment |
| **Reader** | Environment | View-only access to an environment |

## Add user authentication requirements

For added security, Magento is introducing a project-level MFA enforcement feature to improve security for SSH access to Cloud infrastructure. See [MFA enforcement for SSH].

When MFA enforcement is enabled on a {{site.data.var.ece}} project, all users with SSH access to an environment in that project must enable 2FA on their {{site.data.var.ece}} account. For automated processes, users must create an API token that machine users can use to authenticate. See [Enable user accounts for 2FA and SSH access](#update-account-security-settings).

## Add users and manage access

You can add users and assign roles using the Magento CLI or the Project Web Interface.

{:.bs-callout-tip}
Adding or updating a user for a {{site.data.var.ece}} environment triggers a site deployment, which takes your site offline until deployment completes. For Production environments, we recommend completing user administrative tasks during off-peak hours to prevent service disruptions.

**Prerequisites:**

To add a user to a project or environment, you need the email address associated with an existing {{ site.data.var.ece }} account. New users can [register for an account][{{site.data.var.ece}} account] and provide the associated email address after completing account validation.

### Manage users with the CLI {#cloud-user-mg-cli}

You can use the {{site.data.var.ece}} command line client to manage users and integrate this with any other automated system.

Available commands:

-  `magento-cloud user:add`–add a user to the project
-  `magento-cloud user:delete`–delete a user
-  `magento-cloud user:list [users]`–list project users
-  `magento-cloud user:role`–view or change the user role

The following examples show how to add a user and configure the project and environment-level role, and how to modify project assignments and assigned user roles.

{:.procedure}
Add a user and assign roles:

1. Add the user:

   ```bash
   magento-cloud user:add
   ```

1. Follow the prompts to specify the user email address, set the project and environment roles, and add the user:

   ```terminal
   Enter the user's email address: alice@example.com

   Email address: alice@example.com

   The user's project role can be 'viewer' ('v') or 'admin' ('a').
   Project role [V/a]: a
   The user's environment-level roles can be 'viewer', 'contributor', or 'admin'.
   development environment role [V/c/a]: c
   Summary:
     Email address: alice@example.com
     Project role: contributor
   Adding users can result in additional charges.
   Are you sure you want to add this user? [Y/n]
   Adding the user to the project
   ```
   {:.no-copy}

   {%include cloud/note-prevent-site-availability-issues.md%}

   After you add the user, Magento sends an email to the specified address with instructions for accessing the {{ site.data.var.ece }} project.

The following example changes the environment-level role that is assigned to a user:

```bash
magento-cloud user:role alice@example.com --level environment --environment development --role admin
```

{:.bs-callout-info}
To list the available `magento-cloud` CLI commands, use the `magento-cloud list` command.

### Manage users from the Project Web UI {#cloud-user-webinterface}

You can add project-level and environment-level users from the Project Web UI, and use the _Edit_ feature to modify permissions for an existing user.  After you add a user, the user receives an email inviting them to join the {{site.data.var.ece}} project.

{:.procedure}
Access the Project Web UI to add users:

1. Log in to [your {{site.data.var.ece}} account][{{site.data.var.ece}} account].

1. Click the **Projects** tab.

   ![Click the projects tab to access your Cloud project]({{ site.baseurl }}/common/images/cloud/cloud_account_project.png){:width="550px"}

1. Click the name of your project to open the Cloud project portal (Onboarding UI).

1. Click **Infrastructure access**, and then click the **Project Access (Web UI)** link.

   ![Cloud project portal]({{ site.baseurl }}/common/images/cloud/cloud-login-infrastructure-access.png){:width="550px"}

1. In the Project Web UI, add project-level users and environment-level users as needed.

{:.procedure}
Add a project-level user:

1. In the Project Web UI, click the configure project gear icon next to project name in the top navigation bar.

   ![Configure the project]({{ site.baseurl }}/common/images/cloud/cloud_project_gear.png){:width="184px"}

1. In the _Users_ tab, click **Add User**.

   ![Start creating users]({{ site.baseurl }}/common/images/cloud/cloud_project-config.png){:width="500px"}

1. Complete the _Add User_ form:

   ![Add users]({{ site.baseurl }}/common/images/cloud/cloud_project-add-super-user.png){:width="500px"}

   -  Enter the user e-mail address.

   -  Select the access for the account:

      For a project administrator account, select the **Super User** checkbox. This provides Admin rights for all settings and environments. If not selected, the account has only view options for all project environments.

   -  Select permissions per specific environment (or branch) in the Integration environment: _No access_, _Admin_ (change settings, execute action, merge code), _Contributor_ (push code), or _Reader_ (view only). When you add active environments, you can modify permissions per user.

1. Click **Add User**.

   {:.bs-callout-warning}
   After you add project-level users, you must redeploy all environments to apply the changes. Adding a project user does not trigger the redeploy automatically.

{:.procedure}
Add an environment-level user:

1. In the Project Web UI, select the environment. Then, click the configure environment settings icon next to the environment name.

   ![Configure the environment]({{ site.baseurl }}/common/images/cloud/cloud_project-env.png){:width="550px"}

1. Click the **Users** tab, then click **Add User**.

1. Complete the _Add User_ form:

   ![Add the user]({{ site.baseurl }}/common/images/cloud/cloud_project-add-env-user.png){:width="550px"}

   -  Enter the user email address.

   -  Select the user role from the dropdown menu: _Admin_, _Contributor_, or _Reader_.

   -  Click *Add User*.

{%include cloud/note-prevent-site-availability-issues.md%}

## Update account security settings

After you add a user to a Cloud project, ask them to review their account settings and add the following configuration as needed:

-  Enable two-factor authentication (TFA)

   Magento recommends adding two-factor authentication to all accounts to meet security and compliance standards. Some projects will soon require two-factor authentication on all accounts that use SSH to connect to {{site.data.var.ece}} projects.

-  Enable SSH keys

   Users that require access to {{site.data.var.ece}} source code repositories and infrastructure must enable SSH keys on their account. See [Enable SSH keys][].

-  Create an API token

   You can generate an API token on your account that can be used for secure machine access to an environment through an SSH connection. Use the API token to enable login workflows for automated processes.

   On projects with the MFA enforcement feature enabled, you must use the API token to authenticate SSH access requests from automated accounts. to bypass authentication workflows which require 2FA.

### Enable 2FA

{{site.data.var.ece}} supports 2FA using any of the following applications:

-  [Google Authenticator (Android/iPhone/BlackBerry)][]
-  [Authy (Android/iPhone)][]
-  [Authenticator (Windows Phone)][]
-  [FreeOTP (Android)][]
-  [GAuth Authenticator (Firefox OS, desktop, others)][]

You can find instructions for setting up the authenticator app and enabling 2FA on the _Account settings_ page for your {{site.data.var.ece}} user account.

{:.procedure}
To enable 2FA on your {{site.data.var.ece}} user account:

1. Log in to the [{{site.data.var.ece}} user account][{{site.data.var.ece}} account].

1. On the Cloud projects page, click the **Account settings** tab.

   ![Cloud projects page]({{ site.baseurl }}/common/images/cloud/cloud-account-settings-tab.png){:width="550px"}

1. On the Account settings tab, expand the **Security** section. Then, click **Set up application**.

   ![Cloud Security settings]({{ site.baseurl }}/common/images/cloud/cloud_account_settings-tfa-setup-app.png){:width="550px"}

1. Use the linked instructions to install one of the approved authentication applications on your mobile device, if needed.

   ![Cloud Security settings]({{ site.baseurl }}/common/images/cloud/cloud_account_settings-tfa-setup-app-options.png){:width="550px"}

1. On your mobile device, open the authentication app and add the setup code to the app.

   For example, if you use Google authenticator, click the + sign in the app, and then enter the text code from Magento in the app, or scan the QR code to enable {{site.data.var.ece}} TFA.

   ![Cloud 2FA app device setup]({{ site.baseurl }}/common/images/cloud/cloud-2fa-settings-tfa-app-example.png){:width="400px"}

1. On the _TFA set up - Application_ page, type the two-factor authentication code from your mobile device in the **Application verification code** field.

   ![Cloud 2FA app device setup]({{ site.baseurl }}/common/images/cloud/cloud_account_settings-tfa-setup-steps.png){:width="550px"}

1. Click **Verify and save**.

1. Optional. Enable _Trusted browser_ settings to save current authentication code in the browser session for 30 days and reduce number of authentication challenges during project log in.

1. Click **Save** or **Skip**.

1. On the _TFA setup - Recovery_ codes page, copy and save the recovery codes so that you can log into your {{site.data.var.ece}} project when you cannot access your mobile device or authentication app.

   ![Cloud TFA recovery codes]({{ site.baseurl }}/common/images/cloud/cloud-2fa-settings-tfa-recovery-codes.png){:width="550px"}

   {:.bs.callout-warning}
   Copy the recovery codes to another location or write them down to recover access if you your device or access to the authenticator application. Save the codes to your account to view and manage them from your account security settings. If you lose account access on a 2FA-enabled account and have no recovery codes, you must contact your Project administrator, or submit a Magento support ticket to reset the TFA application.

1. After completing the TFA setup, click **Save** to update your account.

1. On the _Account settings_ page, you can view and manage TFA configuration from the _Security_ section.

   ![Cloud manage TFA config]({{ site.baseurl }}/common/images/cloud/cloud-account-settings-manage-2fa-config.png){:width="550px"}

### Create an API token

An API token can be exchanged for an OAuth 2 access token, which can then be used to authenticate requests.

On projects that have 2FA enforcement enabled, you must have an API token to enable secure SSH access for machine users and automated processes.

1. Log in to the [{{site.data.var.ece}} account][].

1. On the Cloud projects page, click the **Account settings** tab.

   ![Cloud projects page]({{ site.baseurl }}/common/images/cloud/cloud-account-settings-tab.png){:width="550px"}

1. On the Account settings tab, expand the **API Tokens** section. Then, click **Create an API token**.

   ![Cloud create API token]({{ site.baseurl }}/common/images/cloud/cloud_account_settings-create-api-token.png){:width="550px"}

1. Specify an **Application** name for the token, for example specify a name that matches the machine user or automated process that will use the API token.

   ![Cloud create API token]({{ site.baseurl }}/common/images/cloud/cloud_account_settings-api-token-app-name.png){:width="550px"}

1. Click **Create API token** to generate the token.

   ![Cloud generate API token]({{ site.baseurl }}/common/images/cloud/cloud_account_settings-api-token-created.png){:width="550px"}

<!--Link references-->

[snapshots]: {{ site.baseurl }}/cloud/project/project-webint-snap.html
[Enable MFA enforcement for SSH]: {{ site.baseurl }}/cloud/project/project-enable-mfa-project.html
[{{site.data.var.ece}} account]: https://accounts.magento.cloud/user/
[enable SSH keys]: {{ site.baseurl }}/cloud/before/before-workspace-ssh.html
[{{site.data.var.ece}} user account]: https://account.magento.cloud/user
[snapshots]: {{ site.baseurl }}/cloud/project/project-webint-snap.html
[Google Authenticator (Android/iPhone/BlackBerry)]: https://support.google.com/accounts/answer/1066447?hl=en
[Authy (Android/iPhone)]: https://www.authy.com/app/
[Authenticator (Windows Phone)]: http://www.windowsphone.com/en-us/store/app/authenticator/021dd79f-0598-e011-986b-78e7d1fa76f8
[FreeOTP (Android)]: https://play.google.com/store/apps/details?id=org.fedorahosted.freeotp
[GAuth Authenticator (Firefox OS, desktop, others)]: https://github.com/gbraad/gauth
