
# Performance Optimization Guidelines for Shopify Theme

This document outlines the steps and best practices implemented to improve the performance and site speed of the Shopify theme. Following these optimization measures will help reduce load time, improve Core Web Vitals (LCP, CLS, TBT), and enhance user experience and SEO.

---

## Step 1: Clean `theme.liquid`

- ✅ Remove unused images, inline CSS/JS, or hardcoded assets directly embedded in the `theme.liquid`.
- ✅ Conditionally load snippets using Liquid `if` logic only when needed.
- ✅ Avoid rendering modals, popups, or third-party components unless required on the current template.

**Example:**
```liquid
{% if template == 'product' %}
  {% render 'product-custom-snippet' %}
{% endif %}
```

---

## Step 2: Audit & Remove Unused Apps

- ✅ Identify and uninstall apps that are no longer in use.
- ✅ Remove app-related scripts or stylesheets that are still being loaded even after app deletion.
- ✅ Check in `theme.liquid`, `layout`, or `snippets` folders for leftover app code.

---

## Step 3: Improve CLS (Cumulative Layout Shift)

- ✅ Generate Critical CSS for above-the-fold content using:  
  [https://www.corewebvitals.io/tools/critical-css-generator](https://www.corewebvitals.io/tools/critical-css-generator)
- ✅ Ensure image dimensions are defined using:
  ```css
  width: 100%;
  height: auto;
  ```
- ✅ Use placeholder containers or skeleton loaders to preserve layout while lazy-loading content.
- ✅ Preload the base.css / theme.css file to load the css file to improve the FCP
  ``` liquid {{ 'base.css' | asset_url | stylesheet_tag: preload: true }}```

---

## Step 4: Implement Responsive Image Rendering

- ✅ Use a custom `responsive-picture` snippet to serve appropriate images for different devices.
- ✅ Automatically add `srcset`, `sizes`, `loading`, and `fetchpriority` attributes to optimize image loading.

**Usage Example:**
```liquid
{% render 'responsive-picture',
  forloop: forloop,
  desktop_image: block.settings.image_desktop,
  mobile_image: block.settings.image_mobile,
  img_class: 'custom-class',
  desktop_size: '50vw',
  mobile_size: '100vw'
%}
```
**Preloading hero banner to reduce LCP:**
``` liquid
{% render 'preload-lcp' %}
```
``` liquid
{%- if template.name == 'index' -%}
  {% assign preload_image_desktop = settings.preload_image_desktop %}
  {% assign preload_image_mobile = settings.preload_image_mobile %}

  {% if preload_image_desktop != blank %}
    <link
      rel="preload"
      as="image"
      fetchpriority="high"
      media="(min-width: 768px)"
      href="{{ preload_image_desktop | image_url }}"
      imagesrcset="
        {%- if preload_image_desktop.width >= 375 -%}{{ preload_image_desktop | image_url: width: 375 }} 375w,{%- endif -%}
        {%- if preload_image_desktop.width >= 550 -%}{{ preload_image_desktop | image_url: width: 550 }} 550w,{%- endif -%}
        {%- if preload_image_desktop.width >= 750 -%}{{ preload_image_desktop | image_url: width: 750 }} 750w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1100 -%}{{ preload_image_desktop | image_url: width: 1100 }} 1100w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1500 -%}{{ preload_image_desktop | image_url: width: 1500 }} 1500w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1780 -%}{{ preload_image_desktop | image_url: width: 1780 }} 1780w,{%- endif -%}
        {%- if preload_image_desktop.width >= 2000 -%}{{ preload_image_desktop | image_url: width: 2000 }} 2000w,{%- endif -%}
        {%- if preload_image_desktop.width >= 3000 -%}{{ preload_image_desktop | image_url: width: 3000 }} 3000w,{%- endif -%}
        {%- if preload_image_desktop.width >= 3840 -%}{{ preload_image_desktop | image_url: width: 3840 }} 3840w,{%- endif -%}
        {{ preload_image_desktop | image_url }} {{ preload_image_desktop.width -}}w
      "
      imagesizes="100vw"
    >
  {% endif %}

  {% if preload_image_mobile != blank %}
    <link
      rel="preload"
      as="image"
      fetchpriority="high"
      media="(max-width: 767px)"
      href="{{ preload_image_mobile | image_url }}"
      imagesrcset="
        {%- if preload_image_mobile.width >= 375 -%}{{ preload_image_mobile | image_url: width: 375 }} 375w,{%- endif -%}
        {%- if preload_image_mobile.width >= 550 -%}{{ preload_image_mobile | image_url: width: 550 }} 550w,{%- endif -%}
        {%- if preload_image_mobile.width >= 750 -%}{{ preload_image_mobile | image_url: width: 750 }} 750w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1100 -%}{{ preload_image_mobile | image_url: width: 1100 }} 1100w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1500 -%}{{ preload_image_mobile | image_url: width: 1500 }} 1500w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1780 -%}{{ preload_image_mobile | image_url: width: 1780 }} 1780w,{%- endif -%}
        {%- if preload_image_mobile.width >= 2000 -%}{{ preload_image_mobile | image_url: width: 2000 }} 2000w,{%- endif -%}
        {%- if preload_image_mobile.width >= 3000 -%}{{ preload_image_mobile | image_url: width: 3000 }} 3000w,{%- endif -%}
        {%- if preload_image_mobile.width >= 3840 -%}{{ preload_image_mobile | image_url: width: 3840 }} 3840w,{%- endif -%}
        {{ preload_image_mobile | image_url }} {{ preload_image_mobile.width -}}w
      "
      imagesizes="100vw"
    >
  {% endif %}
{%- elsif template.name == 'product' -%}
  {% assign preload_image_desktop = product.images[0] %}
  {% assign preload_image_mobile = product.images[0] %}

  {% if preload_image_desktop != blank %}
    <link
      rel="preload"
      as="image"
      fetchpriority="high"
      media="(min-width: 768px)"
      href="{{ preload_image_desktop | image_url }}"
      imagesrcset="
        {%- if preload_image_desktop.width >= 375 -%}{{ preload_image_desktop | image_url: width: 375 }} 375w,{%- endif -%}
        {%- if preload_image_desktop.width >= 550 -%}{{ preload_image_desktop | image_url: width: 550 }} 550w,{%- endif -%}
        {%- if preload_image_desktop.width >= 750 -%}{{ preload_image_desktop | image_url: width: 750 }} 750w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1100 -%}{{ preload_image_desktop | image_url: width: 1100 }} 1100w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1500 -%}{{ preload_image_desktop | image_url: width: 1500 }} 1500w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1780 -%}{{ preload_image_desktop | image_url: width: 1780 }} 1780w,{%- endif -%}
        {%- if preload_image_desktop.width >= 2000 -%}{{ preload_image_desktop | image_url: width: 2000 }} 2000w,{%- endif -%}
        {%- if preload_image_desktop.width >= 3000 -%}{{ preload_image_desktop | image_url: width: 3000 }} 3000w,{%- endif -%}
        {%- if preload_image_desktop.width >= 3840 -%}{{ preload_image_desktop | image_url: width: 3840 }} 3840w,{%- endif -%}
        {{ preload_image_desktop | image_url }} {{ preload_image_desktop.width -}}w
      "
      imagesizes="50vw"
    >
  {% endif %}

  {% if preload_image_mobile != blank %}
    <link
      rel="preload"
      as="image"
      fetchpriority="high"
      media="(max-width: 767px)"
      href="{{ preload_image_mobile | image_url }}"
      imagesrcset="
        {%- if preload_image_mobile.width >= 375 -%}{{ preload_image_mobile | image_url: width: 375 }} 375w,{%- endif -%}
        {%- if preload_image_mobile.width >= 550 -%}{{ preload_image_mobile | image_url: width: 550 }} 550w,{%- endif -%}
        {%- if preload_image_mobile.width >= 750 -%}{{ preload_image_mobile | image_url: width: 750 }} 750w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1100 -%}{{ preload_image_mobile | image_url: width: 1100 }} 1100w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1500 -%}{{ preload_image_mobile | image_url: width: 1500 }} 1500w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1780 -%}{{ preload_image_mobile | image_url: width: 1780 }} 1780w,{%- endif -%}
        {%- if preload_image_mobile.width >= 2000 -%}{{ preload_image_mobile | image_url: width: 2000 }} 2000w,{%- endif -%}
        {%- if preload_image_mobile.width >= 3000 -%}{{ preload_image_mobile | image_url: width: 3000 }} 3000w,{%- endif -%}
        {%- if preload_image_mobile.width >= 3840 -%}{{ preload_image_mobile | image_url: width: 3840 }} 3840w,{%- endif -%}
        {{ preload_image_mobile | image_url }} {{ preload_image_mobile.width -}}w
      "
      imagesizes="80vw"
    >
  {% endif %}
{%- elsif template.name == 'collection' -%}
  {% assign preload_image_desktop = collection.products[0].featured_media.preview_image %}

  {% if preload_image_desktop != blank %}
    <link
      rel="preload"
      as="image"
      fetchpriority="high"
      media="(min-width: 768px)"
      href="{{ preload_image_desktop | image_url }}"
      imagesrcset="
        {%- if preload_image_desktop.width >= 375 -%}{{ preload_image_desktop | image_url: width: 375 }} 375w,{%- endif -%}
        {%- if preload_image_desktop.width >= 550 -%}{{ preload_image_desktop | image_url: width: 550 }} 550w,{%- endif -%}
        {%- if preload_image_desktop.width >= 750 -%}{{ preload_image_desktop | image_url: width: 750 }} 750w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1100 -%}{{ preload_image_desktop | image_url: width: 1100 }} 1100w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1500 -%}{{ preload_image_desktop | image_url: width: 1500 }} 1500w,{%- endif -%}
        {%- if preload_image_desktop.width >= 1780 -%}{{ preload_image_desktop | image_url: width: 1780 }} 1780w,{%- endif -%}
        {%- if preload_image_desktop.width >= 2000 -%}{{ preload_image_desktop | image_url: width: 2000 }} 2000w,{%- endif -%}
        {%- if preload_image_desktop.width >= 3000 -%}{{ preload_image_desktop | image_url: width: 3000 }} 3000w,{%- endif -%}
        {%- if preload_image_desktop.width >= 3840 -%}{{ preload_image_desktop | image_url: width: 3840 }} 3840w,{%- endif -%}
        {{ preload_image_desktop | image_url }} {{ preload_image_desktop.width -}}w
      "
      imagesizes="50vw"
    >
  {% endif %}

  {% if preload_image_mobile != blank %}
    <link
      rel="preload"
      as="image"
      fetchpriority="high"
      media="(max-width: 767px)"
      href="{{ preload_image_mobile | image_url }}"
      imagesrcset="
        {%- if preload_image_mobile.width >= 375 -%}{{ preload_image_mobile | image_url: width: 375 }} 375w,{%- endif -%}
        {%- if preload_image_mobile.width >= 550 -%}{{ preload_image_mobile | image_url: width: 550 }} 550w,{%- endif -%}
        {%- if preload_image_mobile.width >= 750 -%}{{ preload_image_mobile | image_url: width: 750 }} 750w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1100 -%}{{ preload_image_mobile | image_url: width: 1100 }} 1100w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1500 -%}{{ preload_image_mobile | image_url: width: 1500 }} 1500w,{%- endif -%}
        {%- if preload_image_mobile.width >= 1780 -%}{{ preload_image_mobile | image_url: width: 1780 }} 1780w,{%- endif -%}
        {%- if preload_image_mobile.width >= 2000 -%}{{ preload_image_mobile | image_url: width: 2000 }} 2000w,{%- endif -%}
        {%- if preload_image_mobile.width >= 3000 -%}{{ preload_image_mobile | image_url: width: 3000 }} 3000w,{%- endif -%}
        {%- if preload_image_mobile.width >= 3840 -%}{{ preload_image_mobile | image_url: width: 3840 }} 3840w,{%- endif -%}
        {{ preload_image_mobile | image_url }} {{ preload_image_mobile.width -}}w
      "
      imagesizes="100vw"
    >
  {% endif %}
{%- endif -%}
```
---

## Step 5: Use `defer.js` for Lazy Loading Images & Scripts

- ✅ Integrate [defer.js](https://github.com/shinsenter/defer.js) for intelligent lazy-loading of images, iframes, and scripts.

**Installation:**  
Place the following script before closing `</body>` in `theme.liquid`:
```html
<script defer src="https://cdn.jsdelivr.net/npm/@shinsenter/defer.js@3.5.0/dist/defer.min.js"></script>
```

**For Images:**
```html
<img 
  class="lazyload tw-w-full"
  data-src="{{ image | image_url: width: 800 }}"
  width="800"
  height="auto"
  alt="Image description">
```

**For Scripts:**

Defer non-essential third-party or custom scripts using:
```html
<script type="lazy-script" src="https://example.com/your-script.js"></script>
<script>
      Defer.dom('.lazyload.img');
      Defer.all('script[type="lazy-script"]', 0, true);
</script>
```

---

## Additional Recommendations

- ✅ Minify and combine CSS & JS where possible.
- ✅ Defer non-critical JavaScript using `defer` or load after `DOMContentLoaded`.
- ✅ Lazy load sections using `data-section-id` with `IntersectionObserver`.
- ✅ Avoid excessive DOM nesting or unused CSS rules.
- ✅ Use a CDN (Shopify uses Fastly/CDN by default — ensure it's not bypassed).
- ✅ Use Shopify’s image filters like `image_url` with appropriate widths to serve optimized assets.
- ✅ Preload important resources like fonts and hero images.
- ✅ Convert large GIFs to MP4/WebM where possible.
- ✅ Avoid overloading `settings_data.json` with unused blocks or large text.
- ✅ Limit the number of custom font variants loaded.
- ✅ Replace polling or `setInterval` scripts with `IntersectionObserver` or `requestIdleCallback`.
