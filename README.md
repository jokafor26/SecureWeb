# SecureWebTransactions

Secure Web Transactions

Practicing how to create certificates and other web transactions in these labs.
Web Browser Certificate Management
Viewing the encryption protocols used for web transaction on Firefox which are SSL 3.0 and TLS 1.0

  ![Image](https://github.com/user-attachments/assets/6f200074-5a61-4ae3-8b28-16f71c79f290)

Then I viewed a list of Certificate Authorities that are my computer when the browser was installed are America Online & AOL Time Warner Inc

![Image](https://github.com/user-attachments/assets/38c82344-40cd-4f89-9400-c994fda46792)
 
Among with the names of the company, you will see the certificates of these companies. These are called “root certificates”. Here is one and when I clicked the details tab, I could see the algorithm used which are SHA1 and RSA encryption. The subjects public key is 2048 bits, and the network protocol listed to verify the certificates is the Online Syatus Protocol.

 ![Image](https://github.com/user-attachments/assets/003472ee-f034-4617-8420-9a180600ae97)

Creating SSL Certificates Using OpenSSL
In this part I would install my own secure web server which utilizes SSL/TLS for secure communications. 
To generate the Certificate Signing Request (CSR), I will need to create my own private/public key first. 
sudo openssl genrsa -des3 -out server.key 1024 which indicates to OpenSSL that you want to generate a key pair, des3 indicates that the private key should be encrypted and protected by a passphrase, out indicates the file name in which to store the results, and 1024 indicates the number of bits of the generated key. My passphrase is Linux.

 ![Image](https://github.com/user-attachments/assets/91a62fd0-e551-4d43-9a6b-cc0ba6ba33ab)

I will store my passphrase in a password script, so that Apache2 will not prompt you for the pass phrase whenever Apache2 is started or restarted. I ran the command: sudo gedit /etc/apache2/ssl_passphrase. The ssl-passphrase script will be opened in a text editor. Entered my private key pass phrase in-between the pink quotes replacing 123456. Then clicked save.

 ![Image](https://github.com/user-attachments/assets/0c5db596-0ee3-47dd-b654-1dcce4238970)

Next, I created a certificate signing request with the private/public key I had just created. This command will prompt for a series of things: When prompted enter the values as follows. 
Country Name: US 
State or Province Name: New York 
Locality Name: New York 
Organization Name: Pace University 
Organizational Unit Name: CSIS-IT300 
Common Name: www.BadStore.net 
Email: my email
A challenge password: enter another password that you can remember. 
An Optional Company Name: CSIS
Then I created the Certificate Signing Request by running the following command at the terminal prompt, making sure I’m still in the /etc/apache2/ssl directory. sudo openssl req -new -key server.key -out server.csr. I was then prompted to enter my private key passphrase, and also prompted to enter the values which were addressed above. I may run command ls in the ssl directory to see a file called server.csr when I’m finished. This is my certificate signing request.
 
![Image](https://github.com/user-attachments/assets/9bc0b452-f20c-477a-95c4-805a06e964df)

Then I created my self-signed certificate. Made sure I was still in the /etc/apache2/ssl directory. The certificate signing request (CSR) has to be signed by a Certificate Authority (CA). For testing purpose, I did not ask a commercial CA (such as Verisign) to sign my certificate but sign the CSR myself, which is called self-signing. In this case, I am my own CA. The following command will create a self-signed certificate that will last for 365 days sudo openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt. The command will prompt me to enter my private key passphrase. Once i enter the correct passphrase, my certificate will be created and it will be stored in the server.crt file. The command, took the certificate signing request, plus my private key in order to make my self-signed certificate. I ran command ls in the /etc/apache2/ssl directory and I will see server.crt, server.csr, and server.key.  Now I have created my very own self-signed certificate. 

![Image](https://github.com/user-attachments/assets/9ce421ae-8628-4891-982e-d6d381572195)

Configuring Apache2 with BadStore.net
I first enabled the SSL module for Apache2 with sudo a2enmod ssl. 
** To disable the SSL module for Apache2, the command is sudo a2dismod ssl ** 
Then I did a restart of Apache2 which is required for the SSL module to be effective. sudo /etc/init.d/apache2 restart. Apache restarted with no errors. Creation of a virtual host for the secured BadStore.net website was necessary so that when the FQDM is entered into a web browser, the secured BadStore.net website will be available at that address. Started by copying the default template which will be modified in the following steps. sudo cp /etc/apache2/sites-available/default /etc/apache2/sites-available/www.badstore.net. The template copy is now named www.badstore.net and is located in the /etc/apache2/sites-available/ directory.

 ![Image](https://github.com/user-attachments/assets/eec76115-7d06-43fe-a708-40578a5a0a2e)

I had to edit the content of the template copy. This is a very important step. I made sure it is modified correctly. sudo gedit /etc/apache2/sites-available/www.badstore.net. Changed the VirtualHost port from 80 to 443. The first line of the file will look like the following: <VirtualHost *:443> Next declared the server name and the fully qualified domain name plus the HTTPS port number after the ServerAdmin line. ServerName www.badstore.net:443. Changed the DocumentRoot to point to the badstore web directory. DocumentRoot /var/www/badstore. Also changed the <Directory /var/www/> to reflect the badstore web directory <Directory /var/www/badstore>. In the line before ErrorLog /var/log/apache2/error.log, I entered these values 
SSLEngine On 
SSLCertificateFile /etc/apache2/ssl/server.crt 
SSLCertificateKeyFile /etc/apache2/ssl/server.key 
These entries tell the virtual host where the SSL certificate and key are located and to turn on SSL. After I saved the file and enabled the website by using the sudo a2ensite www.badstore.net  command.

 ![Image](https://github.com/user-attachments/assets/ead8bf53-ce69-4a36-9f59-46d29964d4d4)

I edited the /etc/hosts file to resolve the www.badstore.net website to 127.0.0.1 since the website is hosted locally with the sudo gedit /etc/hosts command. Found the line that began with 127.0.0.1, removed the localhost text and in its place enter www.badstore.net. Saved it and exit.

 ![Image](https://github.com/user-attachments/assets/d2e5535f-8775-446d-b729-56839a451f8c)

Running a Secure Web Server
All the web server settings are contained in the Apache2 server configuration files. I needed to restart Apaceh2 for the SSL certificate settings to take effect. Ran the command: sudo /etc/init.d/apache2 restart. Apache2 restarted and gave me an [ OK] message. Opened Firefox and visited http://localhost and was greeted with a message.

 ![Image](https://github.com/user-attachments/assets/2dfc16e3-b4a7-446b-8675-62222581c670)

Then in my browser I visited https://www.badstore.net

 ![Image](https://github.com/user-attachments/assets/93d9fc68-56fc-4995-8f9b-92e3ce06f9a8)

Shows that it is HTTPS and not HTTP. Firefox displayed a message stating Secure Connection Failed due to the certificate that is used. In Firefox, the certificate is not trusted because it is self-signed. I accepted the certificate anyway and add an exception. Clicked the I Understand the Risks link, clicked the Add Exception button, and clicked the Get Certificate button. I can click the View button to see the self-signed certificate that I had created. Made sure the permanently store this exception checkbox is selected and click the Confirm Security Exception button.
 
![Image](https://github.com/user-attachments/assets/69ff068b-e974-4c93-af14-bbe05517ed93)

I saw the silver lock on the bottom of the browser indicating a secure web page and clicked it.
 ![Image](https://github.com/user-attachments/assets/d692aa61-fe16-4adf-9784-04dd122f4101)

I viewed the certificate button and showed validation of the certificate. 
 ![Image](https://github.com/user-attachments/assets/6584935c-df20-49ca-a947-98af7d9d2762)

Shows that I am the issuer of the certificate. The functionality of the certificate is to provide to the user using the browser that the web server they are logging into or accessing is from a trusted source.

 ![Image](https://github.com/user-attachments/assets/7b54496a-d460-4e48-b2c7-627870a8d537)

Client SSL Handshakes
Here will show you what information is transmitted between the secure web server and the browser during SSL handshaking. I captured the results in a text file (handshake.txt).  
In the terminal window, logged in as the root user with command: sudo su. Changed my directory to point to the Desktop directory with command: cd /home/user/Desktop. I will then capture the SSL handshake that takes place between the client browser and Apache2 web server with the following command: openssl s_client -connect www.badstore.net:443 > handshake.txt. I saw the web server return some certificate information and the phrase “Verify return:1”. This means that the server has acknowledged your request. At this point, I typed GET / HTTP/1.1. A new file created on my Ubuntu desktop called handshake.txt. It appeared with an orange lock icon in the upper right corner. The orange lock represents the read-only file permission. 

 ![Image](https://github.com/user-attachments/assets/a20781d6-55e6-4227-95d2-4a3e6d48a78f)


I double-click handshake.txt to open it in the gedit text editor. 

 ![Image](https://github.com/user-attachments/assets/83a20801-aa7f-4d51-82ee-ffaa04208281)

Note: The reason the web server sends this information is for me to see what is taking place during communication. The file contains the certificate in encrypted form, how many bytes were written, the cipher and size of the key. Also shows the master key and protocol and a session ticket.
