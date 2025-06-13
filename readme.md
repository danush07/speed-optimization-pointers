
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
<script type="deferjs" data-src="https://example.com/your-script.js"></script>
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
