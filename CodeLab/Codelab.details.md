<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    # CodeLab — Every Line Explained in Plain English

This document walks through `codelab-firebase.html` from top to bottom and explains, in ordinary words, what every part of it does: every link, every piece of styling, every HTML element with an `id`, and every JavaScript function with its parameters. It's written so you can understand the file without reading code.

---

## PART 1 — THE `<head>` SECTION

The `<head>` is the part of the page that never shows directly on screen. It sets up the page's title, icon, and brings in outside tools the page depends on.

### Meta tags
- `<meta charset="UTF-8">` — tells the browser to read the file using the UTF-8 text format, which supports every character and emoji used in the page (₦, 🎓, etc.).
- `<meta name="viewport" ...>` — tells mobile browsers how to size the page. `width=device-width, initial-scale=1` makes the page match the phone's screen width instead of zooming out. `viewport-fit=cover` lets the page draw behind the notch/home-bar area on phones with those features, which is why the CSS later adds padding to compensate.
- `<title>CodeLab - Learn to Code</title>` — the text shown in the browser tab.

### The favicon
```
<link rel="icon" href="data:image/svg+xml,...">
```
This is the small icon in the browser tab. Instead of linking to an image file, it's a tiny SVG image (a laptop emoji 💻) written directly into the link as text. This means the icon never needs a separate file to load.

### External resources (the four lines that pull in outside code)
1. `https://cdn.tailwindcss.com` — loads **Tailwind CSS**, a styling toolkit. It's the reason class names like `flex`, `text-2xl`, or `rounded-full` work anywhere in the page — Tailwind reads those class names and generates the matching CSS automatically.
2. `https://cdn.jsdelivr.net/npm/sweetalert2@11.12.4/...` — loads **SweetAlert2**, the library that creates all the nice popup boxes (confirmations, forms, success messages) you see throughout the app, instead of plain ugly browser alerts.
3. `https://js.paystack.co/v1/inline.js` — loads **Paystack's payment widget**, the tool that opens the payment popup when a student pays their course fee.
4. The Google Fonts link loads **Space Grotesk**, the typeface used for all the text on the page.

### The `<style>` block
This is custom CSS written specifically for this page, on top of what Tailwind provides.

- **`:root { ... }`** — settings that apply to the whole page. `box-sizing: border-box` makes padding and borders count *inside* an element's width instead of adding to it (a standard best practice). The `padding-top`/`padding-bottom` using `env(safe-area-inset-top/bottom)` push content away from the notch or home-indicator area on phones that have them; on phones without one, this padding is just zero.
- **`html, body { background: #0a0a0f }`** — sets the whole page's background to a near-black colour.
- **`body { font-family: ...; color: #fff }`** — sets the default typeface and makes all text white by default.
- **`.inp`** — the style used for every text box, dropdown, and text area in the app: full width, rounded corners, a translucent dark background, a thin border, and spacing below it.
- **`.card`** — the style for the rounded boxed "panels" you see everywhere (the profile box, each task, each stat tile): a subtle border, big rounded corners, and internal padding.
- **`.btn`** — the main call-to-action button style: rounded, bold text, and a purple-pink background colour (`#a707f1`), with white text.
- **`.btn2`** — the secondary button style: rounded with just a thin border, no fill colour, used for less important actions like "Logout" or "Cancel".
- **`.swal2-popup`, `.swal2-html-container`, `.swal2-title`** — these override SweetAlert2's own default white popup styling, forcing its background to dark and its text to white, so every popup matches the rest of the app's theme.

### The "offline overlay" styling
This is a full-screen cover that appears automatically whenever the device loses its internet connection.
- `#offlineOverlay` — positioned to cover the *entire* screen (`inset: 0`), sits above everything else (`z-index: 9999`), and is **hidden by default** (`display: none`).
- `#offlineOverlay.show` — when this extra class name `show` is added to the element, it switches to `display: flex`, making it visible and laying its contents out in a column, centred.
- `#offlineOverlay svg` — sizes the "no signal" icon and gives it a floating up-and-down animation (`float`), which bobs it gently forever, with a 13-second delay before the very first bob starts.
- `@keyframes float` — the actual animation: the icon starts at its normal position, rises 12 pixels halfway through, then returns.
- `#offlineOverlay .dot` and `@keyframes blink` — these make the three dots after "Mobile data" blink on and off one after another, like a typing/loading indicator, each dot starting its blink slightly later than the one before (`.2s`, `.4s` delays).

