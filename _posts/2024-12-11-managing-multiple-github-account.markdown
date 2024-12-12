---
title: "Managing Multiple Github Accounts"
layout: post
date: 2024-12-11 12:10
image: /assets/images/markdown.jpg
headerImage: false
tag:
- github
- multiple-accounts
- workflow
category: blog
author: maderismawan
description: manage multiple GitHub accounts on a single device
---

Sometimes, when start working, i may need to use two GitHub accounts, such as a personal account and a work account. I came across an article that was helpful in setting up [How To Work With Multiple Github Accounts on a single Machine](https://gist.github.com/rahularity/86da20fe3858e6b311de068201d279e3). The article provides a clear explanation for those who need to work with two GitHub accounts. After following the steps in the article, the final step is to configure the configuration file.

{% highlight raw %}
~/.ssh/config

# this personal github account
Host github.com-madrismawan
    HostName github.com
    AddKeysToAgent no
    UseKeychain yes
    IdentitiesOnly yes
    IdentityFile ~/.ssh/github/madrismawan

# this github work account 
Host github.com-rismawan-jti
    HostName github.com
    AddKeysToAgent no
    IdentitiesOnly yes
    UseKeychain yes
    IdentityFile ~/.ssh/github/rismawan-jti
{% endhighlight %}

Verify the SSH key configuration by running:

{% highlight raw %}
ssh -T git@github.com-madrismawan
ssh -T git@github.com-rismawan-jti
{% endhighlight %}


I also added some additional configurations to enable automatic switching between GitHub accounts within specific subfolders. 

{% highlight raw %}
~/.gitconfig

[filter "lfs"]
	required = true
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process

# Global settings
[gpg]
[commit]
	gpgsign = true
[user]
	name = Made Rismawan
	email = rismawannugraha10@gmail.com
	signingkey = ~~~
[github]
	user = "madrismawan"
[core]
	sshCommand = "ssh -i ~/.ssh/github/madrismawan"

# this command for automation change if i work on directory github-work-account 
[includeIf "gitdir:~/Workshop/Jangkartech/"]
	path = ~/.github-config/.work-account
{% endhighlight %}

You can create as many accounts as you have and set up like on this top code, spesific with subfolder you work.

{% highlight raw %}
~/.github-config/.work-account

[user]
    name = Rismawan JTI
    email = rismawan.jangkar@gmail.com
    signingkey = ~~
[github]
    user = "rismawan-jti"
[core]
    sshCommand = "ssh -i ~/.ssh/github/rismawan-jti"
{% endhighlight %}

this example if u use clone use spesific account, change host default from github.com with HOST you regis on file config

{% highlight raw %}
git clone git@github.com-madrismawan:madrismawan/repo.git
{% endhighlight %}