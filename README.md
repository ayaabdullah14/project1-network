# ENCS3320 – Computer Networks Project #1: Socket Programming

**Faculty of Engineering & Technology – Electrical & Computer Engineering Department**
Birzeit University | Spring 2025

**Team Members:**
- Aya Abdullah — ID: 1220782
- Lana Darmna — ID: 1220588
- Jeneen Sattof — ID: 1221682

**Instructor:** Alhareth Zyoud
**Submission Date:** May 10, 2025

---

## Overview

This project covers three practical networking tasks that build hands-on experience with socket programming, network diagnostics, and real-time client-server communication.

---

## Project Structure

```
project/
├── server.py               # Task 2: HTTP web server (port 9958)
├── game_server.py          # Task 3: TCP/UDP hybrid game server
├── game_client.py          # Task 3: Game client
├── html/
│   ├── main_en.html        # English homepage
│   ├── main_ar.html        # Arabic homepage
│   ├── mySite_1220588_en.html   # English media search page
│   └── mySite_1220588_ar.html   # Arabic media search page
├── images/                 # Static image assets
└── videos/                 # Static video assets
```

---

## Task 1 – Network Commands & Wireshark

Hands-on use of standard network diagnostic tools to analyze traffic and understand DNS, routing, and connectivity.

**Commands used:**

| Command | Purpose |
|---|---|
| `ipconfig /all` | View IP address, subnet mask, gateway, DNS |
| `ping <host>` | Test reachability and latency |
| `tracert <host>` | Trace the route packets take |
| `nslookup <host>` | Resolve domain names to IP addresses |
| `telnet <host> 80` | Test raw TCP connectivity on port 80 |
| `ipconfig /flushdns` | Clear DNS cache before Wireshark capture |

**Wireshark:** Captured DNS query and response for `gaia.cs.umass.edu` after flushing the cache, confirming the resolved IP `128.119.245.12`.

---

## Task 2 – Simple Web Server (Socket Programming)

A lightweight HTTP server built with Python's raw `socket` library — no Flask, no Django.

### Running the Server

```bash
python server.py
```

The server starts on **port 9958** (derived from Lana's student ID: 1220588) and binds to all interfaces (`0.0.0.0`).

### Features

- Serves static HTML pages (English & Arabic, including RTL layout support)
- Handles image and video file requests from `/images/` and `/videos/`
- Smart fallback redirects:
  - Missing image → redirects to **Google Images**
  - Missing video → redirects to **YouTube**
- Proper HTTP status codes: `200 OK`, `307 Temporary Redirect`, `403 Forbidden`, `404 Not Found`, `500 Internal Server Error`
- Path traversal protection via `get_safe_path()`
- Request logging: client IP, requested resource, response code

### Served Pages

| URL | Description |
|---|---|
| `/` or `/en` | English homepage |
| `/ar` | Arabic homepage |
| `/images/<filename>` | Serve image file |
| `/videos/<filename>` | Serve video file |
| `/request_handler` | Handle media search form submissions |

### Security

All file paths are validated against the server's base directory to prevent directory traversal attacks. Requests resolving outside the allowed directories receive a `404` response.

---

## Task 3 – TCP/UDP Hybrid Multiplayer Guessing Game

A multiplayer number-guessing game using TCP for reliable control messages and UDP for real-time gameplay.

### Architecture

| Protocol | Port | Usage |
|---|---|---|
| TCP | 6000 | Player registration, game state, announcements |
| UDP | 6001 | Real-time guess submission and responses |

### Running the Game

**Start the server:**
```bash
python game_server.py
```

**Start a client (run in a separate terminal for each player):**
```bash
python game_client.py
```

### Game Rules

- Requires **2–4 players** to start
- Players guess a secret number in the range **1–100**
- Each round lasts **60 seconds**
- Server responds with `higher`, `lower`, or `correct` after each guess
- A **10-second cooldown** between guesses per player
- If time runs out with no correct guess, the round ends with no winner
- Players can type `exit` at any time to leave
- If only one player remains, they are asked whether to continue alone

### Scoring

The first player to guess correctly wins the round. The overall winner is determined after all rounds are completed.

### Test Cases Covered

- ✅ Normal two-player game with a winner
- ✅ Player exits mid-game
- ✅ Last player choosing to continue or quit alone
- ✅ Duplicate player name rejection
- ✅ Round timeout with no winner

---

## Dependencies

- Python 3.x (standard library only — `socket`, `threading`, `os`, `urllib.parse`, `random`, `time`)
- No external packages required

---

## Known Limitations

**Task 2:**
- Single-threaded; handles one request at a time
- No support for cookies, sessions, or complex POST bodies
- Does not implement the full HTTP/1.1 specification

**Task 3:**
- Tested on localhost only; cross-network play requires port forwarding or NAT configuration
- Supports a maximum of 4 players
- Command-line interface only; no GUI

---

## References

1. Socket Programming in C/Python – https://www.scaler.com/topics/socket-programming-in-c/
2. TCP vs UDP – https://www.avast.com/c-tcp-vs-udp-difference
3. Network Commands – https://www.atera.com/blog/network-commands/
4. Web Servers – https://www.techtarget.com/whatis/definition/Web-server
5. How Web Servers Work – https://www.geeksforgeeks.org/web-servers-work/
