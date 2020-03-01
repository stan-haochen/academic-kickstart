+++
# Slider widget.
widget = "slider"  # See https://sourcethemes.com/academic/docs/page-builder/
headless = true  # This file represents a page section.
active = true  # Activate this widget? true/false
weight = 120  # Order that this section will appear.

# Slide interval.
# Use `false` to disable animation or enter a time in ms, e.g. `5000` (5s).
interval = false

# Slide height (optional).
# E.g. `500px` for 500 pixels or `calc(100vh - 70px)` for full screen.
height = ""

# Slides.
# Duplicate an `[[item]]` block to add more slides.
[[item]]
  title = "Music"
  content = "Where I have invested a serious amount of time."
  align = "center"  # Choose `center`, `left`, or `right`.

  # Overlay a color or image (optional).
  #   Deactivate an option by commenting out the line, prefixing it with `#`.
  # overlay_color = "#666"  # An HTML color value.
  overlay_img = "sliders/metal_collage.jpg"  # Image path relative to your `static/img/` folder.
  overlay_filter = 0.2  # Darken the image. Value in range 0-1.

  # Call to action button (optional).
  #   Activate the button by specifying a URL and button label below.
  #   Deactivate by commenting out parameters, prefixing lines with `#`.
  cta_label = "My picks 2019"
  cta_url = "https://open.spotify.com/playlist/3i5gZLKoUOB3XeCrn486LI?si=wuEvVnzXTGid5sj8btLvFQ"
  cta_icon_pack = "fab"
  cta_icon = "spotify"

[[item]]
  title = "Artworks"
  content = "I'd love to draw my wife, and Mo Salah."
  align = "left"

  # overlay_color = "#555"  # An HTML color value.
  overlay_img = "sliders/bella.png"  # Image path relative to your `static/img/` folder.
  overlay_filter = 0.5  # Darken the image. Value in range 0-1.
  cta_label = "Some of my paintings"
  cta_url = "https://photos.app.goo.gl/W62wGEwqY4KSwBSq6"
  cta_icon_pack = "fas"
  cta_icon = "palette"

[[item]]
  title = "Misc"
  content = "TCG player, Arch Linux user, Emacser, LISPer, K-ON, SNL, Woody Allen, One of the KOP, Go Hawks!"
  align = "right"

  # overlay_color = "#333"  # An HTML color value.
  overlay_img = "sliders/misc.png"  # Image path relative to your `static/img/` folder.
  overlay_filter = 0.8  # Darken the image. Value in range 0-1.
+++
