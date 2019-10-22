![2018 Corporare Official Wit Pidash](/uploads/corporate/2018-corporare-official-wit-pidash.png "2018 Corporare Official Wit Pidash")
<!-- TITLE: wit-myDash -->
<!-- SUBTITLE: Web Browser Dashboard -->

# Intro
`wit-myDash` is the flagship WOZiTech project, having seen many redevelopments over the years, the latest using VueJS on Electron.

# Technology
VueJS Progressive Web Application (`wit-myDash`) PWA.

Is used locally to test content presentation; requires an online connection to `wit-serve`.

# JSON
Content is still JSON; content is stored centrally with access via [wit-serve](/projects/wit-serve). 

Content is now supplemented using [JSONata](http://jsonata.org/); it's XSLt for JSON. Making it easier for content to be transformed to align to the various content templates.

# Templates
Gone are handlebar templates; replaced by VueJS templates.

# SASS
Presentation is key when it comes to a dashboard. Styling the presentation, and customising the presentation of that content continues to be provided by SASS. Allowing for more than simply colour changes; the whole look and feel (theme) can be changed. Or you can take one of the default provided themes, and perform subtle custom changes to create your own feel. Default themes are provided for:
* WOZiTech - a fresh modern theme
* Corporate - your more traditional office theme
* Wedding - traditional but fun theme, showcased at the wedding of Warren (Director) and Hans, in September 2018.