# How To

https://gohugo.io/getting-started/quick-start/#step-2-create-a-new-site


# Neuer Post auf dem Blog
Blogposts sollen fürderhin nicht für die normalen Terminankündigungen verwendet werden, sondern nur noch für Inhaltliches. Der Termin kann auf "Kontakt" gesetzt werden.

```
hugo new content posts/my-first-post.md
```

Dann den Post editieren.

Dann den Server die Seiten (inkl. drafts) bauen lassen:
```
hugo server --buildDrafts
hugo server -D 
```

Syntax, um Bilder einzufügen:
```
{{< figure src="/arduino_small.jpg" alt="Arduino Hardware" position="center" style="border-radius: 8px;" caption="" captionPosition="right" captionStyle="color: black;" >}}
```


# Was ist wo?

- Blogposts in `content/posts/
- Bilder für die Blogposts in static/