---
title: Beware of progsreiserfs package for CentOS 5 in dag RPM repository
author: kxn
type: post
date: 2008-01-19T04:38:46+00:00
url: /index.php/archives/184
views:
  - 1315
categories:
  - Tech Notes

---
Beware of non-functional progsreiserfs package for CentOS 5 in dag RPM repository, at least its fsck.reiserfs is just some shit that will say "Not implemented" when you want to fsck your reiserfs partition. Throw it away immediately if you do have reiserfs volumes, grab and build/install a reiserfs-utils RPM, which is easy to find at [rpmfind.net][1].

The progsreiserfs is rather weird, it puts all of its binaries in /usr/sbin and /usr/lib, therefore at boot time, CentOS will say "fsck.reiserfs not found" but it will continue with booting, leaving your reiserfs volumes unchecked. I was very angry when I saw the fsck.reiserfs said "Not implemented" after I had manually copied fsck.reiserfs and its dependency(Yes, the fsck.reiserfs binary is dynamically linked in that package...) to root partition. I don't know who had put this piece of unusable crap into dag repository and even tried to make it conflict with the usable one, reiserfs-utils. It doesn't really conflict, because fsck.reiserfs and mkfs.reiserfs are put into /usr instead of /, . I guess that packager doesn't know much about system boot process.

It is good that Redhat still maintains reiserfs-utils in their Fedora distribution, and RHEL/CentOS is mostly compatible with Fedora, so grabbing an SRPM from latest Fedora distribution and building on CentOS 5 works fine.

 [1]: http://rpmfind.net