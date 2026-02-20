---
title: "How I Built Focus Display: A Beautiful Dashboard That Keeps My Team Aligned"
slug: "how-i-built-focus-display"
date: "2025-08-15"
excerpt: "I wanted a simple way to keep my team's goals visible throughout the day. So I built Focus Display — a stunning full-screen dashboard that rotates through our Trello cards. Here's how it looks and how you can use it too."
tags: ["productivity", "trello", "dashboard", "team-work"]
author: "Mokhamad Arfan Wicaksono"
readingTime: 6
published: true
---

# How I Built Focus Display: A Beautiful Dashboard That Keeps My Team Aligned

I had a problem. Our team used Trello for everything — sprint goals, daily priorities, project deadlines — but once we closed the browser tab, those priorities disappeared from view. Out of sight, out of mind.

I wanted something that would keep our goals front and center throughout the day. Something beautiful enough to put on a shared screen that everyone would see. Something simple that just worked.

So I built **Focus Display**.

## What I Created

Focus Display is a full-screen dashboard that cycles through your Trello cards automatically. Every 10 seconds, it fades to the next card, showing your team's priorities in a clean, distraction-free way.

Here's what you see when you open it:

### The Display

At the top right corner, there's a clock showing both the regular date (Wed, Jan 15, 2024) and the Hijri date (15 Rajab 1445 H) side by side. I added this because it's useful for multicultural teams.

The main area shows your Trello card in full detail:

- **The title** appears large and bold at the top
- **Labels** show as colorful tags with their names
- **Due dates** appear with warnings when something is overdue or due soon
- **Team members** are displayed with their profile pictures and names
- **Checklists** show progress bars (like "3/5 completed") with each item listed and checked
- **Descriptions** display the full text content

### Dynamic Backgrounds

This is my favorite part. Each card can have its own visual identity:

If a card has colored labels in Trello, the background becomes a beautiful gradient using those colors. A green-labeled card gets a soothing green gradient. A purple-labeled card gets a rich purple gradient.

Even better — you can add a cover image. Just put this in your card description:

```
Cover: https://example.com/your-image.jpg
```

The display will grab that image and show it as a full-screen background. It's perfect for visual cards like design mockups, screenshots, or inspirational images.

## How to Use It

Getting started is simple. You need three things from Trello:

1. **List ID** — Found in your Trello board URL
2. **API Key** — Get it from [trello.com/app-key](https://trello.com/app-key)
3. **Token** — Generate a token from the same page

Then just open the display with those parameters:

```
index.html?listId=YOUR_LIST_ID&apiKey=YOUR_API_KEY&token=YOUR_TOKEN
```

That's it. Your cards will start cycling through on the screen.

### For a Permanent Display

If you want to set this up on a dedicated office monitor (which is what I did), use kiosk mode:

**Chrome/Chromium:**
```bash
google-chrome --kiosk --app="file:///path/to/focus-display/index.html?listId=YOUR_LIST_ID&apiKey=YOUR_API_KEY&token=YOUR_TOKEN"
```

**Firefox:**
```bash
firefox --kiosk "file:///path/to/focus-display/index.html?listId=YOUR_LIST_ID&apiKey=YOUR_API_KEY&token=YOUR_TOKEN"
```

This launches the browser in full-screen mode with no address bar or controls — just your beautiful card display.

## Simple Controls

You don't need a mouse. Everything works from the keyboard:

- **← (Left Arrow)** — Go to the previous card
- **→ (Right Arrow)** — Skip to the next card
- **Spacebar (quick tap)** — Manually refresh from Trello (pull latest updates)
- **Spacebar (hold 2 seconds)** — Toggle auto-refresh on/off

When you hold the spacebar, a countdown circle appears on screen showing the progress. After 2 seconds, it toggles between manual-only mode and auto-refresh-every-5-minutes mode. A status indicator in the corner shows whether auto-refresh is on or off.

## How I Use It

**For Personal Use:**
I run it on my second monitor with personal goals — workout routines, study objectives, daily habits. It's surprisingly motivating to see these rotate throughout the day.

## No Setup Required

What I love most about this project is that it has zero dependencies. No npm install, no build step, no frameworks. It's just HTML, CSS, and vanilla JavaScript.

Open the `index.html` file in any browser — Chrome, Firefox, Safari, Edge — and it works. Host it on GitHub Pages, put it on your company intranet, or run it from a local file. Your choice.

## What's Next

I'm planning to add more features:

- Theme customization so you can pick your own colors
- Configurable rotation timing (faster or slower than 10 seconds)
- Sound notifications for urgent cards
- Support for other tools like Asana, Linear, and Notion
- A mobile app version

But the core experience — beautiful cards cycling on your screen, keeping your team focused on what matters — that's here now.

## Try It Yourself

If you've ever wished your team's priorities were more visible, give Focus Display a try.

Clone the repository, grab your Trello credentials, and open `index.html`. Within minutes, you'll have your goals cycling beautifully on any screen in your office or home.

Sometimes the best tools are the simplest ones.

---

**Built with ❤️ for teams who want to stay focused on what matters most.**

*GitHub: [focus-display](https://github.com/marfanwicaksono/focus-display)*
