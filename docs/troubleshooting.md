# Troubleshooting

Below you can find some tips for troubleshooting, which hopefully help you to pinpoint your problem.  

## Google smarthome node logs

The node-red-contrib-google-smarthome node has a feature to enable more extensive logging, via following checkbox in its config nodes:

![image](https://github.com/user-attachments/assets/d4ba390b-c74e-48c8-9fb8-55003ca5966b)

The output of this logging will end up in the standard Node-RED log.  When you see don't see Google Assistant related messages appearing, the funnel is not working.

## Tailscale agent logs

On a Raspberry Pi for example you can have a look at the tailscaled systemd daemon logs via command `journalctl -u tailscaled`.  That log for example shows for what reason requests have been blocked.

## Browser switching from http to https

When I entered a http url in my browser to navigate directly to my Node-RED system (on port 1880), that should be possible because Node-RED is not using https anymore.  Since the https connections are now being handled by the Tailscale agent.  However Chrome continiously switched to https, which resulted in ERR_SSL_PROTOCOL_ERROR because Node-RED is not able to setup https connections anymore.

First make sure that in the Node-RED settings.js file the https is turned off!

Otherwise is could be caused by ***HSTS*** (HTTP Strict Transport Security).  That is a web security mechanism that helps to protect websites against man-in-the-middle attacks, by ensuring that browsers only interact with the site over HTTPS.  By setting up https for Node-RED in the past, Node-RED had sent a HSTS header to my browser, which was stored in my browser and used now to automatically convert all http request to https for my domain.

It was solved after I had cleared in Chrome the HSTS cache for my domain:
1. Navigate to chrome://net-internals/#hsts.
2. Enter your domain <your-virtual-hostname>.ts.net under “Delete domain security policies”.
3. Click the “Delete” button.

## Android loses connections

My Node-RED dashboard was initially not accessible on my Android smartphone, until I opened the Tailscale app (and optionally switched the *"Connected"* off and on again.

This was in my case solved by making sure that the Tailscale VPN connection is always open.  You can achieve that in your Android settings:

![image](https://github.com/user-attachments/assets/9e277779-a721-4d57-ad93-68a932d2e90e)

## White screen
If you enter `https://<your-virtual-hostname>/flow_editor` in the browser address bar, an empty page appears.  Open the developer tools of your browser, and check the Network tabsheet.

![image](https://github.com/user-attachments/assets/1bbd132d-56f9-4c7d-b487-689c4bfd4379)

In this case it is obvious that the first resource "flow_editor" can be loaded, which is the html file from the Node-RED flow editor.  That html file contains relative url's to all other resources (css, js, ...) it requires.  But those resources cannot be found.  This is probably because there is no `/` at the end of the url, so switch it to `https://<your-virtual-hostname>/flow_editor/`.  For more detailed background information see my explanation in [this](https://discourse.nodered.org/t/not-quite-understanding-how-httpadminroot-works/85097/3?u=bartbutenaers) Discourse discussion.

