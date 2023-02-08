---
# A section created with the Portfolio widget.
# This section displays content from `content/project/`.
# See https://wowchemy.com/docs/widget/portfolio/
widget: portfolio

# This file represents a page section.
headless: true

# Order that this section appears on the page.
weight: 30

title: 'The Books I read. The Podcast I listen. <br> The Youtube I watch. '
subtitle: 'They may also be of interest to you.'

content:
  # Page type to display. E.g. project.
  page_type: note

  # Default filter index (e.g. 0 corresponds to the first `filter_button` instance below).
  filter_default: 0

  # Filter toolbar (optional).
  # Add or remove as many filters (`filter_button` instances) as you like.
  # To show all items, set `tag` to "*".
  # To filter by a specific tag, set `tag` to an existing tag name.
  # To remove the toolbar, delete the entire `filter_button` block.
  filter_button:
    - name: All
      tag: '*'
    - name: Books
      tag: Book
    - name: Podcasts
      tag: podcast
    - name: Youtube
      tag: youtube
    # - name: Fun2
    #   tag: fun2
    # - name: Fun3
    #   tag: fun3
    # - name: Fun4
    #   tag: fun4
    # - name: Fun5
    #   tag: fun5
    # - name: Fun666
    #   tag: fun666
design:
  columns: '1'
  view: masonry
  flip_alt_rows: true
  background: {}
  spacing: {padding: [25, 0, 0, 0]}
---
