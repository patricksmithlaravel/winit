# Branch `iced-mobile/0.30`

winit 0.30 with the Android fixes that [iced_mobile](https://github.com/patricksmithlaravel/iced_mobile)
needs, until they ship in a winit release.

- **Base:** tag `v0.30.13`. The crate version stays `0.30.13`, so `[patch.crates-io]` matches it.
- **Rule:** this branch only carries fixes destined for upstream winit. Each patch is one commit
  that cites its upstream pull request. When upstream releases a fix, the branch moves to that
  release and drops the patch.

Use it with:

```toml
[patch.crates-io]
winit = { git = "https://github.com/patricksmithlaravel/winit", branch = "iced-mobile/0.30" }
```

## Patches

### Android: exit on Destroy, allow a new loop, report inset changes (winit PR #4739)

- The event loop exits on `MainEvent::Destroy`. Android waits in `onDestroy` for `android_main` to
  return, so before this, an activity that Android relaunched in the same process (font scale,
  locale, night mode or navigation mode changes) hung, and the app hit an ANR.
- Dropping the `EventLoop` allows building a new one, so the next activity's `android_main` does
  not fail with `RecreationAttempt`.
- Content rect changes (`NativeActivity`, `GameActivity`) and inset changes (`GameActivity`) are
  reported as `WindowEvent::Resized` once the window exists, even if the size is unchanged.
- Differences from the PR as posted, to be proposed on it: `ContentRectChanged` is reported too
  (`NativeActivity` has no `InsetsChanged`), input is skipped after `Destroy`, and the
  `EventLoopBuilder::build` docs and the changelog describe the change.
