# Dev Log: GOLD Additions

This is the original author's [repo](https://github.com/unsurprisable/gold-additions)

It is a Chrome extension that downloads the course schedule as an ICS file from the school portal (it's called GOLD, hence the name), and some other features too.

I make some contributions during winter break and some more recently, figured I'll write a dev log as my first blog.

---

## Changes I Made

- Added final exams, pass times, and other dates to export file
- Used [my code](https://github.com/xziyu6/export-ucsb-gold) to semi-automate quarter start and end date retrieval (previously needed to manually Ctrl+C Ctrl+V)
- Tweaked the settings popup
- Lots of refractoring

Lesson: refractoring is so fun :D

## Problems I Encountered

### 1

**Problem**: If you put the HTML you want to inject in a separate file, you need to get it with

```JavaScript
const response = fetch(chrome.runtime.getURL('xxx.html'));
const modalHtml = response.text();
backdrop.insertAdjacentHTML('beforeend', modalHtml);
```

What you see is even though the HTML is injected, the buttons don't work.

**Solution**: Use `async` and `await`, put all code related to the injection a huge `async` block. No idea why `fetch` is designed like this but whatever.

```JavaScript
(async () => {
    const response = await fetch(chrome.runtime.getURL('xxx.html'));
    const modalHtml = await response.text();
    backdrop.insertAdjacentHTML('beforeend', modalHtml);

    // remaining code
})();
```

**Rationale**: `fetch` is asynchronous, so upcoming code runs before the HTML is injected, `async` fixes this because all the remaining code waits for the await lines to finish.

Note: `await` only happens in the `async` block, code outside the block still runs asynchronously, so EVERYTHING depending on the HTML should be in the async block.

### 2

**Problem**: Injected HTML can't use `<link rel="stylesheet" href="xxx.css">`, you'll get `Failed to load resource` (I think, I just tried replicating the error)

**Solution**:

```JavaScript
const cssLink = document.getElementById('ics-settings-css');
cssLink.href = chrome.runtime.getURL('css/ics-settings.css');
```

**Rationale**: When the extension is running, since the script is injecting the text of the HTML into the page. Its context is of the schedule webpage, and can't access the CSS file by relative path.

## Blah Blah Blah

Really proud of the refractoring I did. Not much technical stuff to it, but it feels good to see my code becoming more and more readable. I pretty much rewrote the entire repo. Also did some documentation (the scraping looks all over the place, I blame whoever designed GOLD)

This is also my first time using AI to code. Github Copilot saved me so much time, mostly with researching implementation methods and mass refractoring. But it's also really dumb, so I'm still working on finding a middle-ground.

I'll try to make dev logs a habit and write them during development in the future (took some time trying to remember the problems I encountered)
