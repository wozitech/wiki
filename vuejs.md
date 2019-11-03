![Vuejs Logo](/uploads/logos/vuejs-logo.png "Vuejs Logo"){.pagelogo}

<!-- TITLE: VueJS -->
<!-- SUBTITLE: It's not React. It's not Angular. It's the best of both. -->
# Background
Vue.JS is a fast and lightweight Javascript framework for building data-driven single page applications (SPAs). It is Javascript framework first, but provides typescript declarations allowing frontend applications to be built in typescript.

VueJS consider everything to be a component; this is much like React.

[Official Homepage](https://vuejs.org/){:target="_blank"}
[Awesome Vue](https://github.com/vuejs/awesome-vue) - a collection of tips and tricks
[Vue Loader](https://vue-loader.vuejs.org/) - webpack loader for single file components (everything for a component is written in a single file - the template, the script and custom blocks, such as, documentation)

# DevTools
https://github.com/vuejs/vue-devtools#vue-devtools
Like React, there are browser plugins for Chrome and Firefox that make working with Vue.JS easier.
# VuEX (State Management)
This is the equivalent of Redux for React of NgRX for Angular.

Any application where the "data is king", need to be able to event from changes in data. This is where VuEX comes in. More than just binding data to a component, this represents the full body of data across the application with multiple components being able to update themselves when the same data part changes - and ignoring any such data changes if that component is not in visual scope.

# Web Sockets
No modern SPA is static, and certainly not one where "data is king". Data is constantly changes, be it events initiated within the application, as a consequence from requests/responses on the backend or external sources. The first two are easily covered by VueJS/VuEX alone. But external events will be the server to trigger data changes within the VuEX model. This is where web sockets are now defacto. Long gone are polling even in its _long polling_ disguise. Web Sockets are treated like any HTTP request/response; they pass through gateway/routers and can be load balanced (on initial connection only). They allow for bi-directional passing of data; the client does not have to call upon RESTful APIs, but can simply send a message via the web socket connection. Web Sockets support structured (HTML/JSON/XML) and unstructured (binary) data. Utilising the HTTP protocol, web sockets can be secured during transport (HTTPS/TLS) and can be proxied (and reverse proxied) and for secure endpoints can be inspected like any other HTTP request/response.

Server Sent Events are unidirectional, allowing a server to push updates to a client, and only support text payloads.

`https://www.npmjs.com/package/vue-native-websocket`