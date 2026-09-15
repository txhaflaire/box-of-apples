<p align="center">
  <img src="README-assets/app-icon.png" width="140" alt="Box of Apples app icon">
</p>

<h1 align="center">Box of Apples</h1>

<p align="center">
  Mac VM labs, neatly boxed.
</p>

<p align="center">
  Create, run, inspect, and automate macOS virtual machines on Apple Silicon.
</p>

<p align="center">
  <a href="https://github.com/txhaflaire/box-of-apples/releases/latest"><img alt="Download latest stable package" src="https://img.shields.io/badge/download-latest%20pkg-F4AF60?style=for-the-badge"></a>
  <a href="https://boa.decompiler.dev/docs.html"><img alt="Read the documentation" src="https://img.shields.io/badge/docs-application%20%26%20CLI-252830?style=for-the-badge"></a>
  <a href="#requirements"><img alt="Requires macOS 26 or later on Apple Silicon" src="https://img.shields.io/badge/macOS-26%2B%20%7C%20Apple%20Silicon-252830?style=for-the-badge"></a>
</p>

Box of Apples is a native macOS workspace built on Apple's Virtualization framework. Keep restore images, VM bundles, reusable templates, security captures, and automation organized in one place. Use the desktop app, the bundled `boa` CLI, or the built-in web dashboard to work with the same library.

