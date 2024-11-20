---
layout: post
title: "Randomized faithful Haar system"
subtitle: "subtitle"
background: "/assets/img/background.jpg"
typora-root-url: ".."
math: true
---

This is an interactive visualization of the randomized faithful Haar system constructed in my article **Dimension dependence of factorization problems: Haar system Hardy spaces** (_Studia Math._, to appear; [pdf](https://arxiv.org/pdf/2407.05187.pdf), see also [arXiv:2407.05187](https://arxiv.org/abs/2407.05187)). Click [here](/assets/img/randomized-faithful-haar-system){:target="_blank"} to open in a new tab.


---

Clicking on the individual Haar functions changes their sign and, if "Full randomization" is checked, updates the subtree below the clicked function. Clicking on "Randomize" flips a randomly chosen Haar function. "Start" initiates an animation, where the system is randomized repeatedly.






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

<script>
    const m = 2; // Number of dyadic trees
    const numLevels = 3; // Number of levels in each tree
    const imgBlue0 = '../../assets/img/2024-11-20-randomized-faithful-haar-system/blue0.png';
    const imgBlue1 = '../../assets/img/2024-11-20-randomized-faithful-haar-system/blue1.png';
    const imgBlue2 = '../../assets/img/2024-11-20-randomized-faithful-haar-system/blue2.png';
    const imgRed0 = '../../assets/img/2024-11-20-randomized-faithful-haar-system/red0.png';
    const imgRed1 = '../../assets/img/2024-11-20-randomized-faithful-haar-system/red1.png';
    const imgRed2 = '../../assets/img/2024-11-20-randomized-faithful-haar-system/red2.png';

    const imgSrcWhite = '../../assets/img/2024-11-20-randomized-faithful-haar-system/white.png';
    const imgSrcBg = '../../assets/img/2024-11-20-randomized-faithful-haar-system/bg.png';

    let fullRandomization = false;

    // Initialize theta and images as tree structures
    const theta = Array.from({ length: m }, () => {
        const tree = [];
        for (let level = 0; level < numLevels; level++) {
            const numNodes = Math.pow(2, level);
            tree.push(...Array(numNodes).fill(0));
        }
        return tree;
    });

    const imagesTree = Array.from({ length: m }, () => []); // Store images in a tree structure

    function createDyadicTree(treeIndex) {
        let treeContainer = document.createElement('div');
        treeContainer.classList.add('tree-container');
        treeContainer.style.width = `${100 / m}%`;

        let nodeIndex = 0; // Track the current node index for theta and imagesTree

        for (let row = 0; row < numLevels; row++) {
            const rowContainer = document.createElement('div');
            rowContainer.classList.add('myrow');
            const numImages = Math.pow(2, row);

            for (let i = 0; i < numImages; i++) {
                const img = document.createElement('img');
                img.alt = `Node ${treeIndex}-${nodeIndex}`;
                img.dataset.treeIndex = treeIndex;
                img.dataset.nodeIndex = nodeIndex;

                if (nodeIndex === 0 || nodeIndex === 1 || nodeIndex === 3) {
                    theta[treeIndex][nodeIndex] = 1;
                }

                // Set initial image source based on theta_t
                updateImageSource(img, treeIndex, nodeIndex);

                img.style.width = `${100 / numImages}%`;
                img.style.height = `200px`;
                img.classList.add('image');
                const tI = treeIndex;
                const nI = nodeIndex;
                img.onclick = () => handleImageClick(tI, nI);

                imagesTree[treeIndex][nodeIndex] = img; // Store the img reference
                rowContainer.appendChild(img);
                nodeIndex++;
            }

            treeContainer.appendChild(rowContainer);
        }

        document.getElementById('trees-container').appendChild(treeContainer);
    }

    function updateImageSource(img, treeIndex, nodeIndex) {
        let rowIndex = Math.floor(Math.log2(nodeIndex + 1));
        if (theta[treeIndex][nodeIndex] === 0) {
            img.src = imgSrcWhite;
            img.classList.add('disabled');
        } else {
            if (rowIndex === 0) {
                img.src = theta[treeIndex][nodeIndex] === 1 ? imgRed0 : imgBlue0;
            }
            else if (rowIndex === 1) {
                img.src = theta[treeIndex][nodeIndex] === 1 ? imgRed1 : imgBlue1;
            }
            else if (rowIndex === 2) {
                img.src = theta[treeIndex][nodeIndex] === 1 ? imgRed2 : imgBlue2;
            }
            img.classList.remove('disabled');
        }
    }

    function handleImageClick(treeIndex, nodeIndex) {
        // Update the theta value for the clicked node
        theta[treeIndex][nodeIndex] = theta[treeIndex][nodeIndex] === 1 ? -1 : 1;

        // Swap the subtrees below the left and right halves
        if (document.getElementById('myCheckbox').checked) {
            swapSubtrees(treeIndex, nodeIndex);
        }

        // Update the image sources for the entire tree
        updateAllImages(treeIndex);
    }

    function swapSubtrees(treeIndex, nodeIndex) {
        const level = Math.floor(Math.log2(nodeIndex + 1));

        const leftIndex = nodeIndex * 2 + 1;
        const rightIndex = nodeIndex * 2 + 2;

        const leftleftIndex = leftIndex * 2 + 1;
        const leftrightIndex = leftIndex * 2 + 2;
        const rightleftIndex = rightIndex * 2 + 1;
        const rightrightIndex = rightIndex * 2 + 2;

        const l = theta[treeIndex][leftIndex];
        const r = theta[treeIndex][rightIndex];
        const ll = theta[treeIndex][leftleftIndex];
        const lr = theta[treeIndex][leftrightIndex];
        const rl = theta[treeIndex][rightleftIndex];
        const rr = theta[treeIndex][rightrightIndex];

        theta[treeIndex][leftIndex] = r;
        theta[treeIndex][rightIndex] = l;
        theta[treeIndex][leftleftIndex] = rl;
        theta[treeIndex][leftrightIndex] = rr;
        theta[treeIndex][rightleftIndex] = ll;
        theta[treeIndex][rightrightIndex] = lr;
    }

    function updateAllImages(treeIndex) {
        for (let nodeIndex = 0; nodeIndex < theta[treeIndex].length; nodeIndex++) {
            let img = imagesTree[treeIndex][nodeIndex];
            if (img) {
                updateImageSource(img, treeIndex, nodeIndex);
            }
        }
    }

    let counter = -1;

    function randomizeSigns() {
        counter++;
        let treeIndex = counter % m;
        let rowIndex = Math.floor(Math.random() * numLevels);
        let nodeIndexMin = Math.pow(2, rowIndex) - 1;
        let nodeCountInRow = Math.pow(2, rowIndex);
        for (let nodeIndex = nodeIndexMin; nodeIndex < nodeIndexMin + nodeCountInRow; nodeIndex++) {
            if (theta[treeIndex][nodeIndex] === 1) {
                theta[treeIndex][nodeIndex] = -1;
        if (document.getElementById('myCheckbox').checked) {
            swapSubtrees(treeIndex, nodeIndex);
        }
            }
            else if (theta[treeIndex][nodeIndex] === -1) {
                theta[treeIndex][nodeIndex] = 1;
        if (document.getElementById('myCheckbox').checked) {
            swapSubtrees(treeIndex, nodeIndex);
        }
            }
        }
        updateAllImages(treeIndex);
    }

    // Initial draw of m dyadic trees
    for (let i = 0; i < m; i++) {
        createDyadicTree(i);
    }

    let intervalId = null;

    // Function to start or stop the timer
    function toggleRandomizer() {
        if (intervalId === null) {
            // Start the timer
            intervalId = setInterval(randomizeSigns, 400); // 400ms
            document.getElementById("toggleButton").textContent = "Stop";
        } else {
            // Stop the timer
            clearInterval(intervalId);
            intervalId = null;
            document.getElementById("toggleButton").textContent = "Start";
        }
    }



</script>

