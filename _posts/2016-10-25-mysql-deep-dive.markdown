---
layout: post
date: 2016-10-25 09:57 -0500
title: mysql deep dive
categories: 
---
I think I want to know more about mysql. I downloaded the last snapshot of mariadb 10.2 and unpacked it. A quick browse of the code shows there are a few high level functions,
and most of the code is written in a restricted subset of C++, particularly the existence
of functions like new defined to avoid requiring libstdc++.

I really feel like reading C code sometimes is like peering into the machine. Witness this block from the base64 code:

```
static char base64_table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
                             "abcdefghijklmnopqrstuvwxyz"
                             "0123456789+/";
static int8
from_base64_table[]=
{
/*00*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-2,-2,-2,-2,-2,-1,-1,
/*10*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*20*/  -2,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,62,-1,-1,-1,63, /*  !"#$%&'()*+,-./ */
/*30*/  52,53,54,55,56,57,58,59,60,61,-1,-1,-1,-1,-1,-1, /* 0123456789:;<=>? */
/*40*/  -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,13,14, /* @ABCDEFGHIJKLMNO */
/*50*/  15,16,17,18,19,20,21,22,23,24,25,-1,-1,-1,-1,-1, /* PQRSTUVWXYZ[\]^_ */
/*60*/  -1,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40, /* `abcdefghijklmno */
/*70*/  41,42,43,44,45,46,47,48,49,50,51,-1,-1,-1,-1,-1, /* pqrstuvwxyz{|}~  */
/*80*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*90*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*A0*/  -2,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*B0*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*C0*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*D0*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*E0*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
/*F0*/  -1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1
};
```

These sorts of raw mappings just look right. If you've read Applied Cryptography,
this is reminiscent of about half of the code there (that's a lot of many ciphers).

Well, I'll keep on reading, the Sasha Pachev internals book (while a bit dated, nearly 10 years old) probably reflects good portions of the high level organization, there is no innobase directory in the mariadb code, but that might be due to moving things around internally. The remarks about checking code out from bitkeeper (remember a world before github?) are quaint. 