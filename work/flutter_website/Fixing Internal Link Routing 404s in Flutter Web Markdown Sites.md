# Fixing Internal Link Routing 404s in Flutter Web Markdown Sites

**Tags:** Flutter, Web, Routing, Markdown, Debugging  
**Date:** 2026-02-24

---

## The Setup

I built a Flutter Web app that acts as a lightweight CMS — it fetches a `mapping.md` file from GitHub, parses route → file mappings, fetches the corresponding `.md` files, converts them to HTML, and renders them inside iframes.

Links inside blog posts could be:

- `[[wiki style links]]` (Obsidian-style)
- `[plain markdown links](some/path/file.md)`
- External `https://` URLs

Everything looked fine until I started clicking internal links inside blog posts.

---

## The Bug

Clicking a link inside a rendered blog post would show:

```
404

No route found for /website/content/blogs/windows-low-battery-alert-40-percent/windows-low-battery-alert-40-percent.md

Known routes:
- /
- /portfolio
- /blogs/flutter
- /blogs/windows-low-battery-alert-40-percent
```

The route `/blogs/windows-low-battery-alert-40-percent` existed perfectly in `_routes`. Yet the router was being asked to find `/website/content/blogs/windows-low-battery-alert-40-percent/windows-low-battery-alert-40-percent.md` — a raw file path, not a route.

---

## The Reason

There were **two separate root causes**, both leading to the same symptom.

### Cause 1 — Wiki links with no matching route leaked raw GitHub URLs

The original `_resolveWikiLinks` method handled `[[wiki]]` links. When a wiki link had a matching route, it correctly rewrote it to `[label](/clean/route)`. But when no route matched, it fell back to the raw GitHub URL:

```dart
// OLD code
return route.isNotEmpty
    ? '[$label]($route)'
    : '[$label]($kRepoRaw/$repoPath)';  // ❌ raw GitHub URL as fallback
```

That raw GitHub URL, or in some edge cases a path starting with `/website/...`, would end up as an `href` in the iframe HTML. The JS click interceptor inside the iframe would see it starting with `/` and post it to Flutter as a navigation event — but it was never a registered route.

### Cause 2 — Plain markdown links were never resolved at all

The bigger culprit: blog content used **plain markdown links**, not wiki links:

```markdown
[windows-low-battery-alert-40-percent](website/content/blogs/windows-low-battery-alert-40-percent/windows-low-battery-alert-40-percent.md)
```

The old code only had `_resolveWikiLinks`. Plain `[label](path)` links were **completely ignored** — they passed through as-is into the rendered HTML. So the iframe rendered a real `<a href="website/content/...">` tag. When clicked, the JS interceptor saw it starting with `/website/` and sent it to Flutter as a route. Flutter had no idea what that was → 404.

```dart
// OLD _loadMd — plain markdown links never touched
var content = _resolveWikiLinks(resp.body);   // only handles [[wiki]]
content = _resolveImagePaths(content);
_mountSrcdoc(_mdToHtml(content));
```

---

## The Solution

Two fixes, applied together.

### Fix 1 — Safe fallback for unmatched wiki links

Instead of emitting a raw GitHub URL when no route matches, render the label as plain text. This is safe — the user can still read the link label, but it won't trigger a broken navigation.

```dart
// NEW code
return route.isNotEmpty
    ? '[$label]($route)'
    : label;  // ✅ plain text — no broken link, no 404
```

### Fix 2 — Add `_resolveMarkdownLinks` for plain `[label](path)` links

A new method that mirrors `_resolveWikiLinks` but handles standard markdown link syntax. It skips external `https://` links, converts relative paths through the same `_wikiToPath` normaliser, looks up the route, and either produces a clean route link or falls back to plain text.

```dart
String _resolveMarkdownLinks(String content) {
  // (?<!!) negative lookbehind excludes image links ![alt](src)
  final mdLinkRe = RegExp(r'(?<!!)\[([^\]]+)\]\(([^)]+)\)');
  return content.replaceAllMapped(mdLinkRe, (m) {
    final label = m.group(1)!.trim();
    final path  = m.group(2)!.trim();

    // Leave external URLs untouched
    if (path.startsWith('http://') || path.startsWith('https://')) {
      return m.group(0)!;
    }

    final repoPath = _wikiToPath(path);
    final route    = _routeForRepoPath(repoPath);

    return route.isNotEmpty
        ? '[$label]($route)'   // ✅ clean internal route
        : label;               // ✅ plain text fallback
  });
}
```

A shared `_routeForRepoPath` helper was also extracted so both `_resolveWikiLinks` and `_resolveMarkdownLinks` use the same lookup logic without duplication:

```dart
String _routeForRepoPath(String repoPath) {
  return _routes.entries
      .firstWhere(
        (e) => !e.value.startsWith('http') && e.value == repoPath,
        orElse: () => const MapEntry('', ''),
      )
      .key;
}
```

### Fix 3 — Harden the JS click interceptor

As a defensive layer, the JS inside `_mdToHtml` was updated to explicitly reject anything that looks like a file path rather than a clean route, even if something slips through the Dart layer:

```javascript
// NEW — hardened interceptor
document.addEventListener('click', function(e) {
  var el = e.target.closest('a');
  if (!el) return;
  var href = el.getAttribute('href');
  if (!href || !href.startsWith('/')) return;

  // Reject file paths — only clean routes allowed
  if (href.endsWith('.md')) return;
  if (href.includes('raw.githubusercontent')) return;
  if (href.startsWith('/website/')) return;
  if (href.startsWith('/content/')) return;

  e.preventDefault();
  window.parent.postMessage({ type: 'navigate', path: href }, '*');
});
```

### Updated `_loadMd` pipeline

The processing pipeline in `_loadMd` now runs all three resolvers in order:

```dart
var content = _resolveWikiLinks(resp.body);    // 1. [[wiki]] links
content = _resolveMarkdownLinks(content);       // 2. [plain](md) links  ← NEW
content = _resolveImagePaths(content);          // 3. image paths
_mountSrcdoc(_mdToHtml(content));
```

---

## Before vs After

|Scenario|Old behaviour|New behaviour|
|---|---|---|
|`[[wiki]]` link with matching route|✅ `/clean/route`|✅ `/clean/route`|
|`[[wiki]]` link with no matching route|❌ Raw GitHub URL → 404|✅ Plain text label|
|`[plain](website/content/...)` link with matching route|❌ Raw file path → 404|✅ `/clean/route`|
|`[plain](website/content/...)` link with no matching route|❌ Raw file path → 404|✅ Plain text label|
|`[external](https://...)` link|✅ Opens external URL|✅ Opens external URL|
|`![image](path)`|✅ Resolved to raw GitHub URL|✅ Resolved to raw GitHub URL|

---

## Key Takeaway

When you render markdown from external sources and intercept link clicks, you need to resolve **every link format your content uses** — not just the ones you wrote the app expecting. A single unhandled link syntax silently passes through as a raw file path and causes confusing 404s that look like routing bugs but are actually content-processing gaps.

Always process your content through the full resolver pipeline before mounting it, and add a defensive JS guard as a last line of defence.