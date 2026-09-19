# Week 06 – Internet Applications

| | |
|---|---|
| **Student** | Zeel Pareshbhai Jani (12331098) |
| **Tutor** | Dr David Ling |

## Task 1 – Knowledge Test

Completed the Week 6 Knowledge Test within the first 10 minutes of the tutorial.

## Task 2 – Create Web Pages in OpenWRT

I opened the example page on the OpenWRT server at `http://192.168.56.2/` (files are served from `/srv/www`) and made these changes:

```sh
cd /srv/www
ls
cp index.html 12331098.html
nano 12331098.html      # add name, ID, date/time button, link to CSS
nano style.css          # colour some text
nano index.html         # add link to 12331098.html
```

1. Copied `index.html` to `12331098.html`.
2. Added a link in `index.html` to the new page.
3. Edited `12331098.html` to show my name and ID, to display the date/time when a button is clicked (JavaScript), and to use an external CSS file.
4. Created `style.css` to change the colour of some text.

### Files created / edited

- [`index.html`](week06/index.html)
- [`12331098.html`](week06/12331098.html)
- [`style.css`](week06/style.css)

**index.html**

```html
<html>
<body>
<h1>Example Page</h1>
<p><a href="12331098.html">Zeel Jani 12331098</a></p>
</body>
</html>
```

**12331098.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Zeel Jani 12331098</title>
<link rel="stylesheet" href="style.css">
</head>
<body>
<h1>Zeel Jani</h1>
<p>Student ID: 12331098</p>
<button onclick="showDateTime()">Show date and time</button>
<p id="datetime"></p>
<script>
function showDateTime() {
  document.getElementById("datetime").textContent = new Date().toString();
}
</script>
<p><a href="index.html">Back to home</a></p>
</body>
</html>
```

**style.css**

```css
h1 {
  color: #1a73e8;
}
#datetime {
  color: #d93025;
  font-weight: bold;
}
```

### Screenshot after pressing "Show date and time"

![Browser after clicking Show date and time](week06/week6-task2-browser-date-time.png)

## Task 3 – Capture HTTP Packets

On the OpenWRT server, from the home directory:

```sh
cd
tcpdump -i eth0 -n -w http-12331098.pcap 'not tcp port 22'
```

In an incognito browser window on Windows I visited `http://192.168.56.2/`, clicked the link to my new page, and clicked **Show date and time** twice. I then stopped the capture with Ctrl-C (**106 packets captured, 0 dropped by kernel**).

- HTTP capture file: [`http-12331098.pcap`](week06/http-12331098.pcap) (11,396 bytes, 106 packets)

### ARP table

`arp -a` – shows `192.168.56.2` (the OpenWRT VM) already present after browsing to it:

![arp -a output](week06/week6-task3-arp-a.png)

`Get-NetNeighbor -AddressFamily IPv4`, after pinging `10.255.57.74` and `google.com`:

![Get-NetNeighbor output](week06/week6-task3-get-netneighbor.png)

`10.255.57.74` (the router) shows **Reachable** because I had just contacted it; `192.168.56.2` shows **Stale** (contacted earlier and aged out); devices I had not communicated with show **Unreachable**.

## Task 4 – Analyse HTTP Packet Capture

I opened `http-12331098.pcap` in Wireshark and filtered on `http`. The capture contains exactly **three** HTTP request/response transactions, all on TCP stream 0 (`192.168.56.1:56050` ↔ `192.168.56.2:80`).

### a) Trigger, request and response for each transaction

| # | What triggered it | Request | Response |
|---|---|---|---|
| 1 | Browser navigating to `http://192.168.56.2/` | Frame 30, t = 18.731864 s, `GET / HTTP/1.1` | Frames 32 and 34 (reassembled), `HTTP/1.1 304 Not Modified` – the browser's cached copy was still valid |
| 2 | Clicking the "Zeel Jani 12331098" link | Frame 38, t = 20.160470 s, `GET /12331098.html HTTP/1.1` (Referer `http://192.168.56.2/`) | Frames 39 and 41, `HTTP/1.1 304 Not Modified` |
| 3 | The browser automatically fetching the stylesheet linked from `12331098.html` (`<link rel="stylesheet" href="style.css">`) | Frame 42, t = 20.223017 s, `GET /style.css HTTP/1.1` (Referer `http://192.168.56.2/12331098.html`) | Frames 43 and 45, `HTTP/1.1 304 Not Modified` |

