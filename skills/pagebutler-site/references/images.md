# Putting a photo on a page

For a request such as "use this photo for the hero". A photo that a page shows through an image field is an edit to that field, as new words are: upload the file to the site's image library, then set the field to the address the upload answers. An image that belongs to the site's design, such as a logo or an icon, goes in the site's files instead and is deployed with them.

## How the photo gets there
- A file on this computer, where `pb` runs, as in Claude Code or Codex: `pb assets upload <file>` ("Upload").
- A photo online, in any app with Pagebutler's tools: `assets_import` with its link ("From a link"). Where `pb` runs, this spares downloading it first.
- A photo the person attached in ChatGPT: `assets_import`, to which ChatGPT passes the file. If none came (`image_not_given`), ask them to attach it again or for a link.
- A photo attached in another chat, such as claude.ai: a small copy through `assets_upload` ("In a chat").

## Upload
1. In the site's folder, run `pb assets upload <file>` with the photo's path. Where `pb` runs, never read the image into the conversation or pass its base64 to a tool: `pb` sends the file from this computer to Pagebutler as it is.
2. It prints the image's size, then its address on the line starting `src:`. The address starts `/_pb/assets/`; under `--json` it is `src`. Copy it exactly: never shorten it, rename it or add a host.
3. The same file again answers the same image and address, so running the command again is always safe.
4. `--dry` checks the image and prints the address it would have, storing nothing.
5. An upload changes no page: the preview and the live site stay as they are until a page shows it. Nothing deletes an uploaded image yet, so upload only what the person asked for.

## From a link
1. Call `assets_import` with the link as `url`: the image file's own address, starting `https://`. Import only a photo the person may use, such as their own. Pagebutler fetches it once and doesn't keep the link.
2. A link to a page that shows the photo is refused: Pagebutler never looks inside a page. If you can open the page, find the photo's own address there, asking the person when there are several; if not, ask them to open the photo on its own and copy its address.
3. The answer is the upload's: `src`, the size and `changed`, read as above, and a call the app cut off is safe to make again.
4. A refusal's hint says what to ask the person for. Call again unchanged only when it is `retryable`, and only once.

## Set the field
1. In the site's folder, run `pb fields set '<path>' --image '<the address>' --alt '<what the image shows>'`, the path as the page's code names the field, such as `pages.about.hero.photo`. It reads the field first, and refuses a field that isn't an image. With Pagebutler's tools instead, `page_get` gives the field's `path` and `draft.rev`; call `page_update_fields` with that `path`, its `draft.rev` as `if_rev`, and `value: { "src": "<the address>", "alt": "<what the image shows>" }` (`references/tools.md`, "Changing a page's words").
2. Write `alt` in the page's language for someone who can't see the image, such as "Ana behind the bakery's counter", never the file's name or "photo of". Leave it empty, `--alt ''`, only for an image that is decoration.
3. When the photo's shape differs from its place on the page, `pb` says so on a line of its own; the tools' answer says `wider` or `taller` in `shape`. The site's styles decide whether it is cut off, squashed or changes the place's height. Tell the person, and offer to crop a copy to the place's shape and upload that.
4. It is saved, not live. `pb fields set` waits until the preview shows it, then prints the preview's link; with the tools, wait for `preview.status` to read `preview_updated` before you hand over the preview. Publish only when the person asks, as its `Next:` line says (tools: `references/publishing.md`).
5. Set an existing field rather than writing the address into the page's code: a person's earlier edit of the field would hide a change to the code.

## Where an address works
- Pagebutler serves an uploaded image only where a page or a stylesheet names its address. Write it plainly, starting `/_pb/assets/`: as an image field's `src`, in a page's HTML, or in CSS, such as `url(/_pb/assets/...)`.
- Never in a script, a JSON file, `_redirects` or `_headers`, and never escaped, built from parts or relative (`_pb/assets/...`): Pagebutler doesn't see it there, and the image doesn't load.

## Refusals
Follow the hint. The common ones:
- HEIC, as iPhones save photos: make a JPEG, on a Mac with `sips -s format jpeg photo.heic --out photo.jpg`, and upload that.
- SVG, which can carry scripts: make a PNG and upload that, or put the SVG in the site's files and deploy.
- Over 25 MiB, or too large to read: make a copy at most 2,560 pixels on its longer side. Pagebutler scales every image to that size anyway.
- An animated AVIF: make it a WebP or a GIF.
- A file holding base64 text, a `data:` URL or JSON: write the image it holds to a file of its own first.
- The site's image library full: tell the person. Nothing frees room yet.
- `image_not_uploaded`: an image field takes only an image in the site's image library, or one it has shown. Upload the photo first, even a file of the site's own code such as `img/team.jpg`, and copy any other `src` exactly from `page_get`.

## What the person hears
Say "photo" or "image", never "asset" or a code, and say preview or live. Name only the changes the `pb` lines or `changed` name.
- New, when the lines say it was scaled down and its metadata removed: "I added your photo to the site's image library. I removed the hidden details it carried, which can include where it was taken, and made it smaller, 2,560 pixels on its longer side, the most the site keeps. It isn't on any page yet."
- From a link: "I added the photo from that link to the site's image library." Then the changes, and "It isn't on any page yet."
- Colours converted: "Its colours were made for print, so I converted them to screen colours; they may look a little different."
- Already there: "That photo is already in the site's image library, so I'll use the one there."
- Placed: "I've put your photo on the About page. It isn't live yet; the preview will show it in a few seconds. Tell me when you'd like it live."
- Taller than its place: "The new photo is taller than its space on the page, so depending on the site's design its top and bottom may be cut off, it may look squashed, or the space may grow and push what's below it down. I can crop it to fit. It isn't live yet: the preview will show how it looks."
- Wider than its place: the same, with "wider", "its sides may be cut off" and "the space may get shorter".
- HEIC: "That's an iPhone photo in HEIC, which Pagebutler can't read. I'll make a JPEG of it and upload that."
- Too large: "That image is larger than Pagebutler takes. I'll make a smaller copy first: the site shows it at most 2,560 pixels anyway."
- SVG: "Pagebutler doesn't take SVG uploads, since an SVG can run scripts on the site. I can make a PNG of it, or add the SVG to the site's code instead."
- Library full: "The site's image library is full (1,000 images), and uploaded images can't be deleted yet. I can use one of the images already there."
- Busy, after `pb` retried: "Pagebutler was busy and didn't take the photo. I'll try again in a minute."
- A page, not the photo: "That link opens a page, not the photo itself. Could you open the photo on its own, copy its address and send me that?"
- Needs a sign-in, or opens only on their network: "I couldn't get the photo from that link: it needs a sign-in, or opens only on your network. Could you send me a link anyone can open?"
- Nothing there: "There's nothing at that link any more. Could you send me its current link?"
- The other site slow: "The site with the photo didn't answer in time. I'll try once more."
- Attachment missing, in ChatGPT: "Your photo didn't come through to Pagebutler. Could you attach it again, or send me a link to it?"
- No way to pass it: "I can't pass that photo on to Pagebutler from here. If it's online, send me its link, or I can add it from Claude Code or Codex."
- Smaller copy, from a chat: "A chat can only pass small images, so I uploaded a smaller copy of your photo. For the full size, add it with Claude Code or Codex."

## In a chat
Without `pb`, `assets_upload` takes the image as base64 in `image`. Its bytes pass through your own reply, so send only small images, a few tens of kilobytes at most: a larger one costs a long reply and can arrive damaged.
1. Only from the file: with code, read the file the person attached, print its base64 on one line, and pass that unchanged as `image`, with the file's name as `file_name`. Never write base64 yourself, from memory or from how an image looks.
2. A photo: with code, make a smaller copy first, a JPEG, and tell the person it is smaller. An icon or a logo: send a PNG, which Pagebutler refuses when it was damaged on the way. A damaged JPEG can be stored as it arrived, so check the preview shows the image whole.
3. Then set the field, as above, or write the `src` into a new page (`references/chat.md`).
4. Without code that reads the file, it can't reach Pagebutler: say so, as "No way to pass it" above.
