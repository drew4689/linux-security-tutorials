= Setup of a ssl certificate with let's encrypt
Clément Levallois <clementlevallois@gmail.com>
2017-04-09

last modified: {docdate}

:icons!:
:asciimath:
:iconsfont:   font-awesome
:revnumber: 1.0
:example-caption!:
:sourcedir: ../../../main/java

//ST: 'Escape' or 'o' to see all sides, F11 for full screen, 's' for speaker notes
//ST: !

== System
//ST: !

- I use Debian, version 8.7 (http://www.pontikis.net/blog/five-reasons-to-use-debian-as-a-server[why?])
- Vi is used as a text editor in the following


== Why SSL?
//ST: !

For users, no SSL shows as "http://" in front of a website address, and with SSL it shows as "https://"

"https" means that the connection between the user and the website is encrypted. It is useful for:

//ST: !

- human to machine communication: https keeps the email you write private on its way to gmail.com
- machine to machine communication: the api secret used by a machine to authenticate in a GET or PUT request is kept private.

//ST: !


== Installing the Certbot by Let's Encrypt
//ST: !

https://letsencrypt.org/[Let's Encrypt] is a product launched in 2015 by the https://www.eff.org/[Electronic Frontier Foundation (EFF)] providing SSL certification for free, and made easy.

The "certbot" is the EFF's latest package to let your server use let's encrypt capabilities.

So, let's install the certbot:

//ST: !

 sudo vi /etc/apt/sources.list.d/sources.list

In this file, add a line `deb http://ftp.debian.org/debian jessie-backports main`

//ST: !
Then:

  sudo apt-get update

 sudo apt-get install certbot -t jessie-backports

//ST: !

- make sure your domain already points to the IP of your server (with a DNS record)
- make sure your firewall allows port 443 (with https://help.ubuntu.com/community/UFW[ufw]: just do sudo ufw allow 443).

Then:

//ST: !

 certbot certonly

-> in the interactive window, choose "standalone". Follow the instructions.

That's it. Certificates get installed at:

 /etc/letsencrypt/live/yourdomainname.com

== Automatic renewal of SSL certificates
//ST: Automatic renewal of SSL certificates

Certificates expire after 90 days, so renewing them manually and regularly is a pain.

Thanks to certbot, they will renew themselves automatically, you don't need to add any script.
Just check that it indeed works:

//ST: !

 certbot renew --dry-run

(this will not renew them, but just simulate the action)

//ST: !

This command is useful because you may realize that your port 443 needs to be open for the renewal to succeed.
With Nginx or another reverse proxy running, 443 is already in use so the the renewal will fail.

//ST: !


Solution for nginx:

- with root privileges, edit a https://help.ubuntu.com/community/CronHowto[crontab]:

 crontab -e

Add the following line:

@monthly certbot renew --pre-hook "sudo systemctl stop nginx" --post-hook "sudo systemctl start nginx"

//ST: !

This will test every month the need to renew certificates.
Only when there is a need, nginx will be stopped before then restarted after the operation.

== the end
//ST: The end!

//ST: !

Author of this tutorial: https://twitter.com/seinecle[Clement Levallois]

All resources on linux security: https://seinecle.github.io/linux-security-tutorials/
http://www.clementlevallois.net

Or get in touch via Twitter: https://www.twitter.com/seinecle[@seinecle]