### b) Five address values for the first request (frame 30)

| Layer | Value |
|---|---|
| Source IP address | 192.168.56.1 |
| Destination IP address | 192.168.56.2 |
| Source port | 56050 |
| Destination port | 80 |
| Transport protocol | TCP (application protocol HTTP, indicated by destination port 80) |

### c) Did clicking the date/time button send a request to the web server?

**No.** Only the three GET requests above appear in the capture, even though I clicked the button twice. The date and time are generated entirely in the browser by the page's JavaScript (`new Date()`), so clicking the button produces no network traffic.

### d) Packet diagram – request for the new web page (frame 38)

![Packet diagram for frame 38](week06/week6-task4-http-packet.png)

- Image: [`week6-task4-http-packet.png`](week06/week6-task4-http-packet.png)
- Source: [`week6-task4-http-packet.drawio`](week06/week6-task4-http-packet.drawio)

| Header | Size | Addresses |
|---|---|---|
| Ethernet II | 14 bytes | Src MAC `0a:00:27:00:00:0a`, Dst MAC `08:00:27:16:fd:64` |
| IPv4 | 20 bytes | Src IP `192.168.56.1`, Dst IP `192.168.56.2` |
| TCP | 20 bytes | Src port `56050`, Dst port `80` |
| HTTP request | 564 bytes | `Host: 192.168.56.2`, `Referer: http://192.168.56.2/` |
| **Total** | **618 bytes** | |

### e) Referrer

The `Referer` was `http://192.168.56.2/` on the request for `12331098.html`, and `http://192.168.56.2/12331098.html` on the request for `style.css`. It identifies the page the browser was on (or loading) when it made the request. Web servers can use it for navigation/traffic statistics, to see which pages link to which, and for hotlink and CSRF checks.

### f) What the server learned about the browser

From the `User-Agent` header the server learned the browser is **Chrome 153.0.0.0** (WebKit/Blink) on 64-bit **Windows 10/11**. Other headers show the language (`en-US`), that it accepts gzip/deflate compression, and that Do Not Track (`DNT: 1`) was sent.

### g) HTTP version and transport protocol

The application protocol is **HTTP/1.1** and the transport protocol is **TCP** (over IPv4).

### h) Connection setup

The TCP three-way handshake is frames **27 (SYN)**, **28 (SYN, ACK)** and **29 (ACK)**.

- Handshake duration: frame 29 − frame 27 = 18.730188 s − 18.729793 s = **0.000395 s (395 µs)**, matching Wireshark's own iRTT value.
- From the start of connection setup (frame 27) to the start of data transfer (the GET in frame 30 at 18.731864 s): 18.731864 − 18.729793 = **0.002071 s (about 2.1 ms)**.

### i) Acknowledgements

Every data segment is acknowledged by the other side. Frame 31 (Ack = 545) acknowledges the `GET /` in frame 30; frame 33 acknowledges the first response segment and frame 35 the second. The same pattern repeats for the `/12331098.html` and `/style.css` exchanges. An acknowledgement is typically sent as soon as (or shortly after) data is received, confirming how many bytes arrived so the sender knows they were delivered. Periodic TCP Keep-Alive / Keep-Alive ACK pairs (e.g. frames 36–37 and 47–50) also appear, keeping the persistent connection open between requests.

## Task 5 – View Your Cookies (Homework)

I used Chrome DevTools (F12 → Application → Cookies) on a website I visit regularly (Amazon). I have not included exact cookie values because cookies can contain personal or authentication data. The cookies fall into these groups:

- **Session / authentication cookies** – identify the logged-in session and shopping cart to the server (e.g. `session-id`, `session-id-time`, `session-token`). Most were flagged **HttpOnly** (not readable by page JavaScript) and **Secure** (HTTPS only), which limits exposure to XSS and network sniffing.
- **Preference cookies** – store locale settings so the site renders consistently on later visits: a currency preference and a language/region preference.
- **Tracking / analytics cookies** – persist across sessions to build a picture of browsing behaviour for personalisation and recommendations (e.g. a clickstream/page-hit metric cookie and a persistent browser identifier that ties repeat visits together).
- **Security / bot-protection cookies** – set by the site's web application firewall to distinguish real browsers from bots and scrapers, including one that records state from a CAPTCHA challenge if one was triggered.
