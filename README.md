# Argonpass

A secure one way hash based password manager

### Why?

Good passwords are difficult. Remembering a unique and complex password few
every account is not an easy task and may lead to a lot of frustrating
experiences. Because of this many people will write the password down, reuse the
password between multiple sites, or revert to using many weaker and simpler
passwords. To counteract this many recommend the use of password management
tools such as LastPass; however, this places a lot of trust on 3rd party
entities. Websites can be hacked, databases can be leaked, and you can never be
completely sure they actually store your information as they promise. Instead,
argonpass places its trust on mathematics and open source software so you can
always know your sensitive information is not being tampered with.

## Principle

Argonpass takes a master password and using various information such as the
website and your usename produces a unique key for each account. This has
several advantages:

- Nothing sensitive is stored. There is no password file to be leaked or cracked
  as everything is computed when invoked.

- The user only has to remember one strong password (or more if desired).

- No passwords are reused so gaining access to one password should not breach
  other sites.

- The generated passwords can be arbitrarily long and contain all standard
  letters numbers and symbols, making them strong. These passwords should be
  relatively resistant to brute force and dictionary attacks.

## Features

- Each password's parameters can be tweaked to the site it is used on (Ex:
  cannot be over XX characters, cannot contain symbols), so it should work with
  almost any system.

- Built in revision system. Need to change your password every month or just
  worried your old one was leaked? No worries simply pass '-c' to argonpass and
  it will generate a new password for the account that bears no resemblance to
  the previous one.

- Configuration file can store various metadata about the account such as the
  website url and username making logging into sites even easier.

## Weaknesses

- No system is foolproof. Keyloggers, memory inspection, and other tools may be
  able to steal the generated keys.

- If someone learns your master password and has access to your argonpass
  account parameters then they will be able to access all of your accounts. Make
  sure you make it a strong one and don't give it out to friends or family.

- If you forget your password or do not have access to this software then you
  can not access the account (without a password reset or other external
  measure).

All of the aforementioned issues are not specific to argonpass and come with the
territory of password managers. However, there are some specific points that
should be noted about argonpass.

- Argon2 is a new algorithm. It is possible that a flaw may be discovered making
  it easier to compute the outputted passwords. A different algorithm could be
  replaced with little work, but at the cost of cpu and memory difficulty.

- The implementation of this program has not been reviewed by professional
  cryptographers.

- I carry no responsibility if anything bad happens to you by using this
  software.

## The details

The functioning of argonpass is fairly simple. Your entered password is hashed
using Argon2 with the website, username, and revision as a salt. Argon2 was
chosen as it is the now recommend password hashing algorithm after its choice by
the PHC (Password Hashing Competition) in 2015 - See
https://password-hashing.net. Argon2 is both CPU and Memory hard making attempts
at guessing the master password input inefficient and difficult. Like PBKDF2,
Argon2 is a key derivation function and thus "may prevent an attacker who
obtains a derived key from learning useful information about either the input
secret value or any of the other derived keys" (See
https://en.wikipedia.org/wiki/Key_derivation_function). These qualities make it
a very good choice for a password manager.

After running the password and salt information through Argon2 we are left with
an array of bytes which is not very useful to users. To transform this into
something usable as a password the result is treated as one large n bit number.
This number is then converted into a chosen base, X. These base X digits can
then be applied to a look up table to produce the displayed characters. For
example if we choose X to be 95 then each "digit" of the result will have one of
95 values, these correspond to the 95 printable ascii characters (26 lowercase
letters, 26 uppercase letters, 10 digits, and 33 symbols). If certain characters
are not desired or allowed then the radix of the result, X, can be reduced to
limit the output (such as 36 for only lowercase letters and numbers). This
method preserves all the entropy of the original binary hash and could thus be
converted back if desired. Other displays for binary information such as
hexadecimal and base64 were not chosen due to their limitations in output
variety (little to no symbols).

The site name, revision, and other information such as the hashing parameters
are stored in a configuration file for ease of use. This should be acceptable as
salts do not need to remain private assuming the master password is secure. For
increased security this file may be encrypted but that exceeds the scope of this
project (look at software such as eCryptfs and EncFS). The hashing parameters
can be configured on a global and per site level and should be tweaked to be as
strong as possible without taking too long (100ms is a good target for delays in
interactive processes).

## Installation

You must have python 3 installed.

You also need argon2_cffi from the python package index. You can use pip for
this. `pip install argon2_cffi`

If you would like to use the provided linux frontend then you should install
rofi (https://davedavenport.github.io/rofi).

A sample config is provided, you can copy it to your home directory

```
cp ./config ~/.argonpass
```
