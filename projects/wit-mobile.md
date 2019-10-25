![Wiki Official White Circle](/uploads/corporate/wiki-official-white-circle.png "Wiki Official White Circle"){.pagelogo}
<!-- TITLE: wit-mobile -->
<!-- SUBTITLE: wit-myDash on the go -->

# Background
The original basis for these projects is to present content to a large display in prime/common areas of a building. Could be a doctors waiting area, a hositpal ward, a large concert venue or all the office canteens for a UK nationwide transport company.

But work patterns are changing; less and less are people going to an office. Home working, remote working and working on road the road are common practices.

So there is a need to access the same information but without specifically provisioning/dedicating a large screen.

# At Home or Remote Office Location
This is the simplest to solve. The home or remote office can be assumed to have an Internet connection. This is simply about making `wit-myDash` production ready but running for a desktop - be it Windows, Linux or MacOS. As a PWA, `wit-myDash` can run on any platform. But as an application alone, it is restricted to development use only to test new content before publishing. Used standalone, there is no need for license or authentication services.

`wit-mobileDesktop` is simply a repackaged PWA that includes the additional licensing and authentication services running in "Service Workers".

> VueJS PWA (progressive web app) plugin - https://www.npmjs.com/package/@vue/cli-plugin-pwa

## Screensaver
What work is required to turn this into a dynamic screensaver?

Note - this would typically require content to be presented landscape.

# On the Road
Tablets and smart phones are pervasive; everyone has at least one of these devices.

[AWS Amplify](https://aws-amplify.github.io/docs/js/start?ref=amplify-js-btn&platform=purejs) takes an existing VueJS application (`wit-myDash`) and provides additional components and services that allow for a good highly managed mobile application, including authentication, push notification and messaging.

The challenge here would be how much of the content as would work on a mobile device compared to a 55" Ultra Definition TV. Also, content that is specific to a given office, e.g. a lunch offer promotion, is relevant to the mobile device.

It would still be up to the content providers using `wit-dash` to provide and control content channels; mobile would simply be an additional channel.

To whether the content is landscape or portrait should not matter, as the mobile device can be rotated. Push notifications could be used to highlight when new content is available.

## Mobile Projections
It is now also easy enough to project (cast) mobile screen output to TVs, allowing "offline" personal displays, e.g. business conferences. Simply create a wifi hotspot on your phone, connect the SmartTV wifi to hotspot and then cast screen.