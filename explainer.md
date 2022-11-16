Following [TPAC Devices and Sensors WG discussion](https://www.w3.org/2022/09/15-dap-minutes.html#t20), this explainer explores a  **declarative approach** for allowing web developers to ask the browser to increase the screen brightness.

An element attribute proposal was [suggested](https://www.w3.org/2022/09/15-dap-minutes.html#t20:~:text=could%20be%20an-,element%20attribute,-as%20well). Let's call it `increaseBrightness` for now. Naming is hard. 🚲

## Proposal

```webidl
[Exposed=Window]
partial interface Element {
  [Reflect] attribute boolean increaseBrightness;
}
```

## Example

```html
<div id="my-qr-code" increasebrightness>
    <img src="qrcode.png" width="256" height="256">
</div>
```

## Feature support

You can check for this element attribute support with:

```js
const supported = Element.prototype.hasOwnProperty("increaseBrightness");
```

## Behaviour

When an element with the `increaseBrightness` attribute becomes visible to the user, the browser would take care of boosting the screen region that matches the element bounds OR increasing the overall screen brightness level if the device does not support partial screen brightness boosting.

Only elements with the `increaseBrightness` attribute would trigger this behaviour. This way, web pages such the [Wikipedia QR code article](https://en.wikipedia.org/wiki/QR_code), would not suffer from automatic detection. 

## Security and privacy considerations

Based on the following signals, the browser MAY choose to proactively brighten an area of the screen or the whole screen, provide an affordance for the user to brighten the screen, or ignore the `increaseBrightness` attribute:

- The website is in a secure browsing context.
- The element is displayed fullscreen (or the [display mode](https://www.w3.org/TR/mediaqueries-5/#display-mode) of the active document is "fullscreen").
- The element is visible.
- The element is "scannable" (e.g. QR code, barcode).

Based on the following signals, the browser MAY choose to restore screen brightness automatically:

- The element is not visible anymore.
- The element is not "scannable" anymore (e.g. QR code, barcode).
- The website abuses of the `increaseBrightness` attribute and fires too many requests.

To avoid possible user fingerprinting issues, a website is not able to detect when screen brightness is increased.

Further, exiting full screen, or the "fullscreen" display mode is no longer applying, returns the screen brightness to its default state. 

## Open design issues

The following issues (on top of [the existing ones](https://github.com/w3c/screen-wake-lock/blob/gh-pages/brightness-mode-explainer.md#open-design-issues)) remain open for discussion:

- Unlike other proposals, the screen brightness can be increased without a user gesture. Shall we require one?
- What happens when there are multiple elements with the `increaseBrightness` attribute? Shall we boost both partial screen regions or one only?
