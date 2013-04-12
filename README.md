Patches to provide native sign in to the browser.

## Demo

https://vimeo.com/62828580

## Downloading

You can download try builds of these for MacOSX, Linux, and Windows here:

http://ftp.mozilla.org/pub/mozilla.org/firefox/try-builds/jparsons@mozilla.com-c4ea7c9d58ae/

Those builds are packaged like Firefox nightlies, so you just need to download
and install them.  No compiling necessary.

## Building

If you want to compile and play, the patches apply to mozilla-central in this order:

```shell
> hg qapplied
sign-in-to-website
sign-in-to-browser
```

Then `./mach build` at the root of your `mozilla-central` repo.

Latest tbpl: [Thu Apr 11 21:43:53 2013 PDT](https://tbpl.mozilla.org/?tree=Try&rev=245ba06fe850)

## The Sign In tab

Visit the chrome url `chrome://browser/content/browser-id-signIn.html`

## Using the API from the Scratchpad

1. In `about:config`, set `devtools.chrome.enabled` to `true`
2. Open **Tools** → **Web Developer** → **Scratchpad**
3. Choose **Environment** → **Browser**
4. Use code like that below:

```javascript
let Cu = Components.utils;
Cu.import('resource://gre/modules/XPCOMUtils.jsm');
Cu.import('resource://gre/modules/identity/MinimalIdentity.jsm');

function createAccount(email, password, origin) {
  // Create an account, sign in, and return the assertion
  IdentityService.createAccount({
    email: email,
    password: password,
    origin: origin,
    allowUnverified: true},
    function(assertion) {
      alert("create account success: " + assertion);
    }
  );
}

function signIn(email, password, origin) {
  // Sign in and return an assertion
  IdentityService.signIn({
    email: email,
    password: password,
    origin: origin,
    allowUnverified: true},
    function (assertion) {
      alert("signed in: " + assertion);
    }
  );
}
function signOut() {
  // Sign the current user out
  IdentityService.signOut(
    function(result) {
      alert("signed out: " + result);
    }
  );
}

function getIdentity(origin) {
  // If the user is signed in, return an assertion for the origin.
  // Otherwise, return null.
  IdentityService.getIdentity({
    origin: origin},
    function (assertion) {
      alert("got identity: " + assertion);
    }
  );
}

function accountExists(email) {
  // Test whether the current account exists
  IdentityService.accountExists({
    email: email},
    function (info) {
      alert("account exists? " + info.state);
    }
  );
}

// Probably want to make up your own usernames and passwords
var EMAIL = 'gnorts@mr.ali.en';
var PASSWORD = 'i like pie!';
var ORIGIN = 'https://firefox.com';

//createAccount(EMAIL, PASSWORD, ORIGIN);
//signIn(EMAIL, PASSWORD, ORIGIN);
//signOut();
//accountExists(EMAIL);
getIdentity(ORIGIN);
```
