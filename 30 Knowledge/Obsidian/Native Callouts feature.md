---
title: Native Callouts feature
tags:
  - Obsidian
  - Callout
---
# Native Callouts feature

The closest built-in option uses the `[!note]` syntax, which provides the matching document icon.

```markdown
> [!note] Environment variables are NOT secure for sensitive data:
```

## 🎨 How to customize the colors to match exactly

By default, the `[!note]` callout will use your theme's default color (usually blue or gray). If you want to make it look exactly like your image with a **purple background and red text**, you can use inline HTML styling right inside your markdown:

```markdown
> [!note] <span style="color: #e03131; font-weight: bold;">Environment variables are NOT secure for sensitive data:</span>
```

---
## 🛠️ For a permanent, exact visual match (CSS Snippet)

If you want to create a custom callout type (e.g., `[!warning-purple]`) that looks identical to your image every time you use it without typing HTML, you can add a CSS snippet to Obsidian:
1. Go to **Settings > Appearance**.
2. Scroll down to **CSS snippets** and click the folder icon to open the snippets folder.
3. Create a new text file named `custom-callouts.css` and paste the following code:
	```
	.callout[data-callout="secure-warn"] {
	--callout-color: 147, 130, 235; /* Lavender/Purple background RGB */
	--callout-icon: lucide-file-text;
	color: #e03131; /* Red text color */
	font-weight: 600;
}
	```

4. Save the file, go back to Obsidian settings, and click the refresh button next to CSS snippets to **toggle it on**.


```markdown
> [!secure-warn] Environment variables are NOT secure for sensitive data:
```

