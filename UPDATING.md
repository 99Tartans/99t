# Updating the 99 Tartans website

Vercel watches the `main` branch and redeploys https://www.99tartans.com automatically
about a minute after every push. There is no build step and nothing to install.

---

## 1. Adding or editing events (most common task)

All events live in **`events.json`**. The Events page reads this file and builds
the Upcoming and Past sections itself, so you never edit `events.html` for routine updates.

### Add a new event

1. Open `events.json`.
2. Copy an existing event block (everything between `{` and `}` inside the `"events"` list)
   and paste it as a new entry. Make sure each block is separated by a comma.
3. Fill in the fields:

| Field | What to put |
|---|---|
| `id` | A unique slug, e.g. `"jane-doe-acme-2027"` |
| `title` | Headline shown on the card |
| `speaker` / `speakerRole` | Name and "Title, Company". Set `speaker` to `null` if there's no single speaker |
| `format` | Tag text, e.g. `"Fireside Chat · In Person"` or `"Fireside Chat · Virtual"` |
| `date` | `"YYYY-MM-DD"`. Controls the big date on the card and whether it's Upcoming or Past |
| `time` | e.g. `"5:30–6:30 PM ET"`, or `null` |
| `location` | e.g. `"Swartz Center for Entrepreneurship"` or `"Virtual · Pittsburgh"` |
| `audience` | Who can attend |
| `description` | One short paragraph |
| `registration` | `{ "url": "...", "lumaEventId": "evt-..." }` for a Luma event, or `null` |
| `recording` | `{ "url": "https://youtu.be/...", "label": "Watch recording" }` or `null` |

Example of a new upcoming Luma event:

```json
{
  "id": "jane-doe-acme-2027",
  "title": "Fireside Chat with Jane Doe (Acme)",
  "speaker": "Jane Doe",
  "speakerRole": "Founder & CEO, Acme",
  "format": "Fireside Chat · In Person",
  "date": "2027-02-12",
  "time": "5:30–6:30 PM ET",
  "location": "Swartz Center for Entrepreneurship",
  "audience": "99 Tartans members & CMU students/alumni",
  "description": "A conversation with Jane Doe about building Acme from Pittsburgh.",
  "registration": {
    "url": "https://luma.com/event/evt-XXXXXXXX",
    "lumaEventId": "evt-XXXXXXXX"
  },
  "recording": null
}
```

The `lumaEventId` is the `evt-...` part of the Luma URL. With it, clicking the card opens
the Luma registration form in a popup. Without it (set `"lumaEventId": null`), the
Register button just links out to the URL.

### After an event happens

Nothing to do. Once the date passes, the event moves from Upcoming to Past Events
on its own. The counts update too. If there are no upcoming events, the page shows
a friendly "No upcoming events right now" message.

### Add a YouTube recording to a past event

Change `"recording": null` to:

```json
"recording": {
  "url": "https://youtu.be/VIDEO_ID",
  "label": "Watch recording"
}
```

Use the short share link without the `?si=...` tracking part. The card then shows a
"Watch recording" button instead of "Concluded".

### Force an event into a section

Add `"status": "upcoming"` or `"status": "past"` to the event to override the date logic.

### Check your JSON before pushing

A missing comma or quote breaks the whole Events page. Paste the file into
https://jsonlint.com, or run:

```
python3 -c "import json; json.load(open('events.json')); print('OK')"
```

---

## 2. Editing other pages

Each page is one self-contained file: `index.html`, `about.html`, `team.html`,
`portfolio.html`, `gallery.html`, `membership.html`, `faq.html`, `contact.html`.
Open the file, find the text, change it, save. Styles are in a `<style>` block at the top
of each file. Images go in the `photos/` folder and are referenced as `photos/filename.jpg`.

The navigation bar and footer are copied into every page, so a link change there must
be made in all eight files.

---

## 3. Publishing changes

### Option A: Edit on GitHub.com (no software needed)

1. Go to https://github.com/DavidZhongtai/99t.
2. Click the file (for example `events.json`), then the pencil icon (Edit).
3. Make your change, then click **Commit changes** and commit directly to `main`.
4. Wait about a minute and reload https://www.99tartans.com/events.html.

### Option B: Edit locally with git

```
git pull
# edit files
python3 -c "import json; json.load(open('events.json')); print('OK')"   # if you touched events.json
git add -A
git commit -m "Add Jane Doe fireside chat"
git push
```

### Previewing locally

The Events page loads `events.json` with a network request, which browsers block
when you open the HTML file directly from Finder. Run a tiny web server instead:

```
cd path/to/99t
python3 -m http.server 8000
```

Then open http://localhost:8000/events.html. Other pages work fine when opened directly.

---

## 4. Checking a deploy

Vercel's dashboard (project `99t`) lists every deploy with its status and a preview URL.
If the live site doesn't update, check there first for a failed deploy, then hard-refresh
the page (Cmd+Shift+R).
