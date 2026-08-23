# CustomSena User Guide

`customsena` is a macOS application for replacing Sena 60S voice prompts, validating custom voice firmware, creating a device backup, and installing the voice FOTA over USB.

This standalone guide is intended for users of the distributed `customsena` executable.

## 1. Supported features

- Select and download an official Sena 60S base firmware
- View the voice prompt list in the selected firmware
- Replace prompts with MP3, WAV, M4A, and other macOS-readable audio
- Automatically convert audio and fit it to the firmware voice area
- Build and validate custom voice firmware
- Detect the Sena 60S and view device information
- Back up the complete 16 MiB flash
- Run an installation preflight
- Install the voice FOTA

The supported target is the Sena 60S Airoha voice FOTA. QCC external MCU updates and other Sena products are not supported.

## 2. Runtime requirements

- macOS
- Sena 60S
- A USB cable that supports data communication
- Internet access when downloading official firmware for the first time

Run the distributed `customsena` executable to open the TUI.

## 3. Usage order

Run the distributed `customsena` executable, then follow this order in the TUI:

```text
1. Choose base firmware   /firmware <language>
2. Choose an audio file   /prompt <id> <audio-path>
3. Build and verify       /build
4. Back up device         /backup
5. Prepare installation   /install
6. Confirm installation   /confirm
```

Complete each step before moving to the next. `/install` checks the device, backup, and firmware compatibility; only run `/confirm` after all checks pass.

## 4. Select a base firmware and prompts

Enter the following in the TUI input field:

```text
/firmware English
```

Supported languages:

`English`, `French`, `Spanish`, `Italian`, `German`, `Korean`, `Japanese`, `Dutch`, `Russian`, `Chinese`, `Finnish`, and `Polish`.

The selected firmware's actual locale and prompt list are then shown. Search the prompt list or narrow it by category to find the desired voice ID.

Prompt contents may differ between language firmware files. Do not reuse an ID from another firmware without checking the currently selected firmware.

## 5. Replace audio and build firmware

Select a prompt and provide an audio file:

```text
/prompt <id> <audio-path>
```

You can also drag an audio file from Finder onto the selected prompt. Set each replacement separately when replacing multiple prompts. Use `/restore <id>` to remove a replacement and restore the original prompt.

Prompt meanings by firmware language:

| Prompt ID | English | Korean |
| --- | --- | --- |
| `7` | `hello` | `안녕하세요` |
| `5` | `goodbye` | `감사합니다` |

The same prompt ID can have different spoken text depending on the selected base firmware language. Prompt 7 is `hello` in the English firmware and `안녕하세요` in the Korean firmware. Prompt 5 is `goodbye` in the English firmware and `감사합니다` in the Korean firmware.

When ready, build the firmware:

```text
/build
```

Audio is converted to 16 kHz mono MP3. If space is limited, the builder retries at 40, 32, 24, 16, and 8 kbps. If the image is still too large at 8 kbps, reduce the number or duration of replacements.

After a successful build, a custom `.img` file and an `.img.customsena.json` verification report are created beside the executable. Keep both files together.

## 6. Detect the device and create a backup

Connect the Sena 60S over USB and check its status in the TUI. If it is not detected, enter `/detect` to search again.

Always create a backup before installation:

```text
/backup
```

The backup reads the complete 16 MiB flash and may take a long time. Do not disconnect USB during the backup. The backup file and SHA-256 report are created beside the executable.

The application can create and validate a backup, but it does not provide a command to restore that backup to the device. A backup is not an automatic recovery guarantee.

## 7. Install the custom firmware

After completing the previous steps, enter the following in the TUI input field:

```text
/install
```

The application checks:

- Outer image MD5 and voice FOTA SHA
- Voice FOTA product name `AB1585_Headset`
- Device product name `Sena Headset HID`
- AB158x SDK version
- FOTA partition offset and size
- Whether the connected device matches the backup
- Whether the voice FOTA fits in the partition

When all checks pass, a confirmation step is shown. To proceed:

```text
/confirm
```

To cancel:

```text
/cancel
```

## 8. Usage

### Main TUI commands

| Command | Function |
| --- | --- |
| `/status` | Show current device, firmware, and backup status |
| `/detect` | Detect the device again |
| `/firmware <language>` | Select or download an official base firmware |
| `/prompt <id> <audio-path>` | Set replacement audio |
| `/restore <id>` | Restore the original prompt |
| `/build` | Build and validate custom firmware |
| `/backup` | Back up the complete device flash |
| `/install` | Start installation checks and confirmation |
| `/confirm` | Start the confirmed installation |
| `/cancel` | Cancel installation confirmation |
| `/set base\|output\|backup <path>` | Set a path directly |
| `/help` | Show help |

### Keyboard and mouse

- `1`–`4`: switch screens
- `↑`/`↓`: move selection
- `Enter`: select or execute
- `/`: search commands and prompts
- `L`: switch Korean/English
- `?`: show help
- `Q`: quit

Menus and buttons can be clicked with the mouse. Do not exit while an operation is running.

## 9. Safety precautions

### This writes to the real device

Once installation starts, the Sena 60S FOTA staging area is modified. A wrong device, image, backup, or interrupted USB connection can cause problems.

### Make the backup from the target device

Do not use a backup from another device. The application compares part of the connected device with the backup, but you must still verify the model and files before starting.

### Keep USB connected

Do not disconnect USB or allow the Mac to sleep during backup or installation. Avoid unstable USB hubs and cables.

### Do not edit generated images manually

Editing an `.img` file with a hex editor can invalidate its MD5, SHA, compression, and encryption structures. Rebuild it in the TUI instead.

### Check the audio files

Use audio that macOS can read. Prompt IDs cannot be duplicated. If the voice area is full, reduce the audio duration or the number of replacements.

### Do not bypass compatibility checks

Do not use the application with unsupported devices or ignore safety-check failures.

## 10. Troubleshooting

1. Check that the Sena 60S is connected over USB.
2. Enter `/detect` in the TUI.
3. Check the product and SDK information displayed in the TUI.
4. Recheck the selected firmware language and prompt IDs.
5. Confirm that the custom image and `.img.customsena.json` report are together.
6. Confirm that the backup is 16 MiB.
7. If an error occurs, do not repeatedly disconnect USB. Record the error and device state first.

When sharing an error report, mask serial numbers and other personal identifiers. Include the firmware filename and the complete error message.
