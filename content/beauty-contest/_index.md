---
title: "Beauty Contest"
url: "/beauty-contest/"
draft: false
---

<div id="beauty-contest-embed">
  <iframe
    src="https://keynesianbeautycontest.streamlit.app"
    title="Beauty Contest App"
    loading="lazy"
    allow="fullscreen; clipboard-read; clipboard-write"
    sandbox="allow-scripts allow-same-origin allow-forms allow-popups"
  ></iframe>
  <noscript>
    JavaScript is required to view the Beauty Contest app. You can open it directly at
    <a href="https://keynesianbeautycontest.streamlit.app" rel="noopener" target="_blank">https://keynesianbeautycontest.streamlit.app</a>.
  </noscript>
</div>

<style>
  html, body {
    width: 100%;
    height: 100%;
    margin: 0;
  }
  main.main {
    padding: 0;
  }
  #beauty-contest-embed {
    position: fixed;
    inset: 0;
    z-index: 1000;
    background: #111;
  }
  #beauty-contest-embed iframe {
    width: 100%;
    height: 100%;
    border: 0;
  }
</style>

<script>
  (function () {
    document.documentElement.style.overflow = "hidden";
    document.body.style.overflow = "hidden";
  })();
</script>
