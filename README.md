# Lab 9 — Wheelhouse: Intake Photos and the Diagnosis

**Monday, October 5** · Covers Class 9 · Learning Outcome 3

## Objective

A repair in Wheelhouse carries the photos taken when the bike came in and the diagnosis the mechanic
wrote, as formatted text. The photos are uploaded from the repair's form, validated by type and by
size, and shown as thumbnails wherever repairs are listed. The diagnosis is written in a rich-text
editor and printed through the filter of Action Text. Every page that shows photos or a diagnosis
runs a fixed number of queries.

## The deliverable

**The same repository.** `webtech-wheelhouse`, with this lab's commits on top of the Lab 8 history.

**Canvas.** Submit the URL of your repository. This lab is **individual**.

---

## Requirements

### 1. Setup

- Active Storage and Action Text are installed, each with the migration its installer writes. The
  migrations are committed and up, and `db/schema.rb` shows their tables.
- The `repairs` table gets **no new column**, for the photos or for the diagnosis.
- In development, the files are stored in the `storage/` folder of the project. **No uploaded file
  is committed.**

### 2. Intake photos

- **A repair has any number of intake photos**, under one name declared in the model.
- The repair's form has a file field that accepts **several files in one submission**. The field
  follows the rules of every other field of the form: a label, Bootstrap's classes, and its name in
  the permitted list.
- The repair's page shows every photo of the repair. A repair with no photos says so.
- Each photo on the repair's page **links to the file in full size**.
- **Every image has an `alt`** that names the bike and the repair. A filename is not an `alt`.

### 3. Editing a repair that has photos

- Photos chosen in the edit form are **added** to the photos the repair has.
- Saving the edit form **without choosing a file** leaves the photos as they were. Changing only the
  state of a repair from its form does not change its photos.
- **Each photo can be removed on its own**, from the repair's page or from its edit page. The
  removal asks for confirmation, is not a `GET`, and leaves the other photos in place. It uses no
  JavaScript of your own.
- After a removal, neither table of Active Storage has a row for that file.
- Destroying a repair leaves no row behind that points to it, in the tables of Active Storage or in
  the table of Action Text.

### 4. Validating the upload

- **Only images are accepted.** Decide which types the phones of the shop produce; JPEG and PNG are
  on the list. A PDF, a video or an SVG is refused.
- **A photo over the size limit is refused.** The limit is your decision, and the error message
  states it.
- Both rules are validations in the model, and their messages reach the form in `errors`. The
  `accept` attribute of the field narrows the file picker and does not replace them: a request that
  sends another type without the picker is refused as well.
- A refused upload ends as any refused write: status `422`, the form on the page, the message under
  the file field. The message names the file that was refused.
- **After a refused upload, the repair has exactly the photos it had before.** None of the files of
  the refused request is attached, including the valid ones sent with it.
- The browser empties a file field when a form is shown again, so the photos are chosen again after
  a refused write. The other fields keep their values.

### 5. Thumbnails

- **No list downloads an original.** With photos of several megabytes attached, each image of the
  repairs index weighs tens of kilobytes at most. The Network tab of DevTools shows the size.
- The model declares **two named variants**: a small one for the lists and a larger one for the
  repair's page. The dimensions of a variant are written in the model and nowhere else.
- The repair's page shows the larger variant of each photo. The original is downloaded only when a
  photo is clicked.
- **Each row of a list of repairs shows the thumbnail of one intake photo.** The row is the partial
  that every list of repairs renders, so the thumbnail is on the repairs index and on the bike's
  page.
- The thumbnails of a list **all measure the same**, whatever the proportions of the original, and
  none is stretched.
- A repair with no photos keeps its row aligned with the others.

### 6. The diagnosis

- **A repair has a diagnosis, as rich text**, declared in the model.
- The repair's form shows the rich-text editor for it, with a label. The toolbar of the editor is
  styled: its buttons show their icons, in a row.
- The repair's page prints the diagnosis **with its formatting**: bold, lists, links and quotes look
  as they do in the editor. A repair with no diagnosis says so.
