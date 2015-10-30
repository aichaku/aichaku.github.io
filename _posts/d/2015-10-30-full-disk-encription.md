---
layout: post
title: Full Disk Encryption in Android
category: d
tags: [fde, android, dm_crypt, vold]
---

Developing an Android device gets gradually more difficult. As the security threats against Android devices are increasing, Google has adopted FDE since the release of Lollipop, which means Full Disk Encryption. The outline of this feature is like the following.

<img class="post-img" src="http://techsolutionsmd.com/Security/images/FDE.png">
This illustration shows the coverage of FDE.

### Every block disk has to be encrypted as a whole.
In fact, this feature is not new. **[dm_crypt][1]** is one of the key security features in Linux since 2.6 version. As per dm_crypt, it is kind of a device mapper target, so it can see the whole area of a disk and encrpyt the data. It also has a range of advantages compared to its predecessor such as cryptoloop. However, I am not going to go there because it is out of the scope of this article. XTS, LRW and ESSIV are the keywords to understand how dm_crypt works. If you want more information regarding these theory, you might have to find them in another articles. Anyway, what I would like to say is that FDE is not a new technology, but a adaptation for Android to use Linux feature more effectively. FDE uses 128 or higer bits to make a encrypt key. By this key the whole disk will be scrambled and nobody can understand what the data is without the key.

### How FDE meets the basic information security
The [3 basic principle in information security][2] is confidentiality, integrity and availablity. It is obvious that FDE can provide these features, and I will elaborate more.

#### Confidentiality
As the data in a disk has been scambled with a 128 or more bits key which is stored in a hardware security area such as Trust Zone. In this regard, even though an attacker can get the user data from an Android device, the data would be useless unless the attacker obtain the key. However, the key will be place in secured area by hardware, so it is almost close to impossible to get it.

#### Integrity
Say that a hacker implant a virus in Android device. If FDE is enabled, in the process of decrypting, the virus will be transformed to unexecutable weird data. So simple. So no one can affect to the existing data as long as they don't have the previlege to access the encryption key.

#### Availablity
Needless to say that this is of course abled to obtain. Isn't it?

### How it works

 * Encrytion Algorithm: 128 Advanced Encryption Standard (AES) with cipher-block chaining (CBC) and ESSIV:SHA256.

OpenSSL will encrypt the master key with 128 AES. And this will be done by user input such as PIN, password and pattern or by a default value. Read [Google's guide][3] for this.

 > Upon first boot, the device creates a randomly generated 128-bit master key and then hashes it with a default password and stored salt. The default password is: "default_password" However, the resultant hash is also signed through a TEE (such as TrustZone), which uses a hash of the signature to encrypt the master key.


 * vold

Because vold can listen to uevent from kernel and it can also communicate with MountService in Framwork, this is the right place to work with FDE. On moounting a new block device it can issue some changes in properties which make init to do encryption or decryption. To read more about vold, visit [here] [4]and [here][5] you can see the source code.

 * Flows

Please refer to [the official Android guide][6].


### Questions

* If a user downloaded a video clip to the space on sdcard, are there things to do with FDE?

[1]: https://en.wikipedia.org/wiki/Dm-crypt
[2]: https://en.wikipedia.org/wiki/Information_security#Key_concepts
[3]: https://source.android.com/devices/tech/security/encryption/#how_android_encryption_works
[4]: http://www.slideshare.net/wiliwe/android-storage-vold
[5]: https://android.googlesource.com/platform/system/vold/
[6]: https://source.android.com/devices/tech/security/encryption/#flows