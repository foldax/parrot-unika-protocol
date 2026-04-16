# Parrot MKi / UNIKA Protocol Documentation

This project documents and analyzes the proprietary communication protocol used mainly between the **Parrot MKi** hands-free systems and the **UNIKA** interface, although the same protocol also appears to be used in other related devices.

## Background

The **Parrot MKi** was once a high-end automotive hands-free kit, focused on phone integration and voice features. While it was a leading product in its time, it was eventually displaced by newer infotainment platforms such as **Android Auto**. The product line was discontinued, and Parrot later shifted its focus to drones.

**UNIKA** is a universal interface designed to connect Parrot devices to a car’s factory steering wheel controls. It supports vehicle communication buses such as **CAN bus** and **K-Bus / BMW i-Bus**.

## Why analyze the protocol?

A fair question is: if the hardware can still be bought and it basically works, why spend time reverse engineering the protocol?

The answer is simple: the existing implementation is limited and does not make proper use of some factory controls.

I bought a **Parrot UNIKA**, connected it, and discovered that entering the menu required an awkward sequence of rapid button presses such as:

`next / previous / next / previous`

In BMW cars, there are dedicated buttons for phone-related functions, such as:

- **R/T** button
- **phone / handset** button

Yet none of these were properly used for this purpose.

I contacted **Parrot** and asked whether this behavior could be improved. I was then informed that the module was actually handled by **Paser.it**. I contacted them as well, but never received a response.

So I did what I know best: I started analyzing the protocol myself.

## Documentation structure

- [`docs/protocol-overview.md`](docs/protocol-overview.md) — high-level protocol description
- [`docs/message-format.md`](docs/message-format.md) — packet layout and field descriptions (?)

## Disclaimer

This is an independent project and is **not affiliated with or endorsed by Parrot, Paser.it, BMW, or any other related company**.
All information is provided for documentation, research, and interoperability purposes only.
**Use at your own risk.** I am not responsible for any damage, failures, or consequences caused by using the information published in this repository.
