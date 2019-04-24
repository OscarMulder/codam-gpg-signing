# Codam gpg signing
Instructions for installing gpg and signing your commits with it on Codam imacs. I decided to write these instructions because I had a lot of issues while setting up commit signing. I hope it will help you have less issues.

Using the following instructions you should be able to sign your commits.

## Install homebrew
Using kube's homebrew installer script:
```sh
curl -fsSL https://rawgit.com/kube/42homebrew/master/install.sh | zsh
```

## Install gpg & pinentry-mac
```
brew install gpg2 pinentry-mac
```
Installing gpg might take a while... I choose pinentry-mac because it's more stable then the "default" pinentry program. Pinentry-mac seems to actually keep the password for the key in the OSX keychain, so you don't have to re-enter your password everytime you start a new session. Also, pinentry crashed a few times before I switched to pinentry-mac, so far I had no issues with pinentry-mac, so I would recommend using it.

## Configure gpg
Gpg needs to be able to find your pinentry (the prompt that asks for your password to verify you are the owner of the key).
Because 42homebrew installs software in a different folder then usual, you need to tell gpg where to find it.
Run `where pinentry-mac`. The output is the path to pinentry-mac.
Open `~/.gnupg/gpg-agent.conf` in your favourite code editor.
```
code ~/.gnupg/gpg-agent.conf
```
Add the following line, replace the path with your own (the output of `where pinentry-mac`).
```
pinentry-program /Users/ENTER USERNAME HERE/.brew/bin/pinentry-mac
```

## Creating a pgp key
Follow the Github instructions to create a key and add it to your git config. Make sure to use a Github verified email while creating the key.
1. https://help.github.com/en/articles/generating-a-new-gpg-key
2. https://help.github.com/en/articles/adding-a-new-gpg-key-to-your-github-account
3. https://help.github.com/en/articles/telling-git-about-your-signing-key

## Make sure you have set git name and email
Run this command to see your git configuration.
```
git config --global --list
```
If user.name and/or user.email or not set properly, you can set them with these commands. Make sure to set an email that is verified by github.
```
git config --global user.name Your Name
git config --global user.email me@example.com
```

## Configuring git to sign your commits
At this point you should have your pgp key created and added to your git config and github. Now all we need to do is configure git to use the key to sign the requests. Again, because homebrew uses a different path, we should tell github where to find gpg. You can use `where gpg` to find it. Change the path to your own.
```
git config --global gpg.program /Users/$USER/.brew/bin/gpg
```
Now just tell git to sign all commits:
```
git config --global commit.gpgsign true
```

## Restart GPG daemon
You might need to restart your gpg daemon. Also after closing and reopening your session, you sometimes need to restart it.
If you get any error from git. Just restart it.
```
gpgconf --kill gpg-agent
gpg-agent --daemon
```

## Troubleshooting
You can test your gpg daemon by running:
```
echo "test" | gpg --clearsign
```
If this doesn't work, you have a problem in your gpg config. If this works the problem is in your git config.

## Done!
Your commits should now be signed and show up on Github as Verified.

