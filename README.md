# Picture-in-picture with non-video elements in HTML 💻▶

We've been very used to Video Elements running in Picture-In-Picture forever.

But did you know? You can run Picture-In-Picture mode for non-video elements too, essentially creating a mini version of your tab for users to use while they're on other tabs.

This can be useful for:

- Video conferences where you need the meeting to be visible and going, while you are typing something in another tab (Google Meet already supports this, it is actually where I realized this is a capability in modern browsers).
- Pomodoro Clocks / Alarms that need to be active on the side of the screen.
- Anything else you might think of (Just don't spam the user please, we already have too many sites spamming us)

![Google Meet PIP Mode](/assets/google-meet-pip.png "Google Meet PIP Mode")

![Tomodoro PIP Mode](/assets/tomodoro-pip.png "Tomodoro PIP Mode")

### How it works

High-level overview:

- Modern browsers support the `documentPictureInPicture.window` property that has stores a reference to the Picture-in-picture window running for the current tab + Methods to request a new PIP window and close an existing PIP window.

```js
if ("documentPictureInPicture" in window) {
	// The Document Picture-in-Picture API is supported.
}
```

- Request a new PIP window for your tab with:

```js
const pipWindow = await documentPictureInPicture.requestWindow({
	width: number,
	height: number,
});
```

- Add content to the PIP window:

```js
const pipWindowContents = document.createElement("div");

pipWindowContents.innerHTML = "This will show in the Picture In Picture Window!";

pipWindow.document.body.append(pipWindowContents);
```

- Listen for the opening and closing of the PIP Window:

```js
documentPictureInPicture.addEventListener("enter", (event) => {
    ...
});

pipWindow.addEventListener("pagehide", () => { ... });
```

- Close the PIP Window / Go to main tab using:

```js
if (documentPictureInPicture.window) {
	documentPictureInPicture.window.opener.focus(); // Go back to main tab. I.E: The opener
	documentPictureInPicture.window.close(); // Close the PIP window
}
```

_Some things to note:_

- The PIP Window is a separate window with its own DOM, no additional scripts and styles, so you can start the window from a fresh slate, or just copy over stylesheets from your main window to the next one.
- You can even have different versions of your site for main tab and PIP Windows.
- PIP Windows are children of the tab that requested them, you can hence communicate between the windows as well as access DOM Elements of the PIP Window from the main tab too.

### A Demo

<video width="900" height="467.43" controls src="/assets/demo-video.mp4">
</video>

### Source Code for the demo

Check [index.html](/index.html). It also contains the code for toggling the PIP window on visibility change of the

```js
const closePictureInPicture = () => {
	if (documentPictureInPicture.window) documentPictureInPicture.window.close();
};

const openPictureInPicture = async () => {
	closePictureInPicture();

	const pipWindow = await documentPictureInPicture.requestWindow({
		width: 400,
		height: 300,
	});

	const pipWindowContents = document.createElement("div");

	pipWindowContents.innerHTML =
		"This will show in the Picture In Picture Window! Yay!";

	pipWindow.document.body.append(pipWindowContents);
};

const pipButton = document.querySelector("#requestForPIP");

document.addEventListener("visibilitychange", (event) => {
	if (document.hidden) openPictureInPicture();
	else closePictureInPicture();
});

pipButton.addEventListener("click", openPictureInPicture);
```

### Reference

[An Amazing Reference to Picture In Picture capability handling for not just video elements](https://developer.chrome.com/docs/web-platform/document-picture-in-picture)

[The official Picture-in-picture API Documentation, but mostly geared towards]()
