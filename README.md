xxxxxxxxxxxxxx
xxxxxdxfxxdxxxxxxxxxxxcxxxxxxxxxfxxy
git adddxxdxdzc

xx
xxxxxbfx## OSX Steps:

1. Install gpg if you dont have it. 
```
brew install gpg
```

2. See what email address and name you're using for git
```
git config --global user.email
my@email.com
git config --global user.name
Trevor Linton
```
Note, if either of these are wrong or blank run `git config --global user.email [your email address]` and `git config --global user.name [your name]`

3. Run gpg key gen if you haven't already, use the email and name that was outputted above.

```
gpg --gen-key
```

4. List your keys you've generated
```
gpg --list-secret-keys --keyid-format LONG
/Users/trevor.linton/.gnupg/pubring.kbx
---------------------------------------
sec   rsa2048/8877665544332211 2018-08-03 [SC] [expires: 2020-08-02]
      F66F012CC31111555E8A22453311BB2BB1CCCACC
uid                 [ultimate] Trevor Linton <my@email.com>
ssb   rsa2048/0011223344332211 2018-08-03 [E] [expires: 2020-08-02]
```

Keep note of the sec key ID, in this case its `8877665544332211`

5. Assign a key to use when signing
```
git config --global user.signingkey 8877665544332211
```

6. Instruct git to sign commits automatically
```
git config --global commit.gpgsign true
```

7. Tell git to use gpg command line tool
```
git config --global gpg.program gpg
```

8. Export your public certificate 
```
gpg --armor --export 8877665544332211
-----BEGIN PGP PUBLIC KEY BLOCK-----
...
-----END PGP PUBLIC KEY BLOCK-----
```

9. Go to https://github.com/settings/keys and click "New GPG Key"
10. Paste the contents of your public key (including the `----BEGIN PGPG PUBLIC KEY BLOCK----` and `----END PGP PUBLIC KEY BLOCK----`) and click "Add GPG Key" this may ask for your password
11. Add GPG_TTY environment variable to your profiles:
```
echo "export GPG_TTY=\$(tty)" >> ~/.profile
echo "export GPG_TTY=\$(tty)" >> ~/.bash_profile
echo "export GPG_TTY=\$(tty)" >> ~/.zshrc
export GPG_TTY=$(tty)
```

### Backup your GPG keys

```
mkdir backups
cd backups
gpg -a --export my@email.com > public-gpg.key
gpg -a --export-secret-keys my@email.com > private-gpg.key
gpg --export-ownertrust > ownertrust-gpg.txt
tar zcvf github-gpg-keys.tgz *
openssl des -in github-gpg-keys.tgz -out github-gpg-keys.tgz.des.enc
open .
```

Now you can upload `github-gpg-keys-tgz.des.enc` somewhere safe, it's encrypted with the password you used.  Don't loose this file. Once you've uploaded it somewhere safe erase all of the files just created:

```
rm public-gpg.key
rm private-gpg.key
rm ownertrust-gpg.txt
rm github-gpg-keys.tgz
rm github-gpg-keys.tgz.des.enc
```

## Restoring your GPG Keys 

1. To restore unencrypt with openssl:
```
openssl des -in github-gpg-keys.tgz.des.enc -out github-gpg-keys.tgz -d
```
2. Extract the tar gz file.
```
tar zxvf github-gpg-keys.tgz
```
3. Import the keys
```
gpg --import private-gpg.key 
gpg --import-ownertrust ownertrust-gpg.txt
```
