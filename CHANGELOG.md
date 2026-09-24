# Linbit\.Drbd Release Notes

**Topics**

- <a href="#v0-9-9">v0\.9\.9</a>
    - <a href="#minor-changes">Minor Changes</a>
    - <a href="#bugfixes">Bugfixes</a>
- <a href="#v0-9-8">v0\.9\.8</a>
    - <a href="#bugfixes-1">Bugfixes</a>
- <a href="#v0-9-7">v0\.9\.7</a>
    - <a href="#minor-changes-1">Minor Changes</a>
    - <a href="#bugfixes-2">Bugfixes</a>

This changelog describes changes after version 0\.9\.6\.

<a id="v0-9-9"></a>
## v0\.9\.9

<a id="minor-changes"></a>
### Minor Changes

* drbd\_install \- add <code>drbd\_install\_firewalld\_zone</code> to open the DRBD replication ports in a specific firewalld zone\, for example a zone bound to the replication network source\. Empty\, the default\, uses the firewalld default zone\. UFW ignores it \([https\://github\.com/LINBIT/ansible\-linstor\-collection/issues/2](https\://github\.com/LINBIT/ansible\-linstor\-collection/issues/2)\)\.
* drbd\_install \- add <code>drbd\_install\_spaas</code> \(default <code>true</code>\)\. Set it to <code>false</code> on air\-gapped hosts so the DKMS build skips LINBIT spatch\-as\-a\-service instead of waiting for a connection timeout\.
* drbd\_install \- list Ubuntu 26\.04 and SLES 15 SP7 as supported platforms\, and drop SLES 15 GA through SP3\, which are past their support windows\.
* drbd\_install \- on the Debian OS family\, install <code>coccinelle</code> and retry when the DKMS build fails\, so hosts that cannot reach LINBIT spatch\-as\-a\-service generate the compat patch locally \([https\://github\.com/LINBIT/ansible\-drbd\-collection/issues/1](https\://github\.com/LINBIT/ansible\-drbd\-collection/issues/1)\)\.
* drbd\_install \- on the Red Hat OS family\, report the tail of the DKMS <code>make\.log</code> when the DKMS build fails instead of the bare <code>dkms autoinstall</code> error\.
* drbd\_install \- retry package installs up to three times so transient repository failures do not fail the role\.
* galaxy\.yml \- point the repository\, documentation\, and issues URLs at the public GitHub repository\.
* galaxy\.yml \- require <code>community\.general</code> <code>\>\=11\.0\.0</code> and drop the <code>\<12\.6\.0</code> upper bound\.

<a id="bugfixes"></a>
### Bugfixes

* drbd\_install \- document the <code>drbd\_install\_package\_state</code> default as <code>present</code> in the README and argument spec\, matching the actual default\.
* drbd\_install \- gather the OS facts the role needs when they are missing\, so it works under <code>gather\_facts\: false</code> and with tag filters that skip fact gathering\.
* drbd\_install \- install <code>perl\-interpreter</code> with the DKMS prerequisites on the Red Hat OS family\. The <code>drbd\-dkms</code> RPM does not require it\, and the DRBD compat step fails without it on UEK hosts\.
* drbd\_install \- install the kernel headers that match the running kernel flavor\, so DKMS builds DRBD on cloud\, UEK\, Real Time\, and 64K\-page kernels\. The role uses the <code>linbit\.common\.dkms\_kernel\_headers</code> filter instead of assuming the generic headers on the Debian OS family and <code>kernel\-devel</code> on the Red Hat OS family\.
* drbd\_install \- on UEK\, install <code>kernel\-uek\-modules</code> for the running kernel\. The unversioned package pulled in the newest kernel and left the running kernel without the <code>lru\_cache</code> module that DRBD needs\.

<a id="v0-9-8"></a>
## v0\.9\.8

<a id="bugfixes-1"></a>
### Bugfixes

* drbd\_install \- pin the DKMS verification to the running kernel and add a post\-build assert\, so the role fails on real DKMS build failures for the running kernel instead of accepting a stale <code>installed</code> status from an unrelated kernel slot\.

<a id="v0-9-7"></a>
## v0\.9\.7

<a id="minor-changes-1"></a>
### Minor Changes

* drbd\_install \- optional DRBD Proxy package installation via <code>drbd\_install\_drbdproxy\: true</code>\.
* drbd\_install \- optional masking of <code>drbd\-shutdown\.service</code> via <code>drbd\_install\_mask\_shutdown\_service\: true</code> for setups that manage DRBD shutdown out of band\.

<a id="bugfixes-2"></a>
### Bugfixes

* drbd\_install \- flush handlers at end of role so subsequent plays see the freshly installed kernel module without waiting for the global post\-task flush\.
