---
layout: post
title: "Randomized faithful Haar system"
subtitle: "subtitle"
background: "/assets/img/background.jpg"
typora-root-url: ".."
math: true
---

This is an interactive visualization of the randomized faithful Haar system constructed in my article [**Dimension dependence of factorization problems: Haar system Hardy spaces.**](/assets/pdf/publications/2025_Speckhofer_Dimension-dependence-of-factorization-problems-Haar-system-Hardy-spaces.pdf) _Studia Math._ **281** (2025), no. 2, 171-198. See also [arXiv:2407.05187](https://arxiv.org/abs/2407.05187), [doi](https://doi.org/10.4064/sm240706-24-11). Click [here](/assets/img/randomized-faithful-haar-system){:target="_blank"} to open in a new tab.

<style>
     #trees-container {
         display: flex;
         justify-content: space-between;
         width: 100%;
     }

     .tree-container {
         display: inline-block;
         flex: 1;
         margin: 0 0px;
         background-image: url(../../assets/img/2024-11-20-randomized-faithful-haar-system/bg.png);
         background-size: 100%; /* Ensures the image covers the container */
         z-index: 10;
     }

     .myrow {
         display: flex;
         justify-content: center;
         margin-bottom: 30px;
         background-image: url(../../assets/img/2024-11-20-randomized-faithful-haar-system/axes.png);
         background-size: 100%; /* Ensures the image covers the container */
         background-position: center;
         background-repeat: no-repeat;
         pointer-events: none;
         z-index: 8;
     }

     .image {
         cursor: pointer;
         display: flex;
         object-fit: fill;
         z-index: -1;
         pointer-events: all;
     }

     .disabled {
         pointer-events: none;
         opacity: 0.5;
     }
 </style>

<div id="trees-container"></div>
<button onclick="randomizeSigns()">Randomize</button>
<button id="toggleButton" onclick="toggleRandomizer()">Start</button>
<label>
    <input type="checkbox" id="myCheckbox" onchange="toggleBoolean()" checked> Full randomization
</label>


<script src="{{ base.url | prepend: site.url }}/assets/js/2024-11-20-randomized-faithful-haar-system/script.js"></script>


---

Clicking on the individual Haar functions changes their sign and, if "Full randomization" is checked, updates the subtree below the clicked function. Clicking on "Randomize" flips a randomly chosen Haar function. "Start" initiates an animation, where the system is randomized repeatedly.

