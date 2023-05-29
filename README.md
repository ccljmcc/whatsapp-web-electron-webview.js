[![npm](https://img.shields.io/npm/v/whatsapp-web.js.svg)](https://www.npmjs.com/package/whatsapp-web.js) [![Depfu](https://badges.depfu.com/badges/4a65a0de96ece65fdf39e294e0c8dcba/overview.svg)](https://depfu.com/github/pedroslopez/whatsapp-web.js?project_id=9765) ![WhatsApp_Web 2.2306.7](https://img.shields.io/badge/WhatsApp_Web-2.2306.7-brightgreen.svg) [![Discord Chat](https://img.shields.io/discord/698610475432411196.svg?logo=discord)](https://discord.gg/H7DqQs4)

# whatsapp-web.js

A WhatsApp API client that connects through the WhatsApp Web browser app

It uses Puppeteer to run a real instance of Whatsapp Web to avoid getting blocked.

**NOTE:** I can't guarantee you will not be blocked by using this method, although it has worked for me. WhatsApp does not allow bots or unofficial clients on their platform, so this shouldn't be considered totally safe.

## Quick Links

- [Guide / Getting Started](https://wwebjs.dev/guide) _(work in progress)_
- [Reference documentation](https://docs.wwebjs.dev/)
- [GitHub](https://github.com/pedroslopez/whatsapp-web.js)
- [npm](https://npmjs.org/package/whatsapp-web.js)

## Installation

The module is now available on npm! `npm i whatsapp-web.js`

Please note that Node v12+ is required.

## Example usage

```js
// Electron Example

import { app } from "electron";
let debugJsonUrl = "";
const installRemoteDebug = async (port = 0, address = "127.0.0.1") => {
  if (!app) {
    throw new Error(
      "The parameter 'app' was not passed in. " +
        "This may indicate that you are running in node rather than electron."
    );
  }

  if (app.isReady()) {
    throw new Error(
      "Must be called at startup before the electron app is ready."
    );
  }

  if (port < 0 || port > 65535) {
    throw new Error(`Invalid port ${port}.`);
  }

  if (app.commandLine.getSwitchValue("remote-debugging-port")) {
    throw new Error(
      "The electron application is already listening on a port. Double `initialize`?"
    );
  }

  const actualPort = port === 0 ? await getPort({ host: address }) : port;

  // 设置默认ua
  app.userAgentFallback = globalConfig.appUserAgent;
  // 开启debug
  app.commandLine.appendSwitch("remote-debugging-port", `${actualPort}`);
  app.commandLine.appendSwitch("remote-debugging-address", address);
  const electronMajor = parseInt(app.getVersion().split(".")[0], 10);
  // NetworkService crashes in electron 6.
  if (electronMajor >= 7) {
    app.commandLine.appendSwitch("enable-features", "NetworkService");
  }

  debugJsonUrl = `http://${address}:${actualPort}`;
};
installRemoteDebug()


// Webview  Example
import { v4 } from "uuid";
const ses = v4()
const webview = document.createElement('webview')
webview.setAttribute('src','about:blank')
webview.setAttribute('partition',`persist:${ses}`)
body.appendChild(webview)
webview.addEventListener('dom-ready',  async () => {
    // 注入唯一id，让puppeteer找到对应的page
    await webview.executeJavaScript(`window.puppeteer = ${ses}`)
    const { Client } = require("whatsapp-web.js");

    const client = new Client({browserWSEndpoint:debugJsonUrl,sessionId:ses});

    client.on("ready", () => {
        console.log("Client is ready!");
    });

    client.on("message", (msg) => {
    if (msg.body == "!ping") {
        msg.reply("pong");
    }
    });

    client.initialize();
    
},
{ once: true }
)

```

Take a look at [example.js](https://github.com/pedroslopez/whatsapp-web.js/blob/master/example.js) for another example with more use cases.

For more information on saving and restoring sessions, check out the available [Authentication Strategies](https://wwebjs.dev/guide/authentication.html).

## Supported features

| Feature                                          | Status                                                                                                               |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| Multi Device                                     | ✅                                                                                                                   |
| Send messages                                    | ✅                                                                                                                   |
| Receive messages                                 | ✅                                                                                                                   |
| Send media (images/audio/documents)              | ✅                                                                                                                   |
| Send media (video)                               | ✅ [(requires google chrome)](https://wwebjs.dev/guide/handling-attachments.html#caveat-for-sending-videos-and-gifs) |
| Send stickers                                    | ✅                                                                                                                   |
| Receive media (images/audio/video/documents)     | ✅                                                                                                                   |
| Send contact cards                               | ✅                                                                                                                   |
| Send location                                    | ✅                                                                                                                   |
| Send buttons                                     | ✅                                                                                                                   |
| Send lists                                       | ✅ (business accounts not supported)                                                                                 |
| Receive location                                 | ✅                                                                                                                   |
| Message replies                                  | ✅                                                                                                                   |
| Join groups by invite                            | ✅                                                                                                                   |
| Get invite for group                             | ✅                                                                                                                   |
| Modify group info (subject, description)         | ✅                                                                                                                   |
| Modify group settings (send messages, edit info) | ✅                                                                                                                   |
| Add group participants                           | ✅                                                                                                                   |
| Kick group participants                          | ✅                                                                                                                   |
| Promote/demote group participants                | ✅                                                                                                                   |
| Mention users                                    | ✅                                                                                                                   |
| Mute/unmute chats                                | ✅                                                                                                                   |
| Block/unblock contacts                           | ✅                                                                                                                   |
| Get contact info                                 | ✅                                                                                                                   |
| Get profile pictures                             | ✅                                                                                                                   |
| Set user status message                          | ✅                                                                                                                   |
| React to messages                                | ✅                                                                                                                   |

Something missing? Make an issue and let us know!

## Contributing

Pull requests are welcome! If you see something you'd like to add, please do. For drastic changes, please open an issue first.

## Supporting the project

You can support the maintainer of this project through the links below

- [Support via GitHub Sponsors](https://github.com/sponsors/pedroslopez)
- [Support via PayPal](https://www.paypal.me/psla/)
- [Sign up for DigitalOcean](https://m.do.co/c/73f906a36ed4) and get $200 in credit when you sign up (Referral)

## Disclaimer

This project is not affiliated, associated, authorized, endorsed by, or in any way officially connected with WhatsApp or any of its subsidiaries or its affiliates. The official WhatsApp website can be found at https://whatsapp.com. "WhatsApp" as well as related names, marks, emblems and images are registered trademarks of their respective owners.

## License

Copyright 2019 Pedro S Lopez

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this project except in compliance with the License.
You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0.

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
