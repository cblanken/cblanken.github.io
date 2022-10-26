+++
title = "PicoCTF 2022 - SideChannel"
date = 2022-05-29

[taxonomies]
categories = ["CTF Writeup"]
tags = ["ctf", "infosec", "python"]
+++
The name itself "SideChannel" seems to indicate a [side-channel attack](https://en.wikipedia.org/wiki/Side-channel_attack), so we should keep that in mind going forward.

To start, we're given a short description
> There's something fishy about this PIN-code checker, can you figure out the PIN and get the flag?


We're then offered a download for a program called `pin_checker`.
Running the `pin_checker` binary will give us the following output:
```console
$ ./pin_checker
Please enter your 8-digit PIN code:
```

Let's try entering a PIN 

```console
$ ./pin_checker
Please enter your 8-digit PIN code:
00000000
8
Checking PIN...
Access denied.
```
 
Okay, so it looks like a classic PIN entry. The program will tell us if we've found the correct pin. It also requires an 8-digit pin as you might expect. Anything longer or shorter will give an error. For example:

Short PIN
```console
$ ./pin_checker
Please enter your 8-digit PIN code:
12345
5
Incorrect length.
```

Long PIN
```console
$ ./pin_checker
Please enter your 8-digit PIN code:
123456789
9
Incorrect length.
```

### Brute-force
At this point, I decided to try a simple brute-force attack on the PIN, just to see if it was feasible, so I wrote a python script to do just that.
```python
# pins.py
import sys
from subprocess import Popen, PIPE

pins = ("{:08}".format(x) for x in range(0, 99999999))
try:
    for pin in pins:
        proc = Popen(["./pin_checker"], stdout=PIPE, stdin=PIPE, stderr=PIPE)
        stdout_data = proc.communicate(input=bytes(pin, 'utf-8'))[0]
        stdout_string = str(stdout_data)
        if 'granted' in stdout_string:
            print("FOUND PIN! PIN: {}", pin)
            break
        print(f"PIN: {pin}, {str(stdout_data).strip()}")
except KeyboardInterrupt:
    print("Program interrupted.")
    sys.exit(0)
```

`pin.py` output
```console
$ time python pins.py
PIN: 00000000, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000001, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000002, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000003, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000004, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000005, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
...
PIN: 00000095, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000096, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000097, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000098, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'
PIN: 00000099, b'Please enter your 8-digit PIN code:\n8\nChecking PIN...\nAccess denied.\n'

real    0m12.780s
user    0m12.311s
sys     0m0.435s
```

Unfortunately timing just the first 100 PINs, my system took over 12 seconds.
If we do a little math we can find out the minimum amount of time it might take for the program to check every PIN. First let's find the number of possible PIN codes. This describes a [permutation](https://en.wikipedia.org/wiki/Permutation) in mathematics. Suffice to say we can calculate the number of possible PINs with this formula 

$$ permutations = \frac{n!} {(n-r)!}" $$

where $n$ is the number of possible values for each digit, and $r$ is the number of digits in our PIN which gives us:

$$
permutations = \frac{10!} {(10-8)!} = 10 \times 9 \times 8 \times 7 \times 6 \times 5 \times 4 \times 3 = 1814400
$$ 

That's a lot of permutations. Since it will take about $12s$ per 100 PINs on my machine that comes out to 

$$
total time = \frac{1814400} {100} \times 12s = 217728\,s= 3628.8\,min= 60.48\,hours
$$

This is, of course, assuming the time to check each PIN is consistent (we'll find out shortly that's not the case). Regardless this is a good sanity check.

### Timing attack
Okay, now that we've ruled out a brute-force attempt, let's go back to the title of the challenge, *SideChannel*. Side-channel attacks are essentially observing a process and gleaning information from it based on timing, power consumption, or even sound. They can get very complicated. In our case the only path that really makes sense (particularly for a beginner CTF) is a timing-based attack.

So, let's start by fuzzing `pin_checker` with a few different pins and monitoring the execution time to see where that gets us.

Just to highlight a few things I'll be using the following pipeline to clean up the output and just focus on the real elapsed time by the process. You can see an in depth breakdown of the [pipeline](https://explainshell.com/explain?cmd=%24+%2Fusr%2Fbin%2Ftime+-f+2%3E%261+%22%5Cn%25E%22+.%2Fpin_checker+%3C%3C%3C+00000000+%7C+tail+-n1+0%3A00.13) on [explainshell.com](https://explainshell.com/). Just know that we're sending a PIN code (`00000000` in this case) to `./pin_checker` and getting back the elapsed time in `hours:minutes:seconds`.
```console
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 00000000 | tail -n1
0:00.13
```

Following are all the permutations of the first digit of the PIN while leaving the remaining digits as zero.
```console
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 00000000 | tail -n1
0:00.12
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 10000000 | tail -n1
0:00.13
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 20000000 | tail -n1
0:00.13
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 30000000 | tail -n1
0:00.13
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 40000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 50000000 | tail -n1
0:00.13
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 60000000 | tail -n1
0:00.13
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 70000000 | tail -n1
0:00.14
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 80000000 | tail -n1
0:00.13
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 90000000 | tail -n1
0:00.13
```

If you look carefully, you'll see that when we sent the PIN `40000000`, the elapsed time seemed to double. That's certainly interesting. We know that `pin_checker` must be checking the PIN code for a valid entry. Maybe it takes longer to verify the PIN when some of the digits are correct? Let's keep going.

Keeping the first digit as 4, lets check the second digit.
```console
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 40000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 41000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 42000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 43000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 44000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 45000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 46000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 47000000 | tail -n1
0:00.25
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 48000000 | tail -n1
0:00.37
$ /usr/bin/time -f 2>&1 "\n%E" ./pin_checker <<< 49000000 | tail -n1
0:00.25
```
We notice that the elapsed time has increased again when sending the PIN `48000000`. It hasn't doubled this time, but it has markedly increased while the elapsed time for the others remained the same.

I won't go through each step here, but each digit can be deduced from the above process. After checking each digit for an increased elapsed time we can find the correct PIN. I'll leave that up to the reader though =)

Once you determine the correct PIN you can check it against `pin_checker`.
```console
$ ./pin_checker <<< XXXXXXXX
Please enter your 8-digit PIN code:
8
Checking PIN...
Access granted. You may use your PIN to log into the master server.
```
