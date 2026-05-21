# Linbit\.Drbd Release Notes

**Topics**

- <a href="#v0-9-8">v0\.9\.8</a>
    - <a href="#bugfixes">Bugfixes</a>
- <a href="#v0-9-7">v0\.9\.7</a>
    - <a href="#minor-changes">Minor Changes</a>
    - <a href="#bugfixes-1">Bugfixes</a>

This changelog describes changes after version 0\.9\.6\.

<a id="v0-9-8"></a>
## v0\.9\.8

<a id="bugfixes"></a>
### Bugfixes

* drbd\_install \- pin the DKMS verification to the running kernel and add a post\-build assert\, so the role fails on real DKMS build failures for the running kernel instead of accepting a stale <code>installed</code> status from an unrelated kernel slot\.

<a id="v0-9-7"></a>
## v0\.9\.7

<a id="minor-changes"></a>
### Minor Changes

* drbd\_install \- optional DRBD Proxy package installation via <code>drbd\_install\_drbdproxy\: true</code>\.
* drbd\_install \- optional masking of <code>drbd\-shutdown\.service</code> via <code>drbd\_install\_mask\_shutdown\_service\: true</code> for setups that manage DRBD shutdown out of band\.

<a id="bugfixes-1"></a>
### Bugfixes

* drbd\_install \- flush handlers at end of role so subsequent plays see the freshly installed kernel module without waiting for the global post\-task flush\.