---

## PART 2 — THE VISIBLE PAGE (`<body>`)

### The offline overlay (in the HTML)
Inside `<div id="offlineOverlay">` sits:
- An SVG drawing (a circle with a "no signal" face icon inside, using a `<circle>`, two curved `<path>` lines, another small circle for the "mouth," and a diagonal `<line>` crossing through it to resemble a "blocked" symbol). `style="cursor: pointer;"` makes the mouse pointer turn into a hand when hovering over it, even though nothing is clickable there (cosmetic only, has no real function).
- A heading: "No internet connection".
- A paragraph telling the user to check their Wi-Fi or mobile data, with three blinking dots at different points in the sentence for visual effect.
- A **"Try again" button**, whose `onclick="location.reload()"` simply reloads the whole page when clicked, a common fix for "check if the connection is back."

### The navigation bar and main content area
- `<nav id="nav">` — an empty container. JavaScript fills this with either "Login/Sign up" buttons (when logged out) or the user's name, avatar, and a "Logout" button (when logged in). It starts empty and is rebuilt every time something changes.
- `<main id="main">` — the big empty container where literally everything else is drawn: the landing page, the student dashboard, the tutor dashboard, or the admin dashboard, depending on who's logged in. JavaScript replaces its contents constantly.

### The login/signup popup (`<div id="authModal">`)
This whole box is hidden by default (`class="... hidden ..."`) and only appears when the Login or Sign Up button is clicked.

Inside it:
| Element ID | What it is |
|---|---|
| `mTitle` | The popup's heading text ("Create Account" or "Welcome Back") |
| `roleRow` | The row of buttons letting you pick Student / Tutor / Admin |
| `a_name` | Text box for your full name (shown only when signing up) |
| `a_email` | Text box for your email address |
| `a_pw` | Password text box |
| `a_course` | Dropdown to choose your course |
| `a_code` | Text box for the secret admin access code (only shown if "Admin" is selected) |
| `a_btn` | The main submit button ("Create Account" or "Login") |
| `a_err` | A red error message line, hidden until something goes wrong |
| `a_sw` | The link at the bottom to switch between "Sign up" and "Log in" modes |

There's also a small "or" divider, then a **"Continue with Google"** button with Google's official four-colour "G" logo drawn as an inline SVG (so no external image needs to load for it), and a "Forgot password?" link.

---

## PART 3 — THE JAVASCRIPT PROGRAM

Everything below lives inside `<script type="module">`, which means the code is treated as a modern JavaScript module (this is what allows the `import` lines at the top to work).

### Imports
The first two blocks bring in specific tools from Google's Firebase library, loaded directly from Google's servers:
- From **firebase-app**: `initializeApp` — the function that connects this page to your specific Firebase project.
- From **firebase-auth**: all the tools needed for login — creating accounts, signing in, signing out, password reset emails, and Google sign-in.
- From **firebase-firestore**: all the tools needed to read and write data in the database — getting documents, saving documents, watching for live changes, running queries, and running "transactions" (all-or-nothing multi-step saves).

