---
title: "Digital Signatures for Your Code"
layout: post
date: 2024-08-23 12:10
image: /assets/images/markdown.jpg
headerImage: false
tag:
- github
- commit
- signature
category: blog
author: maderismawan
description: A verified commit is like a "Certified Authentic" stamp on your code
---

![Screenshot]({{site.url}}/assets/images/blogs/verified-commit/image.png)

You can make sure your commits are secure and trustworthy without spending a dime? Yep, with the verified commit feature, you can show the world that every line of code you push is really yours

## What is a Verified Commit?
A verified commit is like a "Certified Authentic" stamp on your code. GitHub uses this label to ensure that the code you send hasn’t been tampered with by anyone else. So, a verified commit is like having your unique signature on it—authentic and secure!

## Why is Verifying Your Commits Important?
1. **Safe from Prying Hands**: No one can alter your code without your knowledge. Your commits are locked down and secure.
2. **Reducing Security Risks**: Commit signatures help secure the repository by ensuring that only legitimate changes are accepted.
3. **Automated Validation**: In Continuous Integration/Continuous Deployment (CI/CD) systems, commit signatures can be used to automate validation processes before the code is processed further or deployed, enhancing both efficiency and security.``

Certainly! For more detailed information on [managing commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification). You can read the documentation on GitHub; it explains very clearly how to [Generate a GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key).

![Screenshot]({{site.url}}/assets/images/blogs/verified-commit/add-information.png)
<figcaption >Add information for GPG</figcaption>

After completing input information for GPG, you can get the public key and private key from the list.

![Screenshot]({{site.url}}/assets/images/blogs/verified-commit/list-gpg.png)
<figcaption >List GPG ID, Public Key and Private Key</figcaption>

![Screenshot]({{site.url}}/assets/images/blogs/verified-commit/export-gpg.png)
<figcaption >Substituting in the GPG key ID</figcaption>

Copy your GPG key, beginning with -----BEGIN PGP PUBLIC KEY BLOCK----- and ending with -----END PGP PUBLIC KEY BLOCK---- and [Adding a GPG key to your GitHub account.](https://docs.github.com/en/authentication/managing-commit-signature-veaification/adding-a-gpg-key-to-your-github-account).

The last thing you need to ensure is [Telling Git about your signing key](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key#telling-git-about-your-gpg-key). Or you can search for the GitHub configuration file using the configuration I provided below.

![Screenshot]({{site.url}}/assets/images/blogs/verified-commit/gitconfig.png)
<figcaption >.gitconfig</figcaption>

You can sign commits locally using GPG. When committing changes in your local branch, add the -S flag to the git commit command:
{% highlight raw %}
git commit -S -m "YOUR_COMMIT_MESSAGE"
# Creates a signed commit
{% endhighlight %}

![Screenshot]({{site.url}}/assets/images/blogs/verified-commit/show-commit.png)
<figcaption >show-signature commit</figcaption>




