# iPhone Duo Profile evidence for duo-adaptive-layouts

## Builds

- **before**: unchanged baseline `f9dd297` (upstream main).
- **after**: final head `7f8ad26` (`263e913` plus one comment-only Blade change).

Both are `composer setup:free` debug builds (no Secure Storage or Skia plugin). They ran on the iPhone Duo simulator (iOS 27.2 iPhone19-4, Xcode 27.2 Device Hub) on an Amp Mac satellite, signed in as the disposable `duo-tester` account. That account lives on an isolated local test backend bound to the Mac's 127.0.0.1, so all data shown is seeded test data.

## Files

`profile-<pose>-ios-<appearance>-<stage>.png`, captured in light and dark mode for each pose:

| Pose | What it shows | Width |
| --- | --- | --- |
| `closed` | outer display | 466pt |
| `open` | inner display, landscape | 951pt |
| `tabs` | inner display, Open, scrolled to the Commits/Replies tabs | 951pt |
| `partial` | inner display, landscape Partially Open | 951pt |
| `portrait` | inner display, Partially Open rotated | 669pt |
| `closed-after` | outer display again, after the inner poses | 466pt |

- The new layout applies only at 840pt and wider: `open`, `tabs` and `partial`. `closed`, `portrait` and `closed-after` keep their geometry; the background token changes still apply there.
- `matrix-before.txt` and `matrix-after.txt` record the UI orientation of each capture.
- `profile-tab-switch-ios-light-<stage>.mov` records a tap from Replies to Commits and back to Replies on the inner display (Open). `profile-tab-switch-frames-ios-light-<stage>.png` is a frame strip from that recording.

The repository's `screenshots:take` captures only the simulator's default display and cannot set the fold pose. These files were therefore captured with `xcrun simctl io <udid> screenshot --display=1|3` and Device Hub's pose controls, then named for `screenshots:publish`.

## Findings

- **On the baseline, the Profile header shifts left and is cut off on the Replies page** of the inner display. During the switch it moves with the page. This happens on the unchanged app.
- **On the final head the header stays whole and still**, and only the list slides. Here the Profile pager itself takes `Display::ReadableColumn` (672pt, centred) and is no longer under the trailing rail.
- **Pager bleed.** Content from the neighbouring page (skeleton placeholders, or Replies text) shows under the trailing rail or status column.
  - This is a confirmed visual defect that predates this branch. It is visible on the closed display in both builds.
  - The suspected cause is in NativePHP's pager renderer, which draws into the trailing safe area. That is a hypothesis, not a proven cause, and the place to fix it is not established. This branch does not fix it.

## Not covered

- No Android: orbs have no KVM and there was no Android device.
- A slow finger-driven swipe between pages. Device Hub mouse drags scroll vertically.
- A tab switch with the header collapsed (pinned strip).
- Whether the new-commits pill is visible and tappable.
- Whether content clears the trailing rail.
- Taps on controls inside the fold.
- Release, TestFlight and Keychain builds.

## Other evidence

Earlier evidence captured from the pre-fix commit `2ae4a69` is **not** included here. It covered all screens, the keyboard, and a Profile recording that showed the defect since fixed. It is kept in the working thread's report, labelled with that build.
