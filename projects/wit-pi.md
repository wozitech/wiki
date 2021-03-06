![Raspberry Pi 3 D Logo](/uploads/logos/raspberry-pi-3-d-logo.png "Raspberry Pi 3 D Logo"){.pagelogo}
<!-- TITLE: wit-pi -->
<!-- SUBTITLE: The WIT trailblazer -->

# Hardware Technology
Most of WIT (WOZiTech) products will resolve around the Raspberry PI. `wit-pi` is a custom Operating System provided by WOZiTech, with a set of customised peripherals, such as, proxity sensors, touchpads, pin pads, webcams, IP Cameras, motors, temperature sensors, solenoids, electrostatic sensors, et al, and installing WIT applications built specifically for the `wit-pi`, to provide tailored access solutions. For example, security, entertainment (music/movie streaming) and/or automation.

Note, you may observe some projects/products may be called by a slight variant in name: `wit-o` prefix. These are odroid alternatives; XU-4 (with 8 CPUs concurrent performance) or CU-2 (fast calculations) are required. More power than Raspberry PI, odroids are more expensive and much tricker to work with (it took me two weeks just to get Ubuntu Mate 16.04 installed to the XU-4!!!!).

# Software Technologies
All `wit-pi` applications will be using:
* [VueJS](https://vuejs.org/) - one of the best JS frameworks for building responsive applications
	* [Awesome Vue](https://github.com/vuejs/awesome-vue)
	* [VueJS Developer](https://vuejsdevelopers.com)
* [Quaser Framework](http://quasar-framework.org/) - quality UI components for VueJS
* [Electron (JS)](https://electronjs.org/) - for running JS apps (like VueJS) natively on the Raspberry PI
* [pi-gen with ansible](https://github.com/RPi-Distro/pi-gen)

![Vuejs Logo](/uploads/logos/vuejs-logo.png "Vuejs Logo") ![Quasar Logo](/uploads/logos/quasar-logo.png "Quasar Logo") ![Electron Logo](/uploads/logos/electron-logo.png "Electron Logo") {.align-center}

## Image Creation
This project is specific to creating a customised image that can be easily downloaded and burnt to SD, making it easy to create a personal display.

But what technologies will be used to create the image?
* packer

## Application Deployment and Updates
In respect of deploying the `wit-myDash` app, how will this be done on the PI. As a PWA, the application should be able to update itself, but do we want something with a little more control, such as docker. This would at least make it easier to for the localised `wit` registration, licensing and authentication and being able to publish updates to them.

## Device Management
It's all very well having personal displays, but for those that are online, it will be necessary to remotely manage and monitor those devices.

What technology we will use for this?

A cloud service for this yes. AWS IOT: https://aws.amazon.com/iot/#. Pick and choose and grow as necessary, from simple device management, to security to analytics.
