---
layout: default
---

# Armageddon

![Armageddon] (https://kedaegan.github.io/htb/Machine/Nix/Easy/Armageddon/images/Armageddon.png)

Drupal site that is vulnerable to [CVE-2018-7600] (https://nvd.nist.gov/vuln/detail/CVE-2018-7600). Once I had a command prompt I found the mysql credentials where I found a password hash. 
Once cracked I was able to login as a low priv user.

Once in I found the user had sudo rights to snap. Discovered it is vulnerable to dirty sock. Found a PoC that creates a user dirty_sock with full sudo rights.

## Enumeration

### Ports and Services

![PortsAndServices] (/images/ports.png)

### Software Installed

* PHP 5.4.16
* Drupal 7.56 (changelog http://10.129.43.32/CHANGELOG.txt)

### Nmap Scan

![NMAPScan] (/images/nmap.png)

### Initial Foothold

Found a changelog at http://10.129.43.32/CHANGELOG.txt and found the Drupal Version.
Drupal 7.56 is vulnerable to CVE-2018-7600
Downloaded and tried an exploit script

[Drupalgeddon2] (https://github.com/dreadlocked/Drupalgeddon2)

![Drupalgeddon2 Shell] (images/Drupalgeddon2Shell.png)

Found database info in /var/www/html/sites/default/settings.php

![Drupal SQL Settings] (images/Drupalgeddon2Shell.png)

Found username and password hash in drupal database in the Users table

![Drupal username and password hash] (images/PasswordHash.png)

Used john to crack the hash 

![Cracked Hash] (/images/HashCracked.png)

Looks like user BruceTheRealAdmin re-uses passwords. SSH as brucetherealadmin successfully.

#### User.txt Proof

![User Proof] (/images/UserProof.png)

### Privilege Escalation
Steps to root

#### Root.txt Proof
Screenshot of proof
