# Matrix Webhook Add-on for Home Assistant

This Home Assistant add-on provides a webhook integration with Matrix. It allows sending messages and images to a Matrix chat room from Home Assistant using an HTTP POST request. This add-on can be used to send both plain text and image content to specified Matrix rooms based on a predefined configuration.

![Supports amd64 Architecture][amd64-shield] ![Supports armv7 Architecture][armv7-shield]

## Features

- Send **plain text messages** to a specified Matrix room.
- Send **images** (both encrypted and unencrypted) to Matrix rooms.
- **Emoji-based verification** for incoming webhook requests.
- **Supports YAML, JSON, and Raw message formats** for incoming requests.

## Installation

1. Open **Home Assistant** and navigate to **Supervisor** → **Add-on Store**.
2. Click on **Repositories** and add the GitHub repository URL for this add-on.
3. Find the **Matrix Webhook** add-on in the store and click **Install**.
4. After installation, configure the add-on through its settings.

## Configuration
```

    name: Message Format
    description: Format of the outgoing Matrix message (yaml, json, or raw)

    name: Use Markdown
    description: Enable Markdown formatting in Matrix messages

    name: Allow Unicode
    description: Enable support for Unicode characters in messages

    name: Display App Name
    description: Include the application name in the sent message

    name: Matrix Server URL
    description: URL of your Matrix homeserver (e.g. https://matrix.example.org)

    name: Verify SSL
    description: Enable SSL certificate verification when connecting to the Matrix server

    name: Matrix User ID
    description: Matrix user ID for the bot (e.g. @bot:matrix.example.org)

    name: Matrix Password
    description: Password for the Matrix bot account

    name: Device Name
    description: Unique identifier for the Matrix device (used for login)

    name: Admin Room ID
    description: Room ID to receive admin/debug messages (e.g. !abc123:matrix.example.org)

    name: Known Tokens
    description: Comma-separated list of token-to-room ID pairs used for routing messages
```

## Generate token

You able to use any token these restriction. Used characters: a–z,A–Z,0–9

Use generator, such as: https://it-tools.tech/token-generator or what you want.

### Using Emojis for Verification

- Emojis are used for basic verification of incoming webhook requests.
- You able to start verification a other client (for example Element). The addon automatically accept it.
- You can verify the emojis in addon log.

### Home Assistant usage

Make a simple notify entity (with "rest" platform)
```
notify:
  - name: webhook_notify
    platform: rest
    resource: "http://<your_home_assistant_ip>:8006/post/<token>"
    method: POST_JSON
    headers:
      Content-Type: application/json
    data_template:
      message: "{{ message }}"
```
### Use with Apprise

You able to use these Addon with Apprise, simply use "json" backend.
```
json://<your_home_assistant_ip>:8006/post/<token>?:version&:type&:title
```
### Other usage

To send a message, make a `POST` request to `http://<your_home_assistant_ip>:8006/post/<token>` with the following payload:

***Please note that if you send data in JSON format and the key is "message" the add-on will automatically only forward the value. (This is necessary for Home Assistant/Apprise compatibility.)***

**YAML Format:**

```yaml
app: test
message: Hello, this is a test message!
```

**JSON Format:**

```json
{
  "app": "test",
  "message": "Hello, this is a test message!"
}
```

**Raw Format:**

```
app: test
message: Hello, this is a test message!
```
### Sending images from Home Assistant

Simply make a Shell Command:
```
shell_command:
  send_image: >
    curl -X POST "http://<your_home_assistant_ip>:8006/post/<token>"
    -F "image=@/path/to/image.png"
    -F "caption=Test Image"
```
### Sending images from other service

You can also send images to the Matrix room by using a `POST` request with the following format:

```bash
curl -X POST http://<your_home_assistant_ip>:8006/post/<token>   -F "image=@/path/to/image.png"   -F "caption=Test Image"
```

The add-on supports both **encrypted** and **unencrypted** image uploads.

### Logging

Logs can be accessed from the Home Assistant log interface. The add-on will log any errors related to Matrix authentication, message sending, and image uploads.

[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
