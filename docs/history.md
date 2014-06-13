---
layout: docs
title: 修改历史
permalink: "/docs/history/"
prev_section: contributing
---

## 1.2.2 / 2014-06-12

### Bug Fixes

- Properly prefix links in site template with URL or baseurl depending upon
    need. ([#2319]({{ site.repository }}/issues/2319))
- Update gist tag comments and error message to require username ([#2326]({{ site.repository }}/issues/2326))
- Fix `permalink` setting in site template ([#2331]({{ site.repository }}/issues/2331))
- Don't fail if any of the path objects are nil ([#2325]({{ site.repository }}/issues/2325))
- Instantiate all descendants for converters and generators, not just
    direct subclasses ([#2334]({{ site.repository }}/issues/2334))
- Replace all instances of `site.name` with `site.title` in site template ([#2324]({{ site.repository }}/issues/2324))
- `Jekyll::Filters#time` now accepts UNIX timestamps in string or number form ([#2339]({{ site.repository }}/issues/2339))
- Use `item_property` for `where` filter so it doesn't break on collections ([#2359]({{ site.repository }}/issues/2359))
- Rescue errors thrown so `--watch` doesn't fail ([#2364]({{ site.repository }}/issues/2364))

### Site Enhancements

- Add missing "as" to assets docs page ([#2337]({{ site.repository }}/issues/2337))
- Update docs to reflect new `baseurl` default ([#2341]({{ site.repository }}/issues/2341))
- Add links to headers who have an ID. ([#2342]({{ site.repository }}/issues/2342))
- Use symbol instead of HTML number in `upgrading.md` ([#2351]({{ site.repository }}/issues/2351))
- Fix link to frontmatter defaults docs ([#2353]({{ site.repository }}/issues/2353))
- Fix for `History.markdown` in order to fix history page in docs ([#2363]({{ site.repository }}/issues/2363))


