---
layout: post
title:  "Setting up Bluethooth on Raspberry Pi"
date:   2021-07-01 19:56:00 +0200
categories: work setup
---
When using embedded systems like Raspberry Pi, it is useful to connect input peripherals via wireless connection (like bluethooth) to dedicate USB connection for other objectives.

**Table of Contents**

- [Installation](#installation)
- [Use](#use)


## Installation

Unlike a normal Ubuntu desktop, to use bluetooth devices in Rasperry Pi, should be installed the package `pi-bluetooth`,

```bash
sudo apt install pi-bluetooth bluetooth blueman
```

## Use

Once installed bluetooth driver, Raspberry Pi is ready to pair with peripherals like keyboards or mouses using the tool,

```bash
bluethoothctl
```