# Pixel Legacy on NixOS

This repository documents how to configure [NixOS](https://nixos.org/) to connect to **Pixel Legacy** fan servers for [Pixel Worlds](https://store.steampowered.com/app/636040/Pixel_Worlds/).

Pixel Legacy is a community-run continuation of Pixel Worlds, hosted on custom servers. Connecting requires two changes to your system:

1. Redirecting specific game domains to the Pixel Legacy server IP.
2. Trusting the Pixel Legacy Root CA certificate so the game accepts the server’s HTTPS/TLS connections.

This README walks you through doing this the **NixOS way** — fully declarative, reproducible, and without running imperative scripts.

---

## ⚠ Disclaimer

Pixel Legacy is **not** affiliated with the original Pixel Worlds developers or publishers.


---

## 📋 Prerequisites

- A working NixOS installation
- Basic familiarity with editing `configuration.nix` and rebuilding your system
- The Pixel Legacy Root CA certificate file (`rootCA.crt`) from the Pixel Legacy setup files

---

## 🛠 Setup

### 1. Get the Pixel Legacy Root CA certificate

Download the certificate file (`rootCA.crt`) from the official Pixel Legacy Discord or website and place it in the same directory as your `configuration.nix`.

Link: https://mega.nz/file/LEtFWI7L#pxUMkC5mibkPjcP6vR6OxPbx0N2ESclnwEh10uHUoGQ

(`rootCA.crt` is inside "Pixel Legact Automated For PC" -> "Install Pixel Legacy (Steam)" -> "Step 2" in the archive.)

---

### 2. Edit your NixOS configuration

Open `/etc/nixos/configuration.nix` (or the relevant module if using flakes) and add:

```nix
{
  networking.hosts = {
    "74.208.102.182" = [
      "lambda.us-east-1.amazonaws.com"
      "ec2-34-237-73-93.compute-1.amazonaws.com"
      "cognito-identity.us-east-1.amazonaws.com"
      "prod.gamev92.portalworldsgame.com"
    ];
  };

  security.pki.certificateFiles = [
    # rootCA.crt must be in the same folder as the configuration file containing these lines if you wish to copy and paste the configurations as is.
    ./rootCA.crt
  ];
  # Optional, most likely doesn't do anything except for creating an environment variable.
  environment.variables.PW_APPID = "636040";
}
```