- The diagnosis is optional. A repair is saved with or without it.
- Each row of a list of repairs shows **the beginning of the diagnosis as plain text**, on one line,
  cut at a length you choose. No tag and no HTML entity is visible in it.
- If your schema has a column on `repairs` that holds the diagnosis, the rich text replaces it: a
  migration copies the text of every row to the new table and removes the column.

### 7. HTML written by a user

- **The diagnosis reaches the page through Action Text and nothing else.** `raw`, `html_safe` and
  `sanitize` with a list of your own appear nowhere in the application.
- A diagnosis that contains a `<script>` tag, or an `<img>` with an `onerror` attribute, sent
  without the editor, is saved. The repair's page and the lists show it, and the code does not run.
- Every other string a person typed (a name, a serial number, the name of a service) is printed
  escaped.

### 8. Queries

- **Every page that shows photos or a diagnosis runs a fixed number of queries**, whatever the
  number of repairs and the number of photos of each one. Doubling the seed does not change the
  count.
  - The repairs index, with a thumbnail and the beginning of the diagnosis on each row.
  - The bike's page, with the same row for each of its repairs.
  - The repair's page, with all its photos.
- The preloading goes in the controller, with the scopes that Active Storage and Action Text define.
  The views do not change for it.

### 9. Seed data

- **At least ten repairs have intake photos.** At least one has four or more, and at least two have
  none.
- The image files of the seed are **committed in `db/seeds/`**. They are photos of bicycles that you
  took or that you are allowed to use. The same file can be attached to more than one repair.
- **At least ten repairs have a diagnosis with formatting**: bold text and a list, at least. A
  repair that has just been taken in has none.
- From an empty database, `bin/rails db:seed` finishes without errors, and every page shows its
  photos afterwards.
- Running the seed a **second time** leaves the same number of rows in every table as the first run,
  including the tables of Active Storage and Action Text.

### 10. The README

The README is graded on one criterion: **someone who has never seen your repository can clone it
and get it running by following only the README.**

- The prerequisites name libvips and say how to install it.
- Following the setup commands on a fresh clone ends with an application whose repairs index shows
  thumbnails.

---

## Documentation

- Rails Guides — [Active Storage Overview](https://guides.rubyonrails.org/active_storage_overview.html):
  the requirements and the setup, `has_many_attached`,
  [Replacing vs Adding Attachments](https://guides.rubyonrails.org/active_storage_overview.html#replacing-vs-adding-attachments),
  [Removing Files](https://guides.rubyonrails.org/active_storage_overview.html#removing-files),
  [Transforming Images](https://guides.rubyonrails.org/active_storage_overview.html#transforming-images)
  and the section on querying
- Rails Guides — [Action Text Overview](https://guides.rubyonrails.org/action_text_overview.html):
  the installation, creating and rendering rich text, the styles of Trix, and
  [Avoiding N+1 Queries](https://guides.rubyonrails.org/action_text_overview.html#avoiding-n-1-queries)
- Rails API — [`ActiveStorage::Attached::Model`](https://api.rubyonrails.org/classes/ActiveStorage/Attached/Model.html)
  and [`ActiveStorage::Attached::Many`](https://api.rubyonrails.org/classes/ActiveStorage/Attached/Many.html)
- Rails API — [`ActionText::Content`](https://api.rubyonrails.org/classes/ActionText/Content.html),
  for the plain-text version of a rich text
- `image_processing` — [the libvips operations](https://github.com/janko/image_processing/blob/master/doc/vips.md),
  the resizing methods a variant accepts
- libvips — [Install](https://www.libvips.org/install.html)
- Rails Guides — [Securing Rails Applications](https://guides.rubyonrails.org/security.html#cross-site-scripting-xss),
  the section on cross-site scripting
- Bootstrap — [Images](https://getbootstrap.com/docs/5.3/content/images/) and
  [Figures](https://getbootstrap.com/docs/5.3/content/figures/)

## Deadline

**Tuesday, October 6, 20:00**, on Canvas.
