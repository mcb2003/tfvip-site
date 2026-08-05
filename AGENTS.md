I’m redesigning my personal website (in ./tf-vip, and at https://thefake.vip).

The current site is built with Hugo and my own theme, Elemental (in ./elemental), which currently uses Bootstrap 5 underneath. I do not want to throw all of that away or turn the site into a frontend-framework project. The whole point of the site is that it stays simple, durable, inspectable, and mostly static.

The source lives in a public GitHub repo and is deployed through GitHub Actions to GitHub Pages. The build/deployment flow is intentionally boring: I edit plain files, commit, push, Hugo builds the site, and the generated output gets published. I like that model and want to preserve it.

The motivation for redesigning the site is partly professional. I’ve just graduated with a first-class BSc (Hons) in Computing Science and I’m about to start looking for my first programming job. I need a new CV, and rather than maintain separate hand-written web and print versions I want the important structured CV information in a Hugo data file, probably YAML, with separate templates for web and print/PDF-style output.

But I don’t want the site to become “CV with navigation”. It is still my personal website, and I want it to represent how I actually think, build things, learn, and explore technical ideas.

The current site is badly out of date. The homepage is largely unchanged from when I was about 16, the blog is almost empty, and the projects page no longer reflects what I actually do. I start a lot of projects, often explore them deeply, and am much better at starting interesting things than formally “finishing” them. I don’t want a portfolio that pretends every experiment is a polished shipped product.

A central idea that came out of brainstorming is to stop treating the site as a trophy cabinet and instead make it more like a public engineering postbook / working memory.

The broad content structure I currently like is something along these lines:

* Home
* Work
* Writing

  * Posts
  * Articles
* About
* CV
* Possibly a dedicated Accessibility page
* Possibly a Lab / systems-infrastructure section
* Possibly a /now page

The exact navigation is not fixed.

The most important new content concept is short-form “posts”.

I realised that microblog-style content probably fits how I naturally think much better than trying to force myself to write long polished blog posts all the time. I spend my days sending short nerdy Discord messages to friends: observations, design thoughts, implementation discoveries, technical complaints, API quirks, mini-rants, links with commentary, half-developed architectural ideas, and things I just learned. A lot of those thoughts would be useful or interesting to other people, and at minimum they demonstrate how I think.

So I want posts to be a first-class part of the site, not a lesser form of blogging.

A post may be:

* two sentences
* a few paragraphs
* a code snippet
* a link with commentary
* a technical discovery
* a project update
* an unresolved design thought
* a reply to something somebody else wrote
* something personal/technical without being diary-like

A post should not need a title, hero image, summary, elaborate metadata, or the usual blog-post ceremony. In particular, author information doesn't make sense for a personal site.

The conceptual hierarchy I like is:

posts -> Articles -> Project / Work case studies

with increasing levels of polish and permanence.

posts should be allowed to evolve. I am happy for a post to be edited later and to say things like “Updated: I was wrong about this because…”. I do not want to inherit the artificial immutability of Twitter-style posts just because the content is short.

I also like the idea of threads.

A post may follow up on or reply to another post. I want the site to understand the difference between:

* belonging to the same broader thread/topic
* directly replying to another post

Those relationships should ideally live in source metadata rather than being inferred from links.

For example, conceptually:

thread: accesskit-tree-design
reply_to: /posts/...

Threads could become generated pages that show the full sequence chronologically.

Longer articles could also explicitly record that they grew out of earlier posts, and the earlier posts could link to the later synthesis article. I like the idea that visitors can see both the clean finished explanation and the messy path that led to it.

Hashtags/tags are also important.

I want normal semantic tags that Hugo can understand as taxonomies, so things like accessibility, C++, RmlUi, SQLite, IndieWeb, etc. can generate useful archive pages and feeds.

I would also like visible #hashtags in short-form posts, ideally inline in the prose if that can be made sensible. I am not committed to automatically parsing arbitrary #words from the final HTML. We discussed that Hugo’s taxonomy should probably remain canonical in metadata, while inline hashtags are presentation/authoring sugar. A simple robust approach could be better than clever regex post-processing.

Another major idea is “My Work”.

I do not want a flat Projects page that implies every item is a finished product.

A project/work item should probably be a real Hugo content page with structured front matter plus a richer Markdown/Org body.

Possible statuses/categories include:

* released / shipped
* active
* experimental / exploration
* paused
* archived
* superseded

The exact vocabulary is not fixed.

A work page can then dynamically group items by status instead of me maintaining several lists manually.

I like the idea that a project page is an aggregation point:

* overview/case study
* links to source/docs
* status and metadata
* recent posts associated with that project
* related articles
* perhaps diagrams/screenshots/etc.

A project update should probably be a post associated with a project, not a separate custom changelog system.

Note however that I'm not looking to replace docs in a git repo, GitHub issues or wikis with my personal site.

Some projects/areas that are likely relevant to the redesigned site include:

* RmlUi-Accessibility, which is probably my strongest flagship project
* LEGE / accessible game work
* NO CARRIER
* Workspace, a large SQLite-based native productivity/database app concept
* accessibility work generally
* Linux/NixOS/sysadmin/lab infrastructure
* various systems-programming explorations

The Work area should be honest about incomplete things. An unfinished but deeply explored technical idea can still be worth showing if it demonstrates architecture, judgement, research, or implementation work.

I also want the homepage to be largely composed automatically from the content graph rather than manually kept current.

Conceptually it might contain:

* a concise current introduction
* featured work
* recent posts
* latest or selected article(s)
* maybe current /now information
* contact / identity information

That way the homepage stays alive as I publish new material.

I want the site to have an IndieWeb-ish / POSSE flavour while still remaining fundamentally static.

The philosophical model is:

My site / Git repo is the canonical source of truth.

Other networks and feed formats are projections or syndication targets.

I do not want Mastodon or another platform to become the actual place my writing “lives”.

I use Mastodon as my preferred social network, and I want to syndicate posts there automatically.

The workflow I have in mind is roughly:

* create/edit post locally
* commit
* push
* GitHub Actions builds site
* GitHub Actions also syndicates new posts to Mastodon

The Mastodon syndicator should ideally support actual threaded replies. If post B replies to post A on my site, and post A has already been syndicated to Mastodon, post B should be posted with Mastodon’s in_reply_to_id pointing at the earlier status.

The important conceptual decision is that reply relationships should refer to my own content model, not Mastodon IDs directly. Mastodon-specific IDs are just adapter state.

There needs to be durable mapping from a post to its Mastodon status ID/URL. One idea we liked is having the Action write that syndication metadata back into the post front matter and commit it, so the Git repo remains the database of syndication state.

The syndicator should be idempotent and should reconcile “published posts which requested Mastodon syndication but have no Mastodon ID yet”, rather than relying only on “files changed in this git push”. That way failures recover naturally on later runs.

We also discussed edits:

* first publication should syndicate automatically
* later edits to the canonical post should not necessarily auto-edit Mastodon every time
* Mastodon edits could be explicit if wanted
* the website remains canonical even if a syndicated copy becomes stale

I want the local authoring workflow to be extremely low friction.

The idea is to use a Justfile or Makefile, perhaps eventually augmented by Emacs commands, so that:

just post

creates a new post from an archetype, opens the editor, and automatically creates a commit.

Similarly, something like:

just reply <post>

could create a post with reply metadata already populated.

I use Emacs, so Org-mode became another important part of the discussion.

Hugo has native Org content support. I am considering making posts, and possibly Articles and Work pages, native .org files rather than Markdown.

That is appealing because I can then use org and Emacs's rich features to create and edit content. Even my recently completed dissertation was written in org-mode.

A post could conceptually look like:

```org
#+DATE: ...
#+TAGS[]: accessibility rmlui
#+THREADS[]: accesskit-tree-design
#+PROJECT: rmlui-accessibility
#+REPLY_TO: ...

Actual Org content here...
```

The exact front matter syntax may need checking against Hugo’s current Org handling, but the important idea is that Org source should still integrate with Hugo’s normal Page Params, taxonomies, sections, and collections.

I do not currently want the classic “one giant Org file + ox-hugo exports generated Markdown” model unless native Hugo Org proves inadequate.

I prefer:
one source file == one Hugo page

because that works better with Git history, page identity, threads, publication state, syndication, and Actions.

Org-mode also fits very nicely with Org Social.

I want to participate in the Org Social network by generating a /social.org file from Hugo.

This should be a generated output projection, not the editable canonical Org Social file expected by some Emacs workflows.

The conceptual structure is:

canonical Hugo post
-> HTML
-> RSS
-> h-feed / Microformats
-> social.org
-> Mastodon syndication

If the source post itself is Org, Hugo’s .RawContent looks like a very attractive way to emit the post body into social.org, because the body is already valid Org.

The Org Social template could then add protocol-specific metadata such as timestamps, reply properties, tags, follows, etc., around the raw body.

This also means the source Org file and generated social.org are deliberately different things:

* source Org may contain Hugo-specific metadata
* social.org contains exactly the Org Social representation

I want Org Social reply relationships to come from the same generic reply metadata used by the website and Mastodon adapter where possible.

For external Org Social replies, the source may need protocol-specific external target metadata. For replies to my own posts, the social.org target can be derived from my canonical post identity and publication timestamp.

I also want proper RSS feeds: Hugo already does this well.

I also want normal IndieWeb Microformats in the HTML:

* rel="me" on links to my socials
* h-card for me
* h-entry for posts/Articles
* h-feed for the posts listing
* u-url
* dt-published
* e-content
* u-in-reply-to where appropriate
* u-syndication links to Mastodon copies

I do not want a JS-heavy frontend framework for this. These should be semantic HTML features added to normal server-generated pages.

Webmentions are another important part of the design.

Sending Webmentions fits perfectly into GitHub Actions:

* after the site is deployed
* inspect external links from newly published/changed pages
* discover Webmention endpoints
* send source/target POSTs

This should probably live in a small explicit publication/syndication tool that I can run with `just` locally, during development, testing, or before I write the GitHub Action.

Receiving Webmentions does require writable infrastructure, so for now I plan to use webmention.io.

I specifically like a progressive-enhancement model for rendering received Webmentions:

At build time:

* curl/download webmention.io data into something like data/webmentions.json
* commit/version that snapshot in the repo
* Hugo renders those mentions into static HTML

(this workflow can again be implemented in the Justfile)

At runtime, if JavaScript is enabled:

* a small vanilla JS module calls webmention.io’s public API
* gets the freshest mentions
* replaces the statically rendered mention list

If JavaScript is disabled:

* the viewer sees the snapshot from the last static site build

If webmention.io is down or the JS fails:

* the static copy remains visible

I like this because client-side JS is being used as lightweight progressive enhancement, not as the thing that owns the page.

I am not ideologically anti-JavaScript. I am anti unnecessary complexity and anti making basic content depend on JavaScript.

The Webmention rendering itself should be very simple and semantic:

* replies with author/content
* mentions/reposts as compact links
* likes perhaps as names/avatars
* avoid blindly injecting untrusted content.html
* use text content or proper sanitisation

The data/webmentions.json snapshot is intentionally part of the Git repo because I like external interactions being versioned with the site.

Later I might replace webmention.io with a tiny Cloudflare Worker + D1 receiver, but that is not necessary now.

We also discussed IndieAuth.

For now I could use a hosted IndieAuth service so https://thefake.vip/ is my IndieWeb identity.

The site can advertise the appropriate IndieAuth endpoint/metadata and use rel=me/h-card identity links.

Cloudflare also came up as possible future infrastructure.

The site does not need to move away from GitHub Pages now, but if I eventually need tiny dynamic services, I could:

* keep the site itself static
* put Webmention endpoints on Cloudflare Workers
* or later move static hosting to Cloudflare Pages if that becomes cleaner

The key architectural principle is:
dynamic infrastructure may handle messages about the site or publication/syndication workflows, but serving the site itself should remain boring static files.

Hugo features I want to lean on rather than reimplement include:

* sections for posts, Articles, Work
* taxonomies for tags, and maybe threads
* front matter / Params for metadata like status, project, reply_to, featured
* Page collections and where/filter/group/sort logic
* archetypes for easy post/article/project creation
* Page.Render / view templates for reusable representations of a Page
* partials for generic UI pieces
* custom output formats for social.org and maybe CV print variants
* native RSS output
* data files for CV and Webmentions
* page bundles for work/project pages that have associated resources
* related-content machinery where useful
* Hugo’s native Org-mode support

Since my site uses the ELemental theme, we need to figure out what belongs in the theme, and what belongs in my personal site. We probably want to add more extension points to Elemental to make all of this possible.

A rough source tree discussed was:

content/
_index.org or .md
about.org
now.org
posts/
_index.org
timestamped-post.org
articles/
_index.org
article/index.org
work/
_index.org
rmlui-accessibility/index.org
other-project/index.org

data/
cv.yaml
webmentions.json

archetypes/
posts.*
articles.*
work.*

I want structured Work metadata such as:

* title
* summary
* status
* featured
* topics/tags
* links
* maybe repo/docs/live demo metadata

Tags definitely should be taxonomies.Threads might be a custom taxonomy if that gives clean generated thread archive pages.Project association for posts may just be a front matter parameter, because the actual canonical project page already exists under /work/... and can query posts associated with itself.

The general Hugo design principle I liked from the discussion is:

Anything that is itself a publishable thing should be Hugo content.

Anything that merely describes/configures things should be data/front matter.

So:

* posts -> content
* Articles -> content
* Work/project case studies -> content
* About/Now -> content
* CV structured facts -> data
* received Webmentions -> data
* site config / identity / syndication settings -> config/data/front matter as appropriate

I also want to keep the whole implementation understandable.

I do not want:

* a bespoke CMS
* an SPA
* a database-backed website
* dozens of third-party runtime dependencies
* a giant “post to every social network” service
* clever magic when Hugo’s built-in mechanisms are already good enough

I like small tools with explicit responsibilities:

* Hugo renders
* Git stores
* Actions automates
* Mastodon is a syndication target
* webmention.io is initially a receiver
* small JS progressively enhances freshness
* Org Social/RSS/h-feed are generated representations

The site should feel personal, technical, and slightly nerdy rather than recruiter-polished corporate branding.

The professional goal matters, but I want the site to prove who I am by showing:

* how I reason about engineering
* what I build
* what I explore
* accessibility expertise
* systems/programming interests
* real work in progress
* useful technical observations
* honest project status

The site should make it obvious that I care about small, efficient, understandable, durable software and open standards.

I also want accessibility itself to be treated as domain expertise, not just biography. I am a blind developer and use Orca/NVDA heavily, and accessibility has become a major technical area of my work. That should shape the site and some of its content, but I do not want the homepage to begin with a medical explanation of me.

Likewise, the site itself should demonstrate the values it talks about:

* semantic HTML
* accessible navigation/content
* very little required JavaScript
* good performance
* static-first architecture
* plain source formats
* open standards
* visible interoperability rather than platform lock-in

The main thing I want from further brainstorming is not a complete implementation dumped on me.

I want to keep exploring the architecture and how best to use Hugo’s native machinery for this vision:

* content models
* front matter conventions
* posts/threads/tags
* Work/project relationships
* Org-mode authoring
* social.org generation
* feed architecture
* syndication metadata
* Webmention rendering
* build/publish flow
* IndieWeb semantics
* low-friction local authoring

Please treat this as an ongoing design conversation. Some ideas above are settled preferences, some are tentative, and some are ideas I may later discard. I care more about preserving the overall ethos and finding elegant, boring solutions than about rigidly implementing every brainstormed feature.