### The CONFIG section
- `firebaseConfig` — an object holding the unique address of your Firebase project: your `apiKey` (a public identifier, not a secret), `authDomain`, `projectId`, `storageBucket`, `messagingSenderId`, and `appId`. Together these tell the Firebase library exactly which project in the world to talk to.
- `PASS_MARK = 50` — the minimum average score (out of 100) a student needs across their graded tasks to qualify for a certificate.
- `MIN_DAYS = 180` — the number of days (roughly six months) that must pass after payment before someone can be certified.
- `PRICE = 30000` — the course price in Naira.
- `MAX_ADMINS = 2` — the hard limit on how many admin accounts can ever exist.
- `ADMIN_CODE = 'CODELAB-ADMIN'` — the secret word someone must type correctly to register as an admin.
- `COURSES` — the list of course names shown in every dropdown: Python, AI Learning, Web Development, Programming, and Data Analysis.
- `PAYSTACK_KEY` — the public key that tells the Paystack payment popup which merchant account to charge into. (This one starts with `pk_test_`, meaning it's currently in *test mode*, not processing real money.)

### Firebase connection handles
- `fbApp` — the live connection object created by `initializeApp`.
- `au` — the "Authentication" handle; every login/logout/signup action goes through this.
- `fs` — the "Firestore" handle; every database read/write goes through this.

### Small reusable helper functions
These are short, one-line tools used throughout the rest of the file.

- **`$(id)`** — a shortcut. Instead of writing `document.getElementById('nav')` everywhere, the code just writes `$('nav')`. Saves typing, nothing more.
- **`esc(s)`** *(parameter: `s`, the text to clean)* — takes any text (like someone's name or a message) and replaces characters like `<`, `>`, and `&` with safe versions. This stops someone from typing something like `<script>` into a name field and having it run as actual code — a basic but important security habit.
- **`uid(p)`** *(parameter: `p`, a short prefix)* — builds a random unique ID string, combining the prefix with the current time and a random chunk of letters/numbers, e.g. `m_1a2b3c`. Used whenever a brand-new database entry (a material, a task, a message...) needs its own unique identifier.
- **`fmt(t)`** *(parameter: `t`, a timestamp number)* — converts a raw timestamp into a human-readable date and time string.
- **`url(l)`** *(parameter: `l`, a link typed by a user)* — makes sure a link starts with `https://`. If a tutor types `youtube.com/xyz` without the `https://` part, this function adds it automatically so the link actually works when clicked.
- **`fresh()`** — returns a "blank slate" object representing no data at all: nobody logged in, and every list (users, materials, tasks, etc.) empty. This is used to reset the app's memory whenever someone logs out or the page first loads.

### The app's "memory" (state variables)
- **`db`** — the single object holding everything the app currently knows: the logged-in user's own profile (`db.me`), and arrays of every other user, material, task, submission, log entry, announcement, class, attendance record, and chat message the app has loaded.
- **`unsubs`** — a list of "stop listening" functions. Firestore lets the app "subscribe" to live updates; this list remembers all those active subscriptions so they can all be properly cancelled when someone logs out (otherwise the app would keep listening to data it no longer needs).
- **`certBusy`** — a simple true/false flag used to stop the certificate-granting logic from accidentally running twice at the same moment.

### Offline detection
- **`setOffline(isOffline)`** *(parameter: `isOffline`, true or false)* — adds or removes the `show` class on the offline overlay, which is what actually makes it appear or disappear (remember the CSS rule `#offlineOverlay.show { display: flex }` from earlier).
- Two event listeners: the browser itself tells the page the moment the device's connection drops (`'offline'`) or comes back (`'online'`), and these two lines react instantly — showing the overlay, or hiding it and popping up a small "Back online" confirmation.
- `setOffline(!navigator.onLine)` — runs once immediately when the page first loads, checking whether the device is already offline at that moment (in case someone opens the page without any connection at all).

### More small helpers
- **`img(file, max)`** *(parameters: `file`, the image file someone picked; `max`, the biggest allowed width/height in pixels)* — this is the function that shrinks and compresses any photo someone uploads (profile pictures, proof screenshots). It reads the file, draws it onto an invisible canvas at a smaller size, and turns the result into a compact piece of text (a "data URL") that can be stored directly inside a database document — no separate file storage service needed.
- **`av(u, s)`** *(parameters: `u`, a user object; `s`, the size in pixels, defaulting to 40)* — builds the little round avatar picture. If the user has an uploaded photo, it shows that photo in a circle; if not, it shows a purple circle with just the first letter of their name.
- **`uref()`** — a shortcut that returns "the database document belonging to whoever is currently logged in," used constantly whenever the app needs to update the logged-in person's own profile.
- **`toast(title, icon)`** *(parameters: `title`, the message text; `icon`, defaulting to `'success'`)* — shows the small notification box that slides in from the top-right corner and disappears on its own after about two seconds.
- **`log(action, u)`** *(parameters: `action`, a short description of what happened; `u`, the user doing it, defaulting to whoever is logged in)* — writes one line into the database's activity log, which is what powers the Admin's "Activity log" panel.

### Calculated values (the "DERIVED" section)
These functions don't store anything — they just do quick maths on data the app already has.

- **`payment(u)`** *(parameter: `u`, a user)* — checks whether that user has a `paidAt` date on their profile. If yes, returns that date; if not, returns nothing (meaning: hasn't paid).
- **`prog(u)`** *(parameter: `u`)* — works out a student's coursework progress: it counts how many of their course's materials and tasks they've finished, out of the total available, and turns that into a percentage.
- **`grades(u)`** *(parameter: `u`)* — looks at every task submission that student has made, counts how many have been graded, and calculates their average score.
- **`attRate(u)`** *(parameter: `u`)* — calculates what percentage of the days they were marked, the student was marked "present."
- **`eligibility(u)`** *(parameter: `u`)* — the big combined check. A student only qualifies for a certificate if **all four** of these are true at once: they've paid, their coursework is 100% complete, at least 180 days have passed since paying, and every graded task averages at least 50/100. It returns an object saying whether they qualify (`ok`), how many days have passed, and the progress details.

### Login and signup (the "AUTH" section)
- **`A`** — a small object remembering two things while the login popup is open: whether the person is in "signup" or "login" mode, and which role (student/tutor/admin) is currently selected.
- **`adminFull`** — true/false flag tracking whether the two admin slots are already taken.
- **`openAuth(mode)`** *(parameter: `mode`, either `'login'` or `'signup'`)* — opens the popup, and quietly checks the database to see if admin signups should be hidden because the limit is already reached.
- **`closeAuth()`** — hides the popup.
- **`setRole(r)`** *(parameter: `r`, the chosen role)* — updates which role is selected when someone clicks Student/Tutor/Admin.
- **`syncAuth()`** — redraws the whole popup's text and visible fields to match the current mode and role. For example, it hides the course dropdown entirely if "Admin" is selected (admins don't belong to a course), and shows the admin code box only when needed.
- **`aerr(m)`** *(parameter: `m`, the error text)* — displays a message in red inside the popup, and also pops a small red toast.
- **`auth()`** — the main function that runs when the main submit button is clicked. Step by step: it checks the email looks valid and the password is at least 6 characters; if in "login" mode, it tries to sign the person in and greets them; if in "signup" mode, it checks a name was entered, a course was picked (unless admin), and — if signing up as admin — that the secret code was typed correctly. It then creates the account with Firebase, builds a new profile object (with a randomly generated ID like `CLS-123456` for a student, `CLM-...` for tutor, `CLA-...` for admin), and saves it. If the role is "admin," this save happens inside a special all-or-nothing `runTransaction` that also increases the admin counter by one — guaranteeing two people can never both grab the last admin slot at the same moment. If anything goes wrong partway through, the half-created login is deleted so nobody is left stuck.
- **`googleProvider`** — Firebase's built-in "sign in with Google" tool.
- **`googleAuth()`** — handles the "Continue with Google" button. If the Google account already has a CodeLab profile, it just logs them in. If not, and they're signing up, it creates a new profile the same way `auth()` does, using the name and photo Google provides (or guessing a name from their email if Google doesn't share one).
- **`logout()`** — asks "Are you sure?" first, then logs the person out, clears the app's memory back to a blank slate, and shows a confirmation toast.

### Keeping the screen live and up to date (the "DATA" section)
- **`watch(name, q)`** *(parameters: `name`, which part of `db` to update; `q`, the database query to watch)* — tells Firestore "keep me updated." Whenever matching data changes in the database — anywhere, by anyone — this function automatically re-fetches it and redraws the screen, without the person needing to refresh the page.
- **`startWatch(u)`** *(parameter: `u`, the logged-in user)* — decides exactly *which* pieces of data to start watching, based on the person's role. An admin watches absolutely everything across the whole app. A tutor only watches their own course's materials, tasks, students, and attendance. A student only watches their own course's content plus their own submissions and attendance.
- **`onAuthStateChanged(...)`** — Firebase's built-in "tell me the moment someone logs in or out" listener. This is the master switch: whenever it fires, old subscriptions are cancelled, the memory is reset, and (if someone is logged in) their profile is loaded and watching begins.

### Things a person can click (the "ACTIONS" section)
- **`profile()`** — opens a popup letting the logged-in person change their display name and/or upload a new profile picture.
- **`pay()`** — opens the Paystack payment popup for the course fee. On successful payment, it records the payment, stamps the profile with today's date as `paidAt` (which unlocks course content), and shows a success message.
- **`complete(id)`** *(parameter: `id`, a material's unique ID)* — marks one piece of course material as done for the currently logged-in student.
- **`submitTask(id)`** *(parameter: `id`, a task's unique ID)* — opens a popup asking the student to write a description (minimum 20 characters) and either paste a link or upload a screenshot as proof they attempted the task, then saves that submission.
- **`viewProof(id)`** *(parameter: `id`, a submission's ID)* — opens a popup showing the full details of one submission: who submitted it, when, their written description, any score/feedback already given, the link (if any), and the screenshot (if any).
- **`addItem(kind)`** *(parameter: `kind`, either `'m'` for material or `'t'` for task)* — reads whichever form (material or task) is currently filled in, saves it to the database under the tutor's course, and clears the form fields afterward.

### Running the school (the "SCHOOL MANAGEMENT" section)
- **`attDate`** — a variable remembering which calendar date the attendance list is currently showing/editing, starting as today's date.
- **`setAttDate(v)`** *(parameter: `v`, the new date)* — changes that date and forces the screen to redraw.
- **`resetPw()`** — sends a password-reset email to whatever address is typed in the login box.
- **`postAnn()`** — reads the announcement title and message boxes and saves a new announcement, either to everyone (if posted by an admin and "Everyone" is picked) or to one specific course.
- **`postClass()`** — saves a newly scheduled live class, with its topic, date/time, and meeting link.
- **`markAtt(sid, status)`** *(parameters: `sid`, a student's ID; `status`, `'present'` or `'absent'`)* — records one attendance entry for one student on the currently selected date.
- **`grade(id)`** *(parameter: `id`, a submission's ID)* — opens a popup for a tutor to type a score (0–100) and written feedback, then saves it onto that submission.
- **`toggleActive(id, val)`** *(parameters: `id`, a user's ID; `val`, `true` to reactivate or `false` to suspend)* — an admin-only action that locks or unlocks a user's account. If suspending, it asks for confirmation first.
- **`exportCSV()`** — builds a spreadsheet-style file listing every student's progress, attendance, grades, and certification status, then triggers a download of it.
- **`annList()`, `clsList()`, `annForm(admin)`, `classForm()`, `attCard(studs)`, `schoolPanel(u)`** — these don't change any data. They just *build the visual HTML* for, respectively: the announcements list, the upcoming classes list, the "post an announcement" form, the "schedule a class" form, the attendance-taking table, and the combined panel showing announcements plus upcoming classes plus a quick summary of attendance/grade average.

### Extra features
- **`sendMsg()`** — reads the chat input box and saves a new message to the course's discussion board.
- **`chatCard()`** — builds the visual HTML for that discussion board: the last 30 messages, plus the input box and send button.
- **`delItem(kind, id)`** *(parameters: `kind`, `'m'` or `'t'`; `id`, the item's ID)* — lets a tutor delete one of their own uploaded materials or tasks, after confirming.
- **`myUploads(u)`** *(parameter: `u`, the tutor)* — builds a list of everything that tutor has personally uploaded, each with a delete button.
- **`verifyCert()`** — opens a popup asking for a certificate ID, looks it up in the database, and reports whether it's a genuine certificate (and whose, and from when) or "not found."
- **`dueTag(t, s)`** *(parameters: `t`, a task; `s`, that student's submission for it, if any)* — builds the small "Due [date]" label under a task, turning red and adding "overdue" if the due date has passed and nothing's been submitted yet.

### Certificates
- **`certHtml(u)`** *(parameter: `u`, the student)* — builds the visual design of the certificate itself: the student's name, their course, the issue date, and a unique certificate ID, inside a decorative bordered box.
- **`showCert(first)`** *(parameter: `first`, true/false)* — displays that certificate in a popup. When `first` is true, it adds a "🎉 Congratulations!" message above it — this is the one-time celebration shown the moment someone first earns their certificate. When `first` is false, it's just a plain "here's your certificate again" view.
- **`checkCert(u)`** *(parameter: `u`, the logged-in student)* — runs automatically every time the student's dashboard loads. If they don't already have a certificate, and the `eligibility()` check passes, it generates a new certificate record, saves it (both onto the student's own profile and into a separate public `certs` collection so `verifyCert()` can look it up later), and triggers the celebration popup.

### Building what's on screen (the "VIEWS" section)
- **`badge(ok, y, n)`** *(parameters: `ok`, true/false; `y`, text to show if true; `n`, text to show if false)* — builds a small coloured pill label, green for a "yes" state and red for a "no" state (e.g. "Paid" vs "Not paid").
- **`bar(p, c)`** *(parameters: `p`, a percentage; `c`, a colour class)* — builds a horizontal progress bar filled to that percentage.
- **`navHtml(u)`** *(parameter: `u`, the logged-in user, or nothing if logged out)* — builds the content of the top navigation bar: the logo always shows, plus either Login/Sign-up buttons, or the person's avatar, name, ID, and a Logout button.
- **`landing()`** — builds the homepage content shown to anyone not logged in: the big headline, the "Start Learning" button, the "verify a certificate" button, and a short "what you get" summary with the price.
- **`studentV(u)`** *(parameter: `u`, the student)* — builds the entire student dashboard: their profile header, a "Paid"/"Not paid" badge, and — if they've paid — their coursework progress bar, their time-towards-certification bar (or a "view certificate" button if they already have one), announcements, upcoming classes, the course discussion chat, their list of tasks (with submit/view-proof buttons), and their list of materials (with mark-complete buttons).
- **`tutorV(u)`** *(parameter: `u`, the tutor)* — builds the tutor dashboard: forms to upload materials and assign tasks, a form to post announcements, a form to schedule classes, a list of their own uploads, a table of their students with payment/progress info, the attendance-taking tool, the discussion chat, and a list of student submissions with buttons to view or grade each one.
- **`adminV(u)`** *(parameter: `u`, the admin)* — builds the admin dashboard: six summary stat tiles (student count, tutor count, paid students, total revenue, submission count, certified count), a form to post announcements to everyone or one course, a CSV export button, a searchable table of absolutely every user with a suspend/activate button next to each, and the full activity log.
- **`render(force)`** *(parameter: `force`, optional true/false)* — the master function that decides what to actually put on screen, called every single time anything changes anywhere in the app. It rebuilds the navigation bar, then checks: nobody logged in → show the landing page; logged in but profile still loading → show "Loading…"; account suspended → show the suspended message; otherwise show whichever of the three dashboards (admin/tutor/student) matches that person's role. The `force` parameter exists so that, normally, the screen *won't* redraw while someone is actively typing in a text box (which would be annoying and interrupt them) — but certain actions need to force a redraw anyway (like after sending a chat message), and `force=true` allows that.

### The very last few lines
- **`window.addEventListener('unhandledrejection', ...)`** — a safety net. If anything anywhere in the app fails unexpectedly in a way nothing else caught, this catches it and shows a generic "Something went wrong" popup instead of the app silently breaking.
- **`Object.assign(window, {...})`** — this is a technical necessity: because the code is written as a "module" (remember `<script type="module">`), none of its functions are normally reachable from plain HTML `onclick="..."` attributes. This line explicitly exposes every function that an `onclick` needs to call (openAuth, closeAuth, setRole, auth, and so on) by attaching them to `window`, the browser's global object.
- **`render();`** — the very final line. It runs once immediately when the page first loads, drawing the initial screen (almost always the landing page, since nobody's logged in yet at that point).

---

## QUICK-REFERENCE TABLES

### Every HTML element with an `id`, and what it's for

| ID | Purpose |
|---|---|
| `offlineOverlay` | Full-screen "no internet" cover |
| `nav` | The top navigation bar (filled in by JavaScript) |
| `main` | The main content area (filled in by JavaScript) |
| `authModal` | The login/signup popup box |
| `mTitle` | The popup's heading text |
| `roleRow` | The Student/Tutor/Admin toggle buttons |
| `a_name` | Name input (signup) |
| `a_email` | Email input |
| `a_pw` | Password input |
| `a_course` | Course dropdown |
| `a_code` | Admin access code input |
| `a_btn` | Main submit button |
| `a_err` | Error message line |
| `a_sw` | "Switch to login/signup" link |

Beyond these fixed ones, many more IDs (`m_t`, `t_t`, `n_t`, `c_t`, `msg_t`, `g_s`, `sp_n`, etc.) are created *temporarily* inside popups and forms as the dashboards are built, used only while that specific form is on screen, then discarded when it's replaced.

### Every custom CSS class

| Class | Used for |
|---|---|
| `.inp` | Every text box, textarea, and dropdown |
| `.card` | Rounded bordered panels |
| `.btn` | Primary (filled, purple) buttons |
| `.btn2` | Secondary (outlined) buttons |
| `.swal2-popup`, `.swal2-html-container`, `.swal2-title` | Dark-theme overrides for SweetAlert2 popups |
| `#offlineOverlay`, `.show`, `.dot` | The offline cover and its animations |

(All other classes you'll see in the HTML, like `flex`, `text-2xl`, `rounded-full`, come from Tailwind directly and aren't custom.)

### Every JavaScript function, with its parameters

| Function | Parameters | What it does |
|---|---|---|
| `setOffline` | `isOffline` | Shows/hides the offline cover |
| `img` | `file`, `max` | Shrinks and compresses an uploaded image |
| `av` | `u`, `s` | Builds an avatar image or initial-letter circle |
| `uref` | — | Shortcut to the logged-in user's own document |
| `toast` | `title`, `icon` | Shows a small auto-disappearing notification |
| `log` | `action`, `u` | Records one line in the activity log |
| `payment` | `u` | Checks if a user has paid |
| `prog` | `u` | Calculates coursework progress % |
| `grades` | `u` | Calculates average graded score |
| `attRate` | `u` | Calculates attendance % |
| `eligibility` | `u` | Checks all certificate requirements at once |
| `openAuth` | `mode` | Opens the login/signup popup |
| `closeAuth` | — | Hides the popup |
| `setRole` | `r` | Changes the selected role in the popup |
| `syncAuth` | — | Redraws the popup to match current mode/role |
| `aerr` | `m` | Shows a popup error message |
| `auth` | — | Handles email/password login and signup |
| `googleAuth` | — | Handles "Continue with Google" |
| `logout` | — | Logs the person out |
| `watch` | `name`, `q` | Subscribes to live database updates |
| `startWatch` | `u` | Decides which data to watch, by role |
| `profile` | — | Edit name/photo popup |
| `pay` | — | Opens the Paystack payment popup |
| `complete` | `id` | Marks a material as done |
| `submitTask` | `id` | Submits proof of work for a task |
| `viewProof` | `id` | Shows a submission's full details |
| `addItem` | `kind` | Saves a new material or task |
| `setAttDate` | `v` | Changes the attendance date being edited |
| `resetPw` | — | Sends a password reset email |
| `postAnn` | — | Posts a new announcement |
| `postClass` | — | Schedules a new live class |
| `markAtt` | `sid`, `status` | Marks one student present/absent |
| `grade` | `id` | Scores and gives feedback on a submission |
| `toggleActive` | `id`, `val` | Suspends or reactivates an account |
| `exportCSV` | — | Downloads a spreadsheet of all students |
| `sendMsg` | — | Posts a chat message |
| `delItem` | `kind`, `id` | Deletes a material or task |
| `verifyCert` | — | Looks up a certificate by ID |
| `showCert` | `first` | Displays the certificate popup |
| `checkCert` | `u` | Checks and grants certification automatically |
| `render` | `force` | Redraws whatever's currently on screen |

### The database "collections" (tables) this app reads and writes

| Collection | Holds |
|---|---|
| `users` | Every account: name, role, course, payment, progress, certificate |
| `meta/admins` | A single counter tracking how many admin accounts exist |
| `materials` | Uploaded lesson materials |
| `tasks` | Assigned tasks |
| `subs` | Task submissions (with scores/feedback once graded) |
| `payments` | Payment records |
| `log` | The activity log admins see |
| `announcements` | Posted announcements |
| `classes` | Scheduled live classes |
| `attendance` | Daily present/absent records |
| `messages` | The course discussion chat |
| `certs` | Issued certificates, used for public verification |

---

*End of walkthrough. If any single function or section above still isn't clear, point to its name and I'll expand on exactly that one piece in even more detail.*

</body>
</html>