[Website](https://boa.decompiler.dev/) | [Application Guide](https://boa.decompiler.dev/docs.html#gui-overview) | [CLI Reference](https://boa.decompiler.dev/docs.html#cli-reference) | [Web API](https://boa.decompiler.dev/docs.html#web-api) | [Releases](https://github.com/txhaflaire/box-of-apples/releases)

> Feature availability depends on your installed build and host macOS version. Check the release notes for your package; newer features may appear in a pre-release before the stable release.

## Highlights

| Area | What you can do |
| --- | --- |
| Image Library | Discover Apple's latest supported restore image, browse an alternative IPSW catalog, import local files, and download from direct URLs. Inspect version, build, source, and host compatibility, with download progress and cancellation. |
| Virtual Machines | Configure CPU, memory, disk, networking, audio, and startup serial randomization. Install macOS, start with a console or headless, boot into Recovery, stop, suspend, resume, and inspect lifecycle logs. |
| Snapshots | Save and restore disk states for eligible ASIF VMs on macOS 27+. Organize snapshots with names, notes, tags, favorites, and search. |
| Templates and disposables | Seal clean baselines and launch temporary test environments. Eligible macOS 27+ ASIF templates use linked writable overlays; other templates use the copy-based workflow. |
| Endpoint Security Monitor | Inspect guest security events, executions, process trees, file activity, and event JSON. Configure subscriptions and mute rules, then export captures as JSON or JSONL. |
| Console and clipboard | Use pop-out consoles, keyboard capture, screenshots, and screen recordings. Enable directional text clipboard sync and reuse recent entries from per-VM history. |
| Shared folders | Share local directories or supported WebDAV, SMB, and APFS sources. Mount the Guest Agent and ES Monitor installers inside the guest and update supported running sessions without restarting. |
| Guest Agent | Query guest information, stage files, execute guest processes, synchronize text, and collect artifacts through the bundled guest integration. |
| Runbooks | Build reusable guest workflows with process execution, artifact staging and collection, recording controls, delays, and screen-text monitoring. Use local or Git-backed artifact sources and import/export runbook JSON. |
| Scheduler | Schedule VM boot, shutdown, suspend, resume, and runbook execution by local time and weekday. Inspect action results in the app or dashboard. |
| Web dashboard and API | Manage images, VMs, snapshots, runbooks, and schedules from an authenticated browser interface or JSON API, with a live VM console and optional self-signed HTTPS. |
| macOS 27 integration | Configure First Boot Setup for supported guests, select connected USB peripherals for passthrough, and identify VM processes with app-specific labels in Activity Monitor. Unsupported controls remain visible with availability guidance. |
| CLI and portability | Manage settings, images, VMs, snapshots, templates, and schedules with `boa`. Use supported JSON output for scripts and move eligible stopped VMs between hosts with `.boa-vm` archives. |

## Requirements

- An **Apple Silicon Mac running macOS 26 or later**.
- Enough free space for restore images, VM disks, snapshot layers, captures, and exports. A single macOS VM can require tens of gigabytes.
- Administrator access to install the package. Guest software installation and explicitly elevated guest actions require the appropriate permissions inside the guest.

The **host** is your physical Mac. The **guest** is macOS running inside a VM.

| Feature | Additional requirements |
| --- | --- |
| Core VM, template, console, and automation workflows | macOS 26+ host; guest integrations require their guest-side installation. |
| ASIF disk snapshots | macOS 27+ host; a stopped, unsealed, persistent VM with an ASIF disk. |
| Linked ASIF disposables | macOS 27+ host; an eligible newly created ASIF template with no snapshot stack. See [Snapshots and Templates](#snapshots-and-templates). |
| First Boot Setup | macOS 27+ on both host and guest; configured before the new guest's first normal boot. |
| USB passthrough | macOS 27+ host, a matching supported USB device, and an app-managed VM session. |
| Clipboard, guest actions, and agent-based runbooks | Boa Guest Agent installed inside the guest, its mode enabled for the VM, and a logged-in guest desktop user. |
| Endpoint Security capture | Guest Agent plus the guest ES Monitor, with its system extension approved and Full Disk Access granted inside the guest. |

Snapshots and linked disposables do not independently require a macOS 27 guest. Their disk stacks do require a macOS 27+ host to run.

## Install

1. Download the `.pkg` from the [latest stable release](https://github.com/txhaflaire/box-of-apples/releases/latest).
2. Run the installer, then open **Box of Apples** from `/Applications`.

The distributed package is codesigned and notarized for macOS. For early access to upcoming changes, check [all releases](https://github.com/txhaflaire/box-of-apples/releases) for packages marked **Pre-release** and read their notes before upgrading important lab environments.

## First Run

1. In **Image Library**, choose **Download Latest** or **Import IPSW**. Wait for a downloaded image supported by your host.
2. Open **Virtual Machines > Create > Persistent VM from Image**. Choose the image, name, disk size, and configuration, then select **Create VM**. NAT is the default network mode.
3. Select the VM and choose **VM Actions > Install macOS**. After installation finishes, choose **Start VM** and complete the guest's setup.
4. For clipboard sync and guest automation, follow the [Guest Agent](#guest-agent) setup below.
5. Configure your baseline and shut it down. Take a snapshot of an eligible persistent VM, or seal it as a template for disposable test runs.

New VMs default to ASIF on macOS 27+. You can explicitly choose raw instead, but raw disks do not support snapshots or linked ASIF disposables. Existing disks are not automatically converted.

For supported macOS 27 guests, **First Boot Setup** can configure account creation, automatic login, and Remote Login before the first normal boot. It does not install the Guest Agent or approve the ES Monitor. See the [macOS 27 guide](https://boa.decompiler.dev/docs.html#gui-macos-27).

## Guest Agent

The Boa Guest Agent runs inside the guest as a LaunchAgent for a logged-in desktop user. It enables guest information, process execution, file staging and collection, and text clipboard integration.

1. In the VM configuration, select **Guest Agent > Boa Guest Agent**, enable **Mount Guest Agent Installer**, and choose **Save Configuration**. Restart the VM if you changed its agent mode during an existing session.
2. Log in inside the guest and open `/Volumes/My Shared Files/Boa Guest Agent Installer` in Finder.
3. Run the bundled `.pkg` inside the guest. The installer starts the agent for the active desktop user; otherwise, it starts at the next desktop login.
4. On the host, choose **VM Actions > Guest Agent Actions > Query Guest Info** to verify the connection.

If the installer mount is unavailable, use **Guest Agent > Save Package...** in the Virtual Machines toolbar and share the package with the guest manually. Selecting the agent mode or mounting its installer does not install the software.

Once connected, choose **Actions > Clipboard Sync** in the VM console for **Host to VM** or **Bidirectional** text sync. Sync is off by default. **Show Clipboard History** provides recent entries with **Copy to Host**, **Send to VM**, and **Clear History** actions. History is kept in app memory, not as a permanent archive; sync does not transfer files or images.

[Guest Agent guide](https://boa.decompiler.dev/docs.html#gui-guest-agent) | [Clipboard guide](https://boa.decompiler.dev/docs.html#gui-clipboard)

## Endpoint Security Monitor

Inspect security activity inside a VM from the host, including process executions, relationships, file events, and signing information.

1. Complete the Guest Agent setup and keep a guest desktop user logged in.
2. Enable **Mount ES Monitor Installer** in the VM configuration and save. Inside the guest, install the package from `/Volumes/My Shared Files/Boa ES Monitor Installer`.
3. Open **Box of Apples ES Monitor** inside the guest. Approve its system extension and grant Full Disk Access to **BoxOfApplesEndpointSecurityExtension** in the guest's System Settings. Wait for both permissions to report **Granted** and the monitor to report **Ready**.
4. On the host, right-click the VM and choose **Endpoint Security**. Configure **Subscriptions**, select **Save and Deploy**, and choose **Start**.
5. Inspect events, executions, and process trees. Export the capture as **JSON** or **JSONL** before shutting down the VM.

The default subscription is `exec`. Additional subscriptions and shared mute rules control what is collected. This is an event-inspection tool, not a policy editor for blocking guest processes. Capture storage is session-scoped and is removed when the VM runtime is torn down.

[Endpoint Security setup and capture guide](https://boa.decompiler.dev/docs.html#gui-endpoint-security)

## Snapshots and Templates

### Disk Snapshots

On macOS 27+, stop an unsealed persistent ASIF VM and open **Snapshots** to create a named disk state. Add notes and tags, mark favorites, or search existing snapshots. The CLI and API also support snapshot management.

- Creating, restoring, or deleting a snapshot requires the VM to be stopped. Suspending it is not sufficient.
- Snapshots capture disk state, not live memory, VM configuration, or files in host shared folders.
- Restoring discards unsnapshotted disk changes. Create a snapshot first when you need to preserve the current state.
- A snapshot cannot be deleted while the active disk layer or another snapshot depends on it.
- Snapshot layers live in the VM bundle. Keep the bundle intact and maintain separate backups; snapshots do not protect against losing the bundle.

### Templates and Linked Disposables

Configure and shut down a persistent VM, seal it as a template, then create disposable VMs from that baseline. Sealed and disposable starts use temporary runtime clones, so collect artifacts before stopping the session.

Linked disposables are selected automatically when the template is an installed or stopped, sealed ASIF VM created on macOS 27+ with the linked-clone capability marker and no snapshot stack. They use a writable overlay over the sealed base instead of copying the full disk. Other templates use the existing copy-based path, with APFS copy-on-write support for temporary runtime copies where available.

Linked disposables currently cannot be exported or converted to persistent VMs. Keep their base template at its original location; do not delete or modify its disk. Unsealing is blocked until all linked disposable dependents are deleted from the library, not merely stopped.

Stopped full-copy disposable sources can still be converted to persistent VMs. That conversion does not recover changes discarded from a temporary runtime session.

[Snapshot guide](https://boa.decompiler.dev/docs.html#gui-snapshots) | [Template guide](https://boa.decompiler.dev/docs.html#gui-templates) | [Linked-disposable requirements](https://boa.decompiler.dev/docs.html#linked-disposables)

## Shared Folders

Configure shares in the VM inspector and choose **Save Configuration**. Inside the guest, open `/Volumes/My Shared Files`; each share appears under its configured guest name.

Local directories can be read-only or writable. Supported WebDAV, SMB, and APFS sources are mounted through the host and exposed read-only to the guest. A web source must support WebDAV; an ordinary website or file-download URL is not a folder share.

Shared-folder changes, including the Guest Agent and ES Monitor installer toggles, apply live to app-managed sessions when their shared-folder device is available. This includes headless sessions started by the app. Separate CLI runtimes or older sessions without that device require a stop and start to pick up changes.

[Shared-folder and installer-mount guide](https://boa.decompiler.dev/docs.html#gui-shared-folders)

## Runbooks and Scheduling

Create reusable runbooks for guest commands, host-file staging, artifact collection, recording, delays, and screen-text monitoring. Artifact sources can point to local directories or Git repositories. Import and export runbook JSON to share workflows.

Host scripts and executables selected for a runbook are staged into the guest and run there. Screen-text monitoring watches the display asynchronously; it is not a blocking wait step. Guest actions require a running, unpaused, app-managed VM with a connected Boa Guest Agent. Recording and OCR steps also need a usable VM display.

In **Scheduler**, create schedules with actions for boot, shutdown, suspend, resume, or a saved runbook. Choose local times and weekdays, then inspect each action's last result.

Keep Box of Apples open and the host awake. The scheduler does not wake a sleeping Mac or catch up missed actions, and scheduled runbooks cannot prompt for administrator passwords. After changing schedules through the CLI, reopen the app to load the updated definitions.

[Runbook guide](https://boa.decompiler.dev/docs.html#gui-runbooks) | [Scheduler guide](https://boa.decompiler.dev/docs.html#gui-scheduler)

## Web Dashboard and API

The desktop app includes an optional authenticated web server. It is disabled by default and runs on your Mac, not on the public Box of Apples website.

1. Open **Settings > Web Dashboard**.
2. Leave **Bind Address** set to **This Mac Only** and the port at `7427`, then enable **Server Mode**.
3. Copy the **Authentication Token**.
4. Open [the local dashboard](http://127.0.0.1:7427/), enter the token, and choose **Sign In**.

Use the dashboard for VM and image management, supported snapshot operations, saved runbooks, schedules, guest actions, and a live console. Use the native app for the full runbook builder, Endpoint Security analysis, and clipboard history.

The JSON API supports token authentication for automation. Keep the token private: it grants control of the host's dashboard and actions. For access from another computer, configure a reachable bind address and use HTTPS or an appropriately secured TLS reverse proxy. Built-in **HTTPS Self-Signed** requires trusting the generated certificate on your clients. Keep the desktop app open while using the server.

[Dashboard setup](https://boa.decompiler.dev/docs.html#gui-web-server) | [API authentication](https://boa.decompiler.dev/docs.html#web-api) | [API reference](https://boa.decompiler.dev/docs.html#api-reference)

## Command Line

The package includes `boa` inside the application bundle:

```sh
"/Applications/Box of Apples.app/Contents/Resources/boa" --help
```

Add its directory to the current shell's `PATH` for shorter commands. To keep this setting for future terminals, add the export line to your shell configuration.

```sh
export PATH="/Applications/Box of Apples.app/Contents/Resources:$PATH"
boa --help
```

Run the CLI as the same macOS user who uses the app to access the same library. It is not automatically added to `PATH` by the installer.

| Command | Purpose |
| --- | --- |
| `boa image` | Discover, download, import, inspect, and manage restore images. |
| `boa vm` | Create, install, configure, start, stop, inspect, import, and export VMs. |
| `boa vm snapshot` | List, create, update, delete, and restore snapshots. |
| `boa template` | List, seal, unseal, and create disposable VMs from templates. |
| `boa scheduler` | Manage schedules and their actions. |
| `boa settings` | Inspect and update core settings. |

```sh
boa image discover-latest
boa image download-latest
boa image list --json
boa vm list --json
boa template list
boa scheduler list --json
boa vm snapshot --help
```

`--json` is supported by individual subcommands, not as a global flag. Use `<command> --help` to check the options available in your installed build. The CLI does not have a standalone runbook command group; use the application, scheduler, or documented web API for runbook execution.

`boa vm start --headless` launches a separate CLI runtime. Use an app-managed session for app-only features such as Endpoint Security inspection, clipboard history, and USB device attachment.

[Complete CLI reference](https://boa.decompiler.dev/docs.html#cli-reference)

## Data Locations

The host library lives under:

```text
~/Library/Application Support/Box of Apples/
```

| Relative path | Contents |
| --- | --- |
| `Library/Images/` | Restore-image metadata and managed IPSW downloads. |
| `Library/VMs/Bundles/` | VM bundles, including their disk and configuration files. |
| `Library/VMs/DisposableRuns/` | Temporary runtime clones. |
| `Library/VMs/Scheduler/schedules.json` | Saved schedule definitions. |
| `Library/Runbooks/` | Runbook definitions and execution records. |
| `Library/Artifacts/` | Artifact source definitions and caches. |

Collected artifacts and console captures use the destinations configured for their workflows. In **Settings > Storage**, use the reveal controls to open the image, VM bundle, and disposable-run locations in Finder.

Snapshot catalogs and layers remain inside their VM bundles. A linked disposable also depends on its template outside its own bundle. Manage these through the app, CLI, or API rather than moving or deleting individual disk layers manually.

Use `.boa-vm` export for eligible stopped VMs and runbook JSON export for workflow definitions. Linked disposables cannot currently be exported. Keep backups of persistent bundles and important collected results.

## Screenshots

### Image Library

<p align="center">
  <img src="README-assets/boa-image-library.png" alt="Box of Apples Image Library with macOS restore images">
</p>

### Virtual Machines

<p align="center">
  <img src="README-assets/boa-vm-overview.png" alt="Box of Apples virtual machine library and inspector">
  <img src="README-assets/boa-vm-overview1.png" alt="Additional view of the Box of Apples virtual machine workspace">
</p>

### Runbooks

<p align="center">
  <img src="README-assets/boa-runbooks.png" alt="Box of Apples runbook library">
  <img src="README-assets/boa-runbook-example.png" alt="Box of Apples runbook builder with automation steps">
</p>

## Troubleshooting

- **No create-ready image:** download or import a compatible IPSW and confirm that it is supported by the current host.
- **Guest Agent does not connect:** enable Boa Guest Agent mode, install the package inside the guest, log in to a guest desktop session, and try **Query Guest Info**.
- **Installer folder is missing:** enable its mount toggle and save the configuration. Restart a CLI-owned session or one without a shared-folder device.
- **Endpoint Security has no events:** check guest system-extension approval and Full Disk Access, deploy the required subscriptions, start collection, and review mute rules.
- **Snapshots are unavailable:** use a macOS 27+ host and a stopped, unsealed, persistent ASIF VM. A paused VM, disposable VM, or raw disk does not qualify.
- **A disposable uses copied backing:** its template did not meet the linked-clone eligibility rules. Check the host, creation capability, stopped state, ASIF format, and absence of a snapshot stack.
- **A schedule did not run:** confirm the schedule and action are enabled, the app is open, the host is awake, and the guest is ready. Inspect the action's last result; missed actions are not replayed.
- **CLI command not found:** use the full application-bundle path or the `PATH` setup above.
- **Disk pressure:** inspect managed images, VM bundles, snapshot layers, captures, and exports. Remove items through their supported management actions.

See the [troubleshooting guide](https://boa.decompiler.dev/docs.html#gui-troubleshooting) for more detail. For a reproducible issue, [open a GitHub issue](https://github.com/txhaflaire/box-of-apples/issues) with the Box of Apples version, host and guest macOS versions, reproduction steps, and relevant logs. Remove authentication tokens, passwords, and sensitive guest data before sharing diagnostics.
