
# Password management

Strong password are good and not using the same password everywhere is essential.
That's why we tend to use a limited amount of passwords and reuse them for different sites.
If you have different passwords for every site, chances are you wrote them somewhere in case you forgot.
The bottom line is that security is important and everyone should have unique and strong passwords.

The problem is to remember them because we don't want to write our passwords.
The wrost case it to write the password in plain text on a text file on your computer.
The only step for a hacker is to get a copy of that file.
And it can be really easy for a hacker to get access to you local file system.

That's why you must be careful when you open an email attachment.
Do __NOT__ ever open a `.exe` file from __any__ one out there.
An hacker can send you an email with someone's name you know in the "From:" field.

An other solution is to write your password to a notebook stored on your house.
That would be more secure because it is always offline by definition.
But if your store your password in plain text, the same security issue araise again.
What if the hacker your fear is someone in your familly who wants bad thing to happen to you?
That person could easily have access to your house during a party or an event.

It doesn't matter when your store your passwords.
What matters is that you don't store your password in plain text.
That's where computer can help us.

## Simple cypher algorithm - Caesar cypher

Let's say I have a password `abcdefgh`.
In order to store it somewhere, I need to convert it to an encrypted text.
The simplest encryption algorithm is the Caesar cipher.
It states that every letter in your message will be replaced by the next letter in the alphabet.
My password would become `bcdefghi`.
The first character of my password was `a`.
What comes after A? B.
So the first character of my encrypted password will be `b`.

I can now securly store my encrypted password in my notebook or my text file.
But wait. That's too simple!

With the Caesar cipher, my _encryption key_ was 1.
I shifted only one letter forward.
I can take an other encryption key. Maybe 19.
My encrypted password would be `tuvwxy`.

The thing is that if I have your encrypted password, I can _brute force_ it and have 26 possible passwords to try.
Most website don't implement a limit on how much login attempts I have.
In less than 5 minutes, I can have access to any website for which you stored your password.
The Caesar cypher is too simple for storing something as important as our password.

## Gnu Privacy Guard

More complex algorithm exist and do a better job than we do.
In Unix environments, the `gpg`command allows us to encrypt and decrypt files using encryption algorithm of our choosing.

An example of symetric encryption:

```
# Creates a file.txt.gpg file who's content is encrypted
> gpg --symmetric --cipher-algo AES256 file.txt
# Decrypt the file.txt.gpg file to decrypted_file.txt
> gpg -o decrypted_file.txt -d file.txt.gpg
```

The `gpg`command will ask you for a passphrase.
That passphrase is the encryption key that is used both for encrypting and decrypting.

With that in mind, we can create a files containing the encrypted password for each site we want.
That's up to you to organize your files.
Here is what I do.

I have a directory for each site I have an account.
Then, inside that directory, I create a file who has the same name as my user name I use to log in.
The content of that file is the encrypted password.

# Code

Here is the implementation of the Caesar cypher as a JavaScript function:

``` javascript
function caesar_cypher(message, key) {
    return message.split('')
        .map(c => c.charCodeAt(0))
        .map(n => n + key)
        .map(n => String.fromCharCode(n))
        .join('');
}
```
