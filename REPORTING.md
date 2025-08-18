OpenSSL is available for UNIX, Windows, and macOS operating systems.

- OpenSSL is a robust, full-featured open-source toolkit that implements the Secure Sockets Layer (SSL) and Transport Layer Security (TLS) protocols.

-Certificate Management: OpenSSL provides a rich set of features for managing digital certificates, including the ability to create, sign, and verify certificates. 

-API: OpenSSL offers a comprehensive API for developers to integrate SSL/TLS functionality into their applications. Coded in C language, the API is designed to be flexible and easy to use.

-Widely used by Web Services

------------------------------------------------------------------------------------------------------------------------

OPENSSL ENGINES

- Separate implementation for cryptographic operations 
- OpenSSL engines is used to interact with hardware cryptographic accelerators


------------------------------------------------------------------------------------------------------------------------

Installation Guide

1. First of all OPENSSL is built in UNIX operating systems.
2. So to check if OpenSSL is installed on your system, you can use the following command in the terminal:

```bash
openssl version
```
3. If not installed, you can install OpenSSL using the package manager for your UNIX system. For example, on Ubuntu, you can use the following command:

  ```bash
  sudo apt-get install openssl
  ```

  What is only-upgrade in downloading openssl?
  - The `--only-upgrade` option only upgrades OpenSSL if it is already installed; it will not install OpenSSL if it is missing.
  - For most users, especially beginners, you should use `sudo apt-get install openssl` to ensure OpenSSL is installed even if it was not present before.
  - Use `--only-upgrade` only if you are sure OpenSSL is already installed and you want to avoid installing new packages.


------------------------------------------------------------------------------------------------------------------------
Introduction

man open
 - Manual for the open ssl and information about the tool

openssl help
- show the available commands
openssl --version

openssl version --help


----------------------------------------------------PROBLEM 1 ----------------------------------------------------------------

“Check if a website’s SSL/TLS certificate is expired or insecure”

🧩 Why this is a real problem?

Many websites forget to renew their SSL certificates.

Expired or weak certificates break HTTPS, cause warnings, and can expose users to attacks.

With OpenSSL, you can quickly diagnose if a site is safe or misconfigured.




1. openssl s_client -connect expired.badssl.com:443 -servername expired.badssl.com
   openssl s_client -connect example.com:443 -servername example.com

Analyze
  s_client 
    - acts as a web browser for the openSSL to interact with the website but only SSL/TLS handshakes
    - this is to connect to a server and show every details about the connection examples are certifications,ciphers, protocols.

  -connect expired.badssl.com:443
    - this specifies the server and port to connect to for the SSL/TLS handshake.

  -servername expired.badssl.com
    - this specifies the server name to use for the SSL/TLS handshake, allowing for proper certificate validation.


2. EXAMPLE OF A GOOD CERTIFICATE
  echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null \
  | openssl x509 -noout -dates -subject -issuer

  EXAMPLE OF A BAD CERTIFICATE
  echo | openssl s_client -connect badssl.com:443 -servername badssl.com 2>/dev/null \
  | openssl x509 -noout -dates -subject -issuer


  EXPLANATION
    /dev/null
     - this is a special file that discard all data that might be uncessary or noisy

    | openssl x509 -noout -dates -subject -issuer
     - This is a command tool for X.509 certificates to filter out the essentials in reading a certificate.

  3.  So in real-world usage, you would replace `example.com` and `badssl.com` with the actual domain names you want to check.
      and you probably have encountered the concept of SSL/TLS certificate validation when you open a website that shows that the connection 
      is not private meaning the certification is either expired or misconfigured.




----------------------------------------------------PROBLEM 2 ----------------------------------------------------------------

Problem 2: “I downloaded a file, but how do I know if it’s authentic or tampered with?” 


1. mkdir ActivitySSL
   cd ActivitySSL
   echo "Important software update v1.0" > update.txt
   openssl dgst -sha256 update.txt
   echo "PASTE THE ORIGINAL CHECKSUM" > checksum.txt

2. Pretend to share the file+checksum with a friend or other device


3. Modify the file and generate a new checksum
  - A checksum is a unique code generated from data that helps you verify integrity and authenticity.
  echo "Malicious code" >> update.txt

4. Verify
  openssl dgst -sha256 update.txt

  # Compare the checksums

EXPLANATION
  - The `openssl dgst -sha256` command computes the SHA-256 hash of the file, which serves as a digital fingerprint.
  - If the checksum matches the original, the file is authentic. If it differs, the file has been tampered with.


----------------------------------------------------PROBLEM 3 ----------------------------------------------------------------


“I want to keep a private file safe so nobody else can read it.”


1.  Create a secret file

  -We are gonna use the update.txt in the second problem for example you want to pass it to a friend securely.


2. Then we are gonna encrypt the file using openssl AES 256

   openssl enc -aes-256-cbc -salt -pbkdf2 -in update.txt -out update.enc


   openssl enc
    - Tells OpenSSL to use the encryption (enc) function.

  aes-256-cbc
    - Specifies the AES encryption algorithm with a 256-bit key in CBC mode. Cipher Block Chaining (CBC) mode is secure when a unique IV is used for each encryption, but it can be vulnerable to padding oracle attacks if not implemented carefully.

  salt
    - Adds a random salt to the encryption process, which helps to protect against dictionary attacks.

  pbkdf2
    - This is a key derivation function that applies a cryptographic hash function multiple times to a password and a salt, producing a derived key. It makes brute-force attacks more difficult.

  -in update.txt
    - Specifies the input file to encrypt.

  -out update.enc
    - Specifies the output file for the encrypted data.

3. Try to open the file cat update.enc you see gibberish or random characters.

4. To decrypt the file, use the following command:
   openssl enc -d -aes-256-cbc -in update.enc -out update.dec

   then open the file using cat update.dec to see the original content





   REFERENCES

   - https://stackoverflow.com/questions/16056135/how-to-use-openssl-to-encrypt-decrypt-files
   - https://www.youtube.com/watch?v=Azp_zDgFAGk&t=147s
   - https://www.youtube.com/watch?v=43yPfJUxRCQ 
   - https://docs.openssl.org/master/man1/openssl-s_client/#options
   - https://docs.openssl.org/master/man1/openssl-enc
   - https://www.openssl.org/docs/manmaster/man1/openssl-dgst.html
   - https://stackoverflow.com/questions/7885785/using-openssl-to-get-the-certificate-from-a-server

