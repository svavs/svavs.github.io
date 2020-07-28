+++
title = "Blog"

description = "Blog posts"

# Used to sort pages by "date", "weight" or "none". See below for more information.
sort_by = "date"

# Used by the parent section to order its subsections.
# Lower values have higher priority.
#weight = 0

# Template to use to render this section page.
template = "post-list.html"

# The given template is applied to ALL pages below the section, recursively.
# If you have several nested sections, each with a page_template set, the page
# will always use the closest to itself.
# However, a page's own `template` variable will always have priority.
# Not set by default.
#page_template =

# This sets the number of pages to be displayed per paginated page.
# No pagination will happen if this isn't set or if the value is 0.
paginate_by = 10

# If set, this will be the path used by the paginated page. The page number will be appended after this path.
# The default is page/1.
paginate_path = "page"

# This determines whether to insert a link for each header like the ones you can see on this site if you hover over
# a header.
# The default template can be overridden by creating an `anchor-link.html` file in the `templates` directory.
# This value can be "left", "right" or "none".
#insert_anchor_links = "none"

# If set to "true", the section pages will be in the search index. This is only used if
# `build_search_index` is set to "true" in the Zola configuration file.
#in_search_index = true

# If set to "true", the section homepage is rendered.
# Useful when the section is used to organize pages (not used directly).
#render = true

# This determines whether to redirect when a user lands on the section. Defaults to not being set.
# Useful for the same reason as `render` but when you don't want a 404 when
# landing on the root section page.
# Example: redirect_to = "documentation/content/overview"
#redirect_to =

# If set to "true", the section will pass its pages on to the parent section. Defaults to `false`.
# Useful when the section shouldn't split up the parent section, like
# sections for each year under a posts section.
#transparent = false

# Use aliases if you are moving content but want to redirect previous URLs to the
# current one. This takes an array of paths, not URLs.
#aliases = []

# Your own data.
#[extra]
+++

This is the page listing the blog posts sorted by date.
