+++
title = "Faillock"
date = 2026-05-15
description = "My PC locked me out today."

[extra]
toc = true

[taxonomies]
tags = ["Linux", "authentication", "faillock", "passwords"]
+++

After over eight years of using primarly Linux as my OS of choice, today was the first time that my system has locked me out for failed password attempts. It wasn't that I had forgotten by password. I was simply in a hurry and made a typo three times in row, and, _apparently_, the [`pam_faillock.so` PAM module](https://www.man7.org/linux/man-pages/man8/pam_faillock.8.html) is configured to lockout the user for _fifteen_ minutes after three failed login attempts. At least on Arch Linux (btw) anyway. 

If you're like me, and think that lockout policy is just a _little_ aggressive, then look no further. I'll get you sorted. The default location for the `pam_faillock.so` module is `/etc/security/faillock.conf`, and in that file you'll find an entry titled `deny` set to 3.

```conf
# Deny access if the number of consecutive authentication failures
# for this user during the recent interval exceeds n tries.
# The default is 3.
deny = 3
```

Swap that sucker to a more agreeable number. I picked 10. The next time you restart your system, the new default will be set! If you've somehow locked a user session, but you still have access on the system as a root user then you can reset the lockout with the following command without needing to restart.

```shell
faillock --user YourUsername --reset
```

Happy hacking!
