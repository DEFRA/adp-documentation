---
title: Verify GitHub commit signatures
summary: Verify GitHub commit signatures
authors:
    - Logan Talbot
date: 2024-01-22
weight: -11
---

# Verify GitHub commit signatures

The project's branch policy is configured to necessitate the use of Git signed commits for any merging activities. This policy serves a twofold purpose: firstly, it validates the authenticity of changes and acts as a barrier against unauthorised or malevolent alterations to the codebase. 

Secondly, it provides assurance of code integrity by demonstrating that changes have remained unaltered throughout transit and subsequent commits. During the evaluation of pull requests or merge requests, the presence of signed commits also offers a reliable means to confirm that the proposed changes have been authored by authorised contributors, thereby reducing the likelihood of unintentionally accepting unauthorised code. 


To use signed commits, developers must generate a GPG (GNU Privacy Guard) key pair, which includes a private key kept secret and a public key that is shared. Commits are then signed using the private key, and others can verify the commits using the corresponding public key.  


<IMG  src="https://docs.github.com/assets/cb-97945/images/help/settings/gpg-verified-with-expired-key.png"  alt="Screenshot of a list of commits. One commit is marked with a &quot;Verified&quot; label. Below the label, a dropdown explains that the commit was signed, but the key has now expired."/>


## Generate New GPG Key
Please refer the following link. Please make sure the email you enter in step 8 is your github email account
https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key


## Adding a GPG Key on Github

- In the upper-right corner, click your profile photo, then click Settings.

<IMG  src="https://docs.github.com/assets/cb-65929/images/help/settings/userbar-account-settings.png"  alt="Screenshot of GitHub&#39;s account menu showing options for users to view and edit their profile, content, and settings. The menu item &quot;Settings&quot; is outlined in dark orange."/>

- In the "Access" section of the sidebar, click  **SSH and GPG keys**.

- Next to the "GPG keys" header, click New GPG key.

- In the "Title" field, type a name for your GPG key.

- In the "Key" field, paste the GPG key you copied when you generated your GPG key.

- Click **Add GPG key**.

- To confirm the action, authenticate to your GitHub account.


## Further support and information 


https://docs.github.com/en/authentication/managing-commit-signature-verification 

## Signing commits using WSL

1. [Generate new gpg key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)
1. [Adding a pgp key to your github account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account)
1. [telling git about your signing key](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)
1. [Still having problems committing try this?](https://gist.github.com/paolocarrasco/18ca8fe6e63490ae1be23e84a7039374?permalink_comment_id=3767413#gistcomment-3767413)
1. [Signing old commits](https://superuser.com/questions/397149/can-you-gpg-sign-old-commits)
