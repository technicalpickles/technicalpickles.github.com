--- 
permalink: /posts/stale-etc-portage-package-unmask
title: Stale /etc/portage/package.unmask
tags: 
- gentoo
- java
layout: post
---

			<p>As a recommendation to people who use /etc/portage/package.unmask regularly: You really ought to keep tabs on the thing that you're unmasking... and once it has been unmasked, you should remove the entries from package.unmask.</p>

<p>Here's a scenario: You heard about the new Java system being in package.mask, so you decided to try it. Cool. It has since come out of package.mask... but you still have it unmasked via /etc/portage/package.unmask. Now, we release a new revision of java-config that we put in package.mask for testing... and now you get the version that we are heavily tested, and it doesn't quite work 100%...</p>

<p>So, please, be mindful of your package.unmasks.</p>					