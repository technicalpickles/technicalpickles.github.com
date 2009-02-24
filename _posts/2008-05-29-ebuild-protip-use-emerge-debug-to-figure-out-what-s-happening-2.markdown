--- 
permalink: /posts/ebuild-protip-use-emerge-debug-to-figure-out-what-s-happening-2.html
title: "Ebuild Protip: Use emerge --debug to figure out what's happening"
tags: 
- ebuild
- ebuild protip
- gentoo
layout: post
---
Often enough, something unthinkable will happen while your trying to build an ebuild. Something that is beyond reason. It just doesn't make sense. You could have SWORN you were in the correct directory...

Enter `emerge --debug`. This will spit out a ton of information, including every command that runs through bash.

This is useful for tracking issues like:

 * Not being in the correct directory
 * Files not being where you expect them to be
 * Using the wrong variable (such that they evaluate to empty string)
 * Having incorrect file globs
 
Here's how you invoke it:

<pre class="terminal unix"><samp class="shell prompt">#</samp> <kbd class="shell">emerge --debug <var>somepackage</var></kbd></pre>

Here's a sample snippet of the output:

<pre>+ local retval
+ src_install
+ local bits
+ use x86
+ useq x86
+ local u=x86
+ local found=0
+ [[ x == \! ]]
+ [[ -n ^(alpha|alsa_cards_aoa|alsa_cards_aoa-fabric-layout|alsa_cards_aoa-onyx|alsa_cards_aoa-soundbus|alsa_cards_aoa-soundbus-i2s|alsa_cards_aoa-tas|alsa_cards_aoa-toonie|alsa_cards_armaaci|alsa_cards_at91-soc|alsa_cards_at91-soc-eti-b1-wm8731|alsa_cards_au1x00|alsa_cards_harmony|alsa_cards_powermac|alsa_cards_pxa2xx-i2sound|alsa_cards_pxa2xx-soc|alsa_cards_pxa2xx-soc-corgi|alsa_cards_pxa2xx-soc-poodle|alsa_cards_pxa2xx-soc-spitz|alsa_cards_pxa2xx-soc-tosa|alsa_cards_sa11xx-uda1341ts|alsa_cards_sun-amd7930|alsa_cards_sun-cs4231|alsa_cards_sun-dbri|altivec|amd64|aqua|arm|bmp|bmpx|bootstrap|build|coreaudio|crosscompile_opts_.*|elibc_.*|elibc_Darwin|elibc_DragonFly|elibc_FreeBSD|elibc_NetBSD|elibc_OpenBSD|elibc_glibc|elibc_uclibc|emul-linux-x86|hppa|ia64|ibm|infopipe|kernel_.*|kernel_Darwin|kernel_FreeBSD|kernel_linux|m68k|mips|mplayer-bin|multilib|n32|n64|pam_console|pbbuttonsd|ppc|ppc64|ppcsha1|s390|selinux|sh|sparc|sparc-fbsd|uclibc|ultra1|userland_.*|userland_BSD|userland_Darwin|userland_GNU|video_cards_impact|video_cards_newport|video_cards_sunbw2|video_cards_suncg14|video_cards_suncg3|video_cards_suncg6|video_cards_sunffb|video_cards_sunleo|video_cards_suntcx|vis|x86|x86-fbsd|xmms)$ ]]
+ [[ -n install ]]
+ hasq install config depend info prerm postrm postinst
+ [[  config depend info prerm postrm postinst  == *\ \i\n\s\t\a\l\l\ * ]]
+ [[ ebuild != binary ]]
+ [[ x86 =~ ^(alpha|alsa_cards_aoa|alsa_cards_aoa-fabric-layout|alsa_cards_aoa-onyx|alsa_cards_aoa-soundbus|alsa_cards_aoa-soundbus-i2s|alsa_cards_aoa-tas|alsa_cards_aoa-toonie|alsa_cards_armaaci|alsa_cards_at91-soc|alsa_cards_at91-soc-eti-b1-wm8731|alsa_cards_au1x00|alsa_cards_harmony|alsa_cards_powermac|alsa_cards_pxa2xx-i2sound|alsa_cards_pxa2xx-soc|alsa_cards_pxa2xx-soc-corgi|alsa_cards_pxa2xx-soc-poodle|alsa_cards_pxa2xx-soc-spitz|alsa_cards_pxa2xx-soc-tosa|alsa_cards_sa11xx-uda1341ts|alsa_cards_sun-amd7930|alsa_cards_sun-cs4231|alsa_cards_sun-dbri|altivec|amd64|aqua|arm|bmp|bmpx|bootstrap|build|coreaudio|crosscompile_opts_.*|elibc_.*|elibc_Darwin|elibc_DragonFly|elibc_FreeBSD|elibc_NetBSD|elibc_OpenBSD|elibc_glibc|elibc_uclibc|emul-linux-x86|hppa|ia64|ibm|infopipe|kernel_.*|kernel_Darwin|kernel_FreeBSD|kernel_linux|m68k|mips|mplayer-bin|multilib|n32|n64|pam_console|pbbuttonsd|ppc|ppc64|ppcsha1|s390|selinux|sh|sparc|sparc-fbsd|uclibc|ultra1|userland_.*|userland_BSD|userland_Darwin|userland_GNU|video_cards_impact|video_cards_newport|video_cards_sunbw2|video_cards_suncg14|video_cards_suncg3|video_cards_suncg6|video_cards_sunffb|video_cards_sunleo|video_cards_suntcx|vis|x86|x86-fbsd|xmms)$ ]]
+ hasq x86 elibc_glibc kernel_linux userland_GNU x86
+ [[  elibc_glibc kernel_linux userland_GNU x86  == *\ \x\8\6\ * ]]
+ return 0
+ bits=32
+ dobin release32/glsldb
+ dolib release32/libs/libdlsym.so-32 release32/libs/libglsldebug.so-32 release32/libs/plugins32
install: omitting directory `release32/libs/plugins32&#39;
+ die &#39;dolib failed&#39;</pre>

In this snippet, we see `dolib` fails because it trying to work on a directory. The specific cause was a bad glob in the `dolib` call that was catching said directory, instead of just the .so files.