# 2.2.8-beta.1

Test release for a build related change :hammer:

### Changes ###
- Use govendor to vendor dependencies.

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.7-beta

Minor improvement :sparkles: 

### Changes ###
- Enabled TCP keep alives to better detect dropped connections
- Move test fixtures to git repo instead of on a random s3 bucket

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.5-beta

Minor improvement :sparkles: 

### Changes ###
- The `--dir` argument now supports expanding environment variables.
  (For example `%APPDATA%` on Windows or `$HOME` on Linux)

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.4-beta

Minor build related release :hammer:

### Changes ###
- Build scripts for dev and prod environment are merged into one script.
Instead of `build/prod.sh|bat` and `build/dev.sh|bat` there is only `build/build.sh|bat`.
- By default the build script does a production build. To make a development build use the `-d` flag.

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.3-beta

Bug fix release :bug: 

### Dead Bugs ###
- Fix downloader not reporting failure once it runs out of retry attempts

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.2-beta

Bug fix release :bug: 

### Changes ###
- Launch apps and os x using the `open` command instead of executing the app's file directly.

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.1-beta

Bug fix release :bug: 

### Changes ###
- Added consistency unit tests for extractor since Windows likes to randomly block file access.

### Dead Bugs ###
- The old executable name was being returned across all the codebase after renaming it (we rename the currently running executable after a self-update)
- New joltron updater was always used even if it is identical to the currently running joltron binary.
- Some files randomly not getting extracted (thanks, Windows) - resulting in update leaving games in a bad state.
- Fix race condition in ring buffer implementation.

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.0-beta

Minor release - a few small features and bug fixes

### New Features ###
- Customize whether the loader UI runs in a borderless window or not (`borderless` field in `loader/data.json`)

### More Changes ###
- Retry recovering based on what we're updating to instead of what we're updating from. This allows us to push a new version and have games in a bad state to update correctly :smile: 
- Relaunch with a new updater if provided after a clean installation, not only after updating the existing game.
- Closing the loader UI during recovery 

### Dead Bugs ###
- Relaunching after update not always using the new updater in some cases.
- Fixed panic when closing the loader UI
- Closing the loader UI after recovery prevented future recovery attempts to the current recovery target

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.2.1-beta

This is a patch release contains fixes to the build flow.
No functionality changes in the provided binaries since 2.1.0-beta

# 2.1.0-beta

Minor release adding some cool features and a bunch of fixes.

### New Features ###
- **Graphical loader when a game is being installed/updated** 
Simple OpenGL 3.3 loader
  - Conditionally compiled when using the "loader" build tag.
  - Customizable background image, loading bar image and windows title.
  - Can be disabled through the `--no-loader` command line switch.
  - Loader variants are attached to the release binaries as `joltron_loader_{platform}`
- **Opt-In Crash Recovery**
Games can opt in to additional services through a `declaredImplementations` field defined in the `.manifest` file under the `info` key. The field is a simple object that looks like `service name => bool`.  
**Currently 2 services are supported**:
  - *"presence"*: Tells Joltron the game is "joltron-aware". The game is expected to connect back to Joltron as soon as it starts to interact with it. Note: The game can find which port to connect to by locating the `.manifest` file by crawling up the directory tree.
  - *"badUpdateRecovery"*: Automatically attempt to detect when a game is in a bad state and needs recovery. 
    - Depends on `presence` implementation to be enabled - a game is considered to be in a bad state if it declares to implement presence but fails to connect to Joltron during its run time.
    - Recovery is done by a clean reinstallation of the current version, so local modifications in the game's directory **will be lost**.
    - Recovery is limited to 1 attempt per version to avoid infinite recovery loops. These loops happen if the game keeps corrupting itself or declares to implement presence when it in fact does not.
    - Broadcasts a `gameRecovering` update message when recovery starts
- Added `--debug` flag on Windows that pops out a console window with logs when run interactively. If run through cmd or a similar console this will enable console logging in the existing window.

### More Changes ###
- Only the 64 bit variants of Joltron are provided for mac and linux by default.
- Migration flow now runs with a more restrictive file system sandbox.
- Patcher's broadcaster now sends download and extract progress events as well as state changes.
- Make self updater flow always try to clean up the old updater file.  
On Windows the file may have been locked after the rename and the removal wouldn't go through.
- Much better extractor handling for symlinks, permissions and many other edge cases, covered by unit tests
- Refactor fixtures handling in tests files

### Dead Bugs ###
- Extractor loop when archive lists a nested folder before it's parent folder
- Panic on mac when launching games (oof, that was a syntax error, so bad)
- Migration conflicts when there are multiple migrations active in the same directory at the same time. 
- Console logs were completely suppressed on Windows
- Cross compilation failing due to old syso files from previous compilations for different targets

### Known Issues ###
- Extractor checks for files attempting to break out of the extraction directory could be improved.
- On Windows sometimes the new updater isn't being used when relaunching. Probably E_BUSY errors after renaming the temp updater file
- Uninstallation fails if no `.manifest` file is found instead of tolerating and continuing

# 2.0.3-beta

Minor bug fix release.

### Changes ###
- Hide the cmd window on Windows

# 2.0.2-beta

Bug fix release.

### Changes ###
- Fix extracting archives with symlinks failing in some cases
- Fix extracting not overwriting existing files or directories correctly
- Fix patcher removing files it shouldn't due to a wrong symlink overwriting behavior
- New extractor implementation, with unit tests :ok_hand: 

# 2.0.1-beta+1

Debug release, for you know, debugging and stuff.

This version is identical to v2.0.1-beta.

# 2.0.1-beta

Minor optimizations, release.

### Changes ###
- Doesn't bother downloading the updater if we already have it.
- Doesn't wait before quitting when relaunching the new updater version.
This might fix some issues with the old launcher executable still being "busy" by the time the new updater attempts to clean it up.

# 2.0.0-beta

Major release supporting self updating the Joltron binary itself, more robust updating and a bunch of fixes.

### New Features ###
- **Support automatic self updating on launch.**  
   The update flow is as follows:

  - Fetch metadata from the URL. This is how Joltron figures out what the latest version is for the current platform and where to download it from.
  - download the new version and place it next to the currently running executable.
  - launch the new version with the same arguments it was given, and quit immediately after.
  - The new version disposes the old version's binary
  - The new version renames itself to be the same name as the old binary.

  The URL to hit for the update metadata is baked in at compile time by providing it as an argument to the `build/dev` or `build/prod` scripts. See [manifest template](https://github.com/gamejolt/Joltron/blob/master/config/manifest-template.json) for the metadata structure.

  Automatic self updating can be disabled by passing the `--no-self-update` flag.

- **Support self updating when the managed application is updated.**  
   When the application interacts with Joltron, it must ensure compatibility by specifying an exact version of Joltron to update to, as part of updating itself. This is supported by returning 3 additional fields from the `platform url`. The fields are:

  - `updaterUrl` - A direct url to download the new version from
  - `updaterChecksum` - The checksum of the new version. _It may be omitted to skip validity checks._
  - `updaterRemoteSize` - The size of the new version in bytes. _It may be omitted to skip validity checks._

  The update flow is similar to the flow detailed above, only skipping the first step of fetching the metadata from the update URL, as these are given by the platform url.

- Broadcast a `gameClosed` update message when the application closes normally (with an exit code 0)
- Make Joltron attempt to run the managed application by default if it is given no arguments. It used to display help. This allows using Joltron as the main executable for the managed application.
- Add incoming `open` control command to make Joltron broadcast an `openRequest` update message. This is used to allow the underlying application to react when it is attempted to launch a second time. A common implementation would be to bring the application back to focus.
- Add `executable`, `args` and `cwd` to the manifest's `runningInfo` struct. This allows the managed application to query how the currently running Joltron instance was started.
- Support cross compiling Windows binary with an embedded icon and file information using [goversioninfo](https://github.com/josephspurrier/goversioninfo).

### Dead Bugs ###
- Fix not cleaning up running info after the application has ended.
- Fix detecting when a game is already running.
- Fix Joltron window showing on Windows in the taskbar.
- Better filesystem case sensitivity checks.
- Fix a bunch of race conditions with handling the manifest.
- Fix a race condition when closing the application after it finished extracting the next update.
- Fix temp download files sticking around after finishing an update in some cases.

# 1.2.0-beta

Minor bug fix release.

### Changes ###
* Retry downloads in some failing scenarios
* Stop refetching download URL after every attempt. We only want to do that after a pause/resume.

# 1.1.2-beta

Minor bug fix release.

### Changes ###
* Updated Go version to 1.9.3
* Fixed download connection closing prematurely on x86 binaries
* Increased idle timeout to make it more stable on shaky networks.

# 1.1.1-beta

Minor bug fix release.

### Changes ###
* Updated Go version to 1.8.1.
Fixes handling long paths on Windows.

# 1.1.0-beta

### Features ###
* Migration support

### Going Forward ###
We will keep Joltron versioned 😄 
Binaries will be published with every release

**There are 3 version types:**
1. **Joltron version** - main repo version, follows Semver
2. **Net version** - Joltron instances have a network interface for 3rd party managers. The network api follows Semver. Breaking changes to the network version *MUST* increment the major Joltron version as well, even if the code api itself did not change.
3. **Manifest versions** - used in metadata files to keep track of the state of files on disk. This is a simple integer version, incremented whenever the way Joltron stores files on disk changes.

# 1.0.0-beta

Initial release