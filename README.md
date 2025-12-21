# secureblue.dev

This repo contains the source files of [secureblue's static website](https://secureblue.dev), generated using Jekyll and currently being deployed to Cloudflare Pages. It is a fork of [GrapheneOS's static website](https://github.com/GrapheneOS/grapheneos.org): it borrows pieces of HTML and general ideas while keeping a mostly identical CSS, but secureblue.dev uses a website generation workflow we consider more convenient.

## Content

The actual information present in the website is contained in the `content/` directory in this repository. Due to how Jekyll works, the markdown documents in there aren't processed into HTML pages because they are in that seemingly specific path, but because they contain front matter - a block of metadata at the top of the document, wrapped between triple dashes. The front matter variables documents must have in this project are:

- title: For the tab title text in a browser, or a search engine query.
- description: For search engine queries, shown under the title.
- permalink: Actual URL path of a page. Unrelated to directory structure.

Static files, however, do follow directory structure. Any file that Jekyll isn't instructed to ignore and that doesn't contain front matter is treated as a static file and respects its directory placement in the repository. This is the reason why a few otherwise seemingly random files are at the root of the repository: they are static assets and must be located at the root of the website. The rest is lumped under `assets/` and its subdirectories to keep it tidy.

By default, most files not relevant to the generated website are already excluded by default, and a manual exclusion for this `README.md` file is added in `_config.yaml`.

## Layout

`_layouts/` stores the only layout file that wraps all pages, at the outermost level. It always includes the `meta.html`, `header.html` and `footer.html` fragments, and uses a Liquid expression to include the hero banner only in the index page.

## Includes

`_includes/` stores reusable HTML fragments. What follows are details of fragments worth commenting on.

### alert

`alert.html` is included to wrap text in certain visual highlights, replacing GitHub Markdown alerts like the following:

> [!NOTE]
> An alert of the "note" type. It's pretty cool, right?
>
> Another line in the same alert. Click [here](https://secureblue.dev/faq) to go to the FAQ.

This can be created with the following simple syntax:

```
> [!NOTE]
> An alert of the "note" type. It's pretty cool, right?
> 
> Another line in the same alert. Click [here](https://secureblue.dev/faq) to go to the FAQ.
```

Except that as an inclusible HTML fragment, `alert.html` presents an unhinged manner to imitate the above. It has to be included (implying everything is on a same line) and requires two variables to be passed to it, one indicating the type of alert, and the other being one whole string with all the text that needs to be shown. Newlines are not permitted and have to be broken by including `<br>`, not because it means a line break in HTML, but as an arbitrary token that `alert.html` will scan for and use as a breaking point for newlines. This is necessary since Liquid doesn't support initializing arrays, and that arbitrarily chosen token is scanned to create an array from a whole string, separating elements by each occurrence of the token. Each element of the array will be treated as a line of text in the alert. Also, apostrophes (`'`) have to be escaped with a backwards slash (like so: `\'`). Aditionally, since it is an HTML fragment that is being included, the content passed to it is not processed as markdown. Links have to be written as HTML instead of markdown in alerts, etc...

To demonstrate this, the following syntax in a document in this project produces the same effect of the example earlier:

```
{% include type='note' content='An alert of the "note" type. It\'s pretty cool, right?<br>Another line in the same alert. Click <a href="https://secureblue.dev/faq">here</a> to go to the FAQ.' %}
```

It easily becomes an eyesore with longer strings.

There are 5 types of alerts available: `note`, `tip`, `important`, `warning` and `caution`. Here's how they look and what purpose does each of them serve:

> [!NOTE]  
> Highlights information that users should take into account, even when skimming.

> [!TIP]
> Optional information to help a user be more successful.

> [!IMPORTANT]  
> Crucial information necessary for users to succeed.

> [!WARNING]  
> Critical content demanding immediate user attention due to potential risks.

> [!CAUTION]
> Negative potential consequences of an action.

[Source](https://github.com/orgs/community/discussions/16925).

### header

`header.html` uses Liquid expressions to add `aria-current="page"` to a page's list entry in its header list when this fragment is included in said page, highlighting its text with a blue accent.

### meta

`meta.html` uses variables read from the front matter of any given page it is included in. This is so each page can have its correct metadata without a meta element section of its own, which would be mostly repeated across all pages anyways.
