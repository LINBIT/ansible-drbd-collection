# Linbit\.Drbd Release Notes

**Topics**

- <a href="#v0-9-7">v0\.9\.7</a>
    - <a href="#minor-changes">Minor Changes</a>
    - <a href="#bugfixes">Bugfixes</a>
This changelog describes changes after version 0\.9\.6\.

<a id="v0-9-7"></a>
## v0\.9\.7

<a id="minor-changes"></a>
### Minor Changes

* drbd\_install \- optional DRBD Proxy package installation via <code>drbd\_install\_drbdproxy\: true</code>\.
* drbd\_install \- optional masking of <code>drbd\-shutdown\.service</code> via <code>drbd\_install\_mask\_shutdown\_service\: true</code> for setups that manage DRBD shutdown out of band\.

<a id="bugfixes"></a>
### Bugfixes

* drbd\_install \- flush handlers at end of role so subsequent plays see the freshly installed kernel module without waiting for the global post\-task flush\.
