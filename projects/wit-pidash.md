![2018 Corporare Official Wit Pidash](/uploads/corporate/2018-corporare-official-wit-pidash.png "2018 Corporare Official Wit Pidash")
<!-- TITLE: wit-piDash -->
<!-- SUBTITLE: Production Ready wit-myDash personal display -->

# Intro
`wit-myDash` is the flagship WOZiTech project, having seen many redevelopments over the years, the latest using VueJS on Electron.

Typically, `wit-myDash` runs on `wit-pi`, taking on the specific persona `wit-piDash`, which includes local caching, licensing and signed authentication to `wit-serve`.

# Technology
VueJS Progressive Web Application (`wit-myDash`) PWA using `CacheStorage` for offline work working together with a local `wit-piDash` service to handle registration, licensing and authentication services.

`wit-piDash` can run offline, but content will need to be sync'd first by being online.

For content to be updated, the device will need to be put back online, whereby it will quickly resync. This can be a easy as simply re-attaching the WiFi USB dongle within the same wifi network used during initial registration of the device .