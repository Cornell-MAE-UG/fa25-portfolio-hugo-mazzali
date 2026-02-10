---
layout: default
title: Art
permalink: /art/
---

<div class="art-wrap">
  <div class="art-grid">
    {% assign art = site.static_files
      | where_exp: "f", "f.path contains '/assets/images/art/'"
      | shuffle %}

    {% for f in art %}
      <div class="tile {% cycle 'r4x5','r1x1','r3x4','r16x9','r4x5','r9x16' %}">
        <img src="{{ f.path | relative_url }}" alt="{{ f.name }}" loading="lazy">
      </div>
    {% endfor %}
  </div>
</div>

<!-- Lightbox -->
<div id="lightbox" class="lightbox" aria-hidden="true">
  <button class="lightbox-close" type="button" aria-label="Close">×</button>
  <img class="lightbox-img" alt="">
</div>

<script>
  (function () {
    const lb = document.getElementById("lightbox");
    const lbImg = lb.querySelector(".lightbox-img");
    const closeBtn = lb.querySelector(".lightbox-close");

    function openLightbox(imgEl) {
      lbImg.src = imgEl.currentSrc || imgEl.src;
      lbImg.alt = imgEl.alt || "";
      lb.classList.add("is-open");
      lb.setAttribute("aria-hidden", "false");
      document.body.classList.add("no-scroll");
    }

    function closeLightbox() {
      lb.classList.remove("is-open");
      lb.setAttribute("aria-hidden", "true");
      lbImg.src = "";
      document.body.classList.remove("no-scroll");
    }

    // Click any art image to open
    document.addEventListener("click", (e) => {
      const img = e.target.closest(".art-grid img");
      if (img) openLightbox(img);
    });

    // Close button
    closeBtn.addEventListener("click", closeLightbox);

    // Click outside image closes
    lb.addEventListener("click", (e) => {
      if (e.target === lb) closeLightbox();
    });

    // ESC closes
    document.addEventListener("keydown", (e) => {
      if (e.key === "Escape" && lb.classList.contains("is-open")) closeLightbox();
    });
  })();
</script>

<script>
  (function(){
    const grid = document.querySelector('.art-grid');
    if(!grid) return;
    const tiles = Array.from(grid.children);
    for (let i = tiles.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      grid.appendChild(tiles[j]);
      tiles.splice(j, 1);
    }
  })();
</script>


