README.txt  
Performance Optimization Guidelines for Shopify Theme
=====================================================

This document outlines the steps and best practices implemented to improve the performance and site speed of the Shopify theme. Following these optimization measures will help reduce load time, improve Core Web Vitals (LCP, CLS, TBT), and enhance user experience and SEO.

-----------------------------------------------------
Step 1: Clean `theme.liquid`
-----------------------------------------------------
✔ Remove unused images, inline CSS/JS, or hardcoded assets directly embedded in the `theme.liquid`.
✔ Conditionally load snippets using Liquid `if` logic only when needed.
✔ Avoid rendering modals, popups, or third-party components unless required on the current template.

Example:
```liquid
{% if template == 'product' %}
  {% render 'product-custom-snippet' %}
{% endif %}
