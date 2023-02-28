# LPIC-1.106: User Interfaces and Desktops

## Lesson 106.3: Accessibility

The Linux desktop environment has many settings and tools to adapt the user interface for people with disabilities. The ordinary human interface devices — screen, keyboard and mouse/touchpad — can be individually reconfigured to overcome visual impairments or reduced mobility.

It is possible, for example, to adjust the desktop color scheme to better serve color-blind people. Also, people suffering from repetitive strain injury may take advantage of alternative typing and pointing methods.

Some of these accessibility features are provided by the desktop environment itself, like Gnome or KDE, and others are provided by additional programs. In the latter case, it is important to pick the tool that best integrates the desktop environment, so that the aid is of better quality.

### Accessibility Settings
All major Linux distributions provide about the same accessibility features, which can be customized with a configuration module found in the settings manager that come with the desktop environment. The accessibility settings module is called Universal Access in the Gnome desktop, whereas in KDE is under System Settings, Personalization, Accessibility. Other desktop environments, like Xfce, also call it Accessibility in their graphical settings manager. However, they offer a reduced set of functionalities compared to Gnome and KDE.

Gnome, for instance, can be configured to permanently show the Universal Access menu in the top-right corner of the screen, where the accessibility options can be quickly switched on. It can be used, for example, to replace the sound alert by a visual one, so users with hearing impairment will be able to perceive system alerts more easily. Although KDE does not have a similar quick-access menu, the visual alert feature is also available, but is called visual bell instead.

### Keyboard and Mouse Assist
The default keyboard and mouse behaviour can be modified to get around specific mobility difficulties. Key combinations, key auto repeat rate and unintended key presses can be significant obstacles for users with reduced hand mobility. These typing shortcomings are addressed by three keyboard related accessibility features: Sticky keys, Bounce keys and Slow keys.

The Sticky keys feature, found in the Typing Assist section of Gnome’s Universal Access configuration, allows the user to type keyboard shortcuts one key at a time. When enabled, key combinations like `Ctrl+C` do not need to be held down at the same time. The user may first press the `Ctrl` key, release it and then press the `C` key. In KDE, this option is in the Modifier Keys tab of the Accessibility settings window. KDE also offers the Locking Keys option: if enabled, the `Alt`, `Ctrl` and `Shift` keys will stay “down” if the user presses them twice, similar to the Caps lock key behaviour. Like the Caps Lock feature, the user will need to press the corresponding key again to release it.

The Bounce keys feature tries to inhibit unintended key presses by placing a delay between them, that is, a new key press will be accepted only after a specified length of time has passed since the last key press. Users with hand tremors may find the Bounce keys feature helpful to avoid pressing a key multiple times when they only intend to press it once. In Gnome, this feature concerns only same key repetitions, while in KDE it concerns any other key press as well and is found in the Keyboard Filters tab.

The Slow keys feature also helps to avoid accidental key strokes. When enabled, Slow keys will require the user to hold down the key for a specified length of time before it is accepted. Depending on the user’s needs, it may also be helpful to adjust the automatic repetition while holding a key down, available in the keyboard settings.

The Sticky keys and Slow keys accessibility features can be turned on and off with Activation Gestures performed in the keyboard. In KDE, the Use gestures for activating sticky keys and slow keys option should be checked to enable Activation Gestures, while in Gnome this feature is called Enable by Keyboard in the Typing Assist configuration window. Once the Activation Gestures are enabled, the Sticky keys feature will be activated after pressing the Shift key five consecutive times. To activate the Slow keys feature, the Shift key must be held down for eight consecutive seconds.

Users who find it more comfortable to use keyboard over the mouse or touchpad can resort to keyboard shortcuts to get around in the desktop environment. Furthermore, a feature called Mouse Keys allows the user to control the mouse pointer itself with the numerical keypad, which is present in full-sized desktop keyboards and in larger laptops.

The numerical keypad is arranged into a square grid, so each number corresponds to a direction: `2` moves the cursor downwards, `4` moves the cursor to the left, `7` moves do cursor north-west, etc. By default, number `5` corresponds to the left mouse click.

Whilst in Gnome there is only a switch to enable the Mouse Keys option at the Universal Access settings window, in KDE the Mouse Keys settings are located at System Settings, Mouse, Keyboard Navigation, and options like speed and acceleration can be customized.
```
Tip
The Slow keys, Stick keys, Bounce keys and Mouse keys are accessibility features provided by AccessX, a resource within the X keyboard extension of the X Window System. AccessX settings can also be modified from the command line, with the xkbset command.
```
The mouse or touchpad can be used to generate keyboard input when keyboard usage is too uncomfortable or not possible. If the Screen Keyboard switch in Gnome’s Universal Access settings is enabled, then an on-screen keyboard will appear every time the cursor is in a text field and new text is entered by clicking the keys with the mouse or touchscreen, much like the virtual keyboard of smartphones.

KDE and other desktop environments may not provide the screen keyboard by default, but the onboard package can be manually installed to provide a simple on-screen keyboard that can be used in any desktop environment. After installation, it will be available as a regular application in the application launcher.

The pointer behaviour can also be modified if clicking and dragging the mouse causes pain or is impracticable for any other reason. If the user is not able to click the mouse button quick enough to trigger a double-click event, for example, the time interval to press the mouse button a second time to double-click can be increased in the Mouse Preferences in the system configuration window.

If the user is not able to press one of the mouse buttons or none of the mouse buttons, then mouse clicks can be simulated using different techniques. In the Click Assist section of the Gnome Universal Access, the Simulate a right mouse click option will generate a right-click if the user presses and holds the left mouse button. With the Simulate clicking by hovering option enabled, a click event will be triggered when the user holds the mouse still. In KDE, the KMouseTool application will provide these same features to assist mouse actions.

### Visual Impairments
Users with reduced eyesight may still be able to use the monitor screen to interact with the computer. Depending on the user’s needs, many visual adjustments can be made to improve the otherwise hard to see details of the standard graphical desktop.

Gnome’s Seeing section of Universal Access settings provide options that can help people with reduced eyesight:

High Contrast
- will make windows and buttons easier to see by drawing them in sharper colors.

Large Text
- will enlarge the standard screen font size.

Cursor Size
- allows to choose a bigger mouse cursor, making it easier to locate on the screen.

Some of these adjustments are not strictly related to accessibility features, thus can be found in the appearance section of the configuration utility provided by other desktop environments. A user having difficulties discerning between visual elements can choose a high-contrast theme to make it easier to identify buttons, overlapping windows, etc.

If appearance adjustments alone are not enough to improve screen readability, then a screen magnifier program can be used to zoom in on parts of the screen. This feature is called Zoom in Gnome’s Universal Access settings, where options like magnification ratio, position of the magnifier and color adjustments can be customized.

In KDE, the KMagnifier program provides the same features, but it is available as an ordinary application through the application launcher. Other desktop environments may provide their own screen magnifiers. Xfce, for example, will zoom in and out the screen by rotating the mouse scrolling wheel while the Alt key is down.

Finally, users for whom the graphical interface is not an option can use a screen reader to interact with the computer. Regardless of the chosen desktop environment, the most popular screen reader for Linux systems is Orca, which is usually installed by default in most distributions. Orca generates a synthesized voice to report screen events and to read the text under the mouse cursor. Orca also works with refreshable braille displays, special devices that display braille characters by raising small pins that can be felt with the fingertips. Not all desktop applications are fully adapted for screen readers and not all users will find it easy to use them, so it is important to provide as many screen reading strategies as possible for users to choose from.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)