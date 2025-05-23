---
layout: single
categories: posts
tags: posts
title: UNSW CS Course Graph
postage: true
labels: educational project
---

This is a graph of course relationships of all courses within computer science that are running in 2025 at UNSW. The code I used to generate this made a few assumptions/simplifications:

1. Courses are colour coded based on AI-determined categories based on course name and description. 77 unique courses would have taken too long to manually colour.
2. When a course COMP1234 has prerequisites COMPABCD OR (COMPEFGH AND COMPIJKL), this is represented as COMP1234 has prerequisites COMPABCD, COMPEFGH, COMPIJKL. This simplifies the graph.
3. Only courses running in 2025 are shown.
4. I removed courses that CSE students do not typically take like COMP1917, COMP1921 etc which are courses that have more common course codes e.g. COMP1511, COMP2521. This simplifies the graph.

When using this, keep in mind,

1. Sometimes the graph breaks or takes up to 20 seconds when switching between types of graphs (to generate the graph). Don't click out of the graph when you select an option and simply wait for it to load. If it seems stuck after a while, reload the page and try again. Also *Reset View* is a bit broken so you should probably avoid it.
2. This is mostly intended to be used in full screen mode and sometimes the graph is not centered so if you see a blank canvas, usually the graph is to the right off the screen (zoom out slightly and drag to the bottom right).
3. The intended way to find a course on the graph is to use the searchbar given and type out the course code itself (e.g. COMP1511)

## Features

* Clicking on a course shows immediate prereqs and unlocked courses
* Full screen and embedded modes
* Multiple graph types (**FDP is recommended but Circo is alright too**)
* When clicking on a node, you can see prereqs, unlocks or both (select from the options upstream, downstream, both)
* When you search for a course, it is equivalent to clicking on it

<style>
.graphviz-container {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    margin: 20px 0;
    background: #1e1e1e;
    color: #fff;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

.graphviz-toolbar {
    background: #2d2d30;
    padding: 12px 20px;
    border-bottom: 1px solid #3e3e42;
    display: flex;
    align-items: center;
    gap: 15px;
    flex-wrap: wrap;
}

.graphviz-control-group {
    display: flex;
    align-items: center;
    gap: 8px;
}

.graphviz-control-group label {
    font-size: 13px;
    color: #cccccc;
    white-space: nowrap;
}

.graphviz-container select, .graphviz-container input[type="range"] {
    background: #3c3c3c;
    color: #fff;
    border: 1px solid #555;
    border-radius: 4px;
    padding: 4px 8px;
    font-size: 12px;
}

.graphviz-container select:focus, .graphviz-container input:focus {
    outline: none;
    border-color: #007acc;
}

.graphviz-search-box {
    background: #3c3c3c;
    border: 1px solid #555;
    border-radius: 4px;
    padding: 6px 10px;
    color: #fff;
    font-size: 12px;
    width: 200px;
}

.graphviz-info-panel {
    position: absolute;
    top: 70px;
    right: 20px;
    background: rgba(45, 45, 48, 0.95);
    border: 1px solid #3e3e42;
    border-radius: 6px;
    padding: 15px;
    max-width: 300px;
    backdrop-filter: blur(10px);
    display: none;
    z-index: 1000;
}

.graphviz-info-panel .close-btn {
    position: absolute;
    top: 10px;
    right: 15px;
    background: none;
    border: none;
    color: #cccccc;
    font-size: 18px;
    cursor: pointer;
    width: 20px;
    height: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
}

.graphviz-info-panel .close-btn:hover {
    color: #fff;
    background: rgba(255,255,255,0.1);
    border-radius: 3px;
}

.graphviz-info-panel h3 {
    margin: 0 0 10px 0;
    color: #4fc3f7;
    font-size: 16px;
    padding-right: 25px;
}

.graphviz-info-panel .connections {
    margin-top: 10px;
}

.graphviz-info-panel .connection-type {
    margin: 8px 0;
    font-size: 13px;
}

.graphviz-info-panel .connection-type strong {
    color: #81c784;
}

.graphviz-connection-list {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
    margin-top: 5px;
}

.graphviz-connection-item {
    background: #4a4a4a;
    padding: 3px 8px;
    border-radius: 12px;
    font-size: 11px;
    color: #e0e0e0;
    cursor: pointer;
}

.graphviz-connection-item:hover {
    background: #5a5a5a;
}

.graphviz-graph {
    width: 100%;
    height: 600px;
    background: #fafafa;
    position: relative;
    overflow: hidden;
}

.graphviz-zoom-controls {
    position: absolute;
    bottom: 20px;
    left: 20px;
    display: flex;
    flex-direction: column;
    gap: 5px;
    z-index: 1000;
}

.graphviz-zoom-btn {
    background: rgba(45, 45, 48, 0.9);
    border: 1px solid #555;
    color: #fff;
    width: 35px;
    height: 35px;
    border-radius: 4px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
    font-weight: bold;
}

.graphviz-zoom-btn:hover {
    background: rgba(55, 55, 58, 0.9);
}

.graphviz-loading {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: #666;
    font-size: 16px;
}

.graphviz-highlighted {
    filter: drop-shadow(0 0 8px #4fc3f7) !important;
}

.graphviz-dimmed {
    opacity: 0.3 !important;
}

.graphviz-status-bar {
    background: #2d2d30;
    padding: 8px 20px;
    border-top: 1px solid #3e3e42;
    font-size: 12px;
    color: #cccccc;
    display: flex;
    justify-content: space-between;
}

.graphviz-btn {
    background: #0e639c;
    color: #fff;
    border: none;
    padding: 6px 12px;
    border-radius: 4px;
    cursor: pointer;
    font-size: 12px;
}

.graphviz-btn:hover {
    background: #0a4d7a;
}

.graphviz-btn.secondary {
    background: #5a5a5a;
}

.graphviz-btn.secondary:hover {
    background: #6a6a6a;
}

/* Fullscreen styles */
.graphviz-fullscreen-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: #1e1e1e;
    z-index: 10000;
    display: none;
    flex-direction: column;
}

.graphviz-fullscreen-overlay .graphviz-toolbar {
    flex-shrink: 0;
}

.graphviz-fullscreen-overlay .graphviz-graph {
    flex: 1;
    height: auto;
}

.graphviz-fullscreen-overlay .graphviz-info-panel {
    top: 80px;
}

.graphviz-exit-fullscreen {
    position: absolute;
    top: 20px;
    right: 20px;
    background: rgba(45, 45, 48, 0.9);
    border: 1px solid #555;
    color: #fff;
    padding: 8px 12px;
    border-radius: 4px;
    cursor: pointer;
    z-index: 10001;
}

.graphviz-exit-fullscreen:hover {
    background: rgba(55, 55, 58, 0.9);
}
</style>

<div class="graphviz-container">
    <div class="graphviz-toolbar">
        <div class="graphviz-control-group">
            <label>Layout:</label>
            <select id="graphviz-layoutEngine">
                <option value="fdp">FDP</option>
                <option value="circo">Circo</option>
                <option value="dot">Dot</option>
                <option value="neato">Neato</option>
                <option value="sfdp">SFDP</option>
                <option value="twopi">Twopi</option>
            </select>
        </div>
        
        <div class="graphviz-control-group">
            <label>Show:</label>
            <select id="graphviz-direction">
                <option value="all">All Connections</option>
                <option value="upstream">Upstream (Prerequisites)</option>
                <option value="downstream">Downstream (Unlocks)</option>
            </select>
        </div>

        <div class="graphviz-control-group">
            <label>Zoom:</label>
            <input type="range" id="graphviz-zoomSlider" min="0.1" max="3" step="0.1" value="1">
            <span id="graphviz-zoomValue">100%</span>
        </div>

        <div class="graphviz-control-group">
            <input type="text" class="graphviz-search-box" id="graphviz-searchBox" placeholder="Search courses...">
        </div>

        <div class="graphviz-control-group">
            <button class="graphviz-btn" onclick="graphvizResetView()">Reset View</button>
            <button class="graphviz-btn" onclick="graphvizEnterFullscreen()">Fullscreen</button>
            <button class="graphviz-btn secondary" onclick="graphvizExportSVG()">Export SVG</button>
        </div>
    </div>

    <div style="position: relative;">
        <div id="graphviz-graph" class="graphviz-graph"></div>
        <div class="graphviz-loading" id="graphviz-loading">Rendering graph...</div>
        
        <div class="graphviz-zoom-controls">
            <button class="graphviz-zoom-btn" onclick="graphvizZoomIn()">+</button>
            <button class="graphviz-zoom-btn" onclick="graphvizZoomOut()">−</button>
            <button class="graphviz-zoom-btn" onclick="graphvizResetView()" title="Reset View">⌂</button>
        </div>

        <div class="graphviz-info-panel" id="graphviz-infoPanel">
            <button class="close-btn" onclick="graphvizHideInfoPanel()">&times;</button>
            <h3 id="graphviz-selectedCourse">Course Info</h3>
            <div id="graphviz-courseDetails"></div>
            <div class="connections">
                <div class="connection-type">
                    <strong>Prerequisites:</strong>
                    <div class="graphviz-connection-list" id="graphviz-prerequisites"></div>
                </div>
                <div class="connection-type">
                    <strong>Unlocks:</strong>
                    <div class="graphviz-connection-list" id="graphviz-unlocks"></div>
                </div>
            </div>
        </div>

        <div class="graphviz-status-bar">
            <span>Click on a course to see its connections | Double-click to focus | Search to select</span>
            <span id="graphviz-graphStats">Loading...</span>
        </div>
    </div>
</div>

<!-- Fullscreen overlay -->
<div class="graphviz-fullscreen-overlay" id="graphviz-fullscreenOverlay">
    <button class="graphviz-exit-fullscreen" onclick="graphvizExitFullscreen()">Exit Fullscreen</button>
    <div class="graphviz-toolbar">
        <div class="graphviz-control-group">
            <label>Layout:</label>
            <select id="graphviz-layoutEngine-fs">
                <option value="fdp">FDP</option>
                <option value="circo">Circo</option>
                <option value="dot">Dot</option>
                <option value="neato">Neato</option>
                <option value="sfdp">SFDP</option>
                <option value="twopi">Twopi</option>
            </select>
        </div>
        
        <div class="graphviz-control-group">
            <label>Show:</label>
            <select id="graphviz-direction-fs">
                <option value="all">All Connections</option>
                <option value="upstream">Upstream (Prerequisites)</option>
                <option value="downstream">Downstream (Unlocks)</option>
            </select>
        </div>

        <div class="graphviz-control-group">
            <label>Zoom:</label>
            <input type="range" id="graphviz-zoomSlider-fs" min="0.1" max="3" step="0.1" value="1">
            <span id="graphviz-zoomValue-fs">100%</span>
        </div>

        <div class="graphviz-control-group">
            <input type="text" class="graphviz-search-box" id="graphviz-searchBox-fs" placeholder="Search courses...">
        </div>

        <div class="graphviz-control-group">
            <button class="graphviz-btn" onclick="graphvizResetView()">Reset View</button>
            <button class="graphviz-btn secondary" onclick="graphvizExportSVG()">Export SVG</button>
        </div>
    </div>
    
    <div style="position: relative; flex: 1;">
        <div id="graphviz-graph-fs" class="graphviz-graph"></div>
        <div class="graphviz-loading" id="graphviz-loading-fs">Rendering graph...</div>
        
        <div class="graphviz-zoom-controls">
            <button class="graphviz-zoom-btn" onclick="graphvizZoomIn()">+</button>
            <button class="graphviz-zoom-btn" onclick="graphvizZoomOut()">−</button>
            <button class="graphviz-zoom-btn" onclick="graphvizResetView()" title="Reset View">⌂</button>
        </div>

        <div class="graphviz-info-panel" id="graphviz-infoPanel-fs">
            <button class="close-btn" onclick="graphvizHideInfoPanel()">&times;</button>
            <h3 id="graphviz-selectedCourse-fs">Course Info</h3>
            <div id="graphviz-courseDetails-fs"></div>
            <div class="connections">
                <div class="connection-type">
                    <strong>Prerequisites:</strong>
                    <div class="graphviz-connection-list" id="graphviz-prerequisites-fs"></div>
                </div>
                <div class="connection-type">
                    <strong>Unlocks:</strong>
                    <div class="graphviz-connection-list" id="graphviz-unlocks-fs"></div>
                </div>
            </div>
        </div>

        <div class="graphviz-status-bar">
            <span>Click on a course to see its connections | Double-click to focus | Search to select</span>
            <span id="graphviz-graphStats-fs">Loading...</span>
        </div>
    </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/7.8.5/d3.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3-graphviz/5.4.0/d3-graphviz.min.js"></script>
<script>
(function() {
    let graphvizInstance;
    let graphvizInstanceFS;
    let graphvizSelectedNode = null;
    let graphvizConnections = {};
    let graphvizZoomLevel = 1;
    let graphvizCurrentLayout = 'fdp';
    let graphvizIsFullscreen = false;

    // Complete course data from the document
    const graphvizCourseData = {
        nodes: {
            // Foundational CS & Core Programming (Sky Blue)
            "COMP1010": { color: "#87CEEB", label: "COMP1010\\nArt of Computing" },
            "COMP1511": { color: "#87CEEB", label: "COMP1511\\nProg Fundamentals" },
            "COMP1531": { color: "#87CEEB", label: "COMP1531\\nSoftware Eng Fund." },
            "COMP2511": { color: "#87CEEB", label: "COMP2511\\nOO Design & Prog" },
            "COMP2521": { color: "#87CEEB", label: "COMP2521\\nData Struct & Algo" },

            // Software Engineering & Development (Light Salmon)
            "COMP2041": { color: "#FFA07A", label: "COMP2041\\nSoftware Construction" },
            "COMP3900": { color: "#FFA07A", label: "COMP3900\\nComp Sci Project" },
            "COMP3901": { color: "#FFA07A", label: "COMP3901\\nSpecial Project A" },
            "COMP3902": { color: "#FFA07A", label: "COMP3902\\nSpecial Project B" },
            "COMP4920": { color: "#FFA07A", label: "COMP4920\\nProf Issues IT" },
            "COMP6080": { color: "#FFA07A", label: "COMP6080\\nWeb Front-End Prog" },
            "COMP6452": { color: "#FFA07A", label: "COMP6452\\nSoftware Arch Blockchain" },
            "COMP6771": { color: "#FFA07A", label: "COMP6771\\nAdv C++ Prog" },
            "COMP6991": { color: "#FFA07A", label: "COMP6991\\nMod Prog Rust" },
            "COMP9321": { color: "#FFA07A", label: "COMP9321\\nData Services Eng" },

            // Computer Systems & Networks (Peru)
            "COMP1521": { color: "#CD853F", label: "COMP1521\\nComp Sys Fund" },
            "COMP2121": { color: "#CD853F", label: "COMP2121\\nDig Circ & Sys" },
            "COMP3142": { color: "#CD853F", label: "COMP3142\\nSoftware Test & QA" },
            "COMP3211": { color: "#CD853F", label: "COMP3211\\nComp Architecture" },
            "COMP3222": { color: "#CD853F", label: "COMP3222\\nDig Circ & Sys" },
            "COMP3231": { color: "#CD853F", label: "COMP3231\\nOperating Systems" },
            "COMP3331": { color: "#CD853F", label: "COMP3331\\nComp Net & Apps" },
            "COMP3601": { color: "#CD853F", label: "COMP3601\\nDesign Project A" },
            "COMP3891": { color: "#CD853F", label: "COMP3891\\nExtended OS" },
            "COMP4601": { color: "#CD853F", label: "COMP4601\\nDesign Project B" },
            "COMP6420": { color: "#CD853F", label: "COMP6420\\nHardware Security" },
            "COMP9242": { color: "#CD853F", label: "COMP9242\\nAdv Operating Systems" },
            "COMP9334": { color: "#CD853F", label: "COMP9334\\nCap Plan Comp Sys & Net" },

            // Algorithms and Theory (Olive Drab)
            "COMP3121": { color: "#6B8E23", label: "COMP3121\\nAlgo Design & Analysis" },
            "COMP3131": { color: "#6B8E23", label: "COMP3131\\nProg Lang & Compilers" },
            "COMP3153": { color: "#6B8E23", label: "COMP3153\\nAlgo Verification" },
            "COMP3161": { color: "#6B8E23", label: "COMP3161\\nConcepts Prog Lang" },
            "COMP3821": { color: "#6B8E23", label: "COMP3821\\nExt Algo Design & Analysis" },
            "COMP4128": { color: "#6B8E23", label: "COMP4128\\nProg Challenges" },
            "COMP4141": { color: "#6B8E23", label: "COMP4141\\nTheory of Comp" },
            "COMP4161": { color: "#6B8E23", label: "COMP4161\\nAdv Software Verification" },
            "COMP6131": { color: "#6B8E23", label: "COMP6131\\nSoftware Security Analysis" },
            "COMP9727": { color: "#6B8E23", label: "COMP9727\\nRecommender Systems" },

            // AI and Data Science (Orchid)
            "COMP3311": { color: "#DA70D6", label: "COMP3311\\nDatabase Systems" },
            "COMP3411": { color: "#DA70D6", label: "COMP3411\\nArtificial Intelligence" },
            "COMP3421": { color: "#DA70D6", label: "COMP3421\\nComputer Graphics" },
            "COMP3431": { color: "#DA70D6", label: "COMP3431\\nRobotic Software Arch" },
            "COMP3511": { color: "#DA70D6", label: "COMP3511\\nHCI" },
            "COMP4418": { color: "#DA70D6", label: "COMP4418\\nKnow Rep & Reasoning" },
            "COMP6713": { color: "#DA70D6", label: "COMP6713\\nNat Language Proc" },
            "COMP6714": { color: "#DA70D6", label: "COMP6714\\nInfo Ret & Web Search" },
            "COMP9312": { color: "#DA70D6", label: "COMP9312\\nData Analysis for Graphs" },
            "COMP9313": { color: "#DA70D6", label: "COMP9313\\nBig Data Mgmt" },
            "COMP9315": { color: "#DA70D6", label: "COMP9315\\nDB Sys Implementation" },
            "COMP9319": { color: "#DA70D6", label: "COMP9319\\nWeb Data Comp & Search" },
            "COMP9417": { color: "#DA70D6", label: "COMP9417\\nML & Data Mining" },
            "COMP9418": { color: "#DA70D6", label: "COMP9418\\nAdv Stat ML" },
            "COMP9444": { color: "#DA70D6", label: "COMP9444\\nNN & Deep Learning" },
            "COMP9517": { color: "#DA70D6", label: "COMP9517\\nComputer Vision" },

            // Security (Medium Orchid)
            "COMP1337": { color: "#9370DB", label: "COMP1337\\nFoundational Cyber Security" },
            "COMP1939": { color: "#9370DB", label: "COMP1939\\nCyber Sec Wksp 1" },
            "COMP3441": { color: "#9370DB", label: "COMP3441\\nSec Eng & Cyber Sec" },
            "COMP6441": { color: "#9370DB", label: "COMP6441\\nSec Eng & Cyber Sec" },
            "COMP6841": { color: "#9370DB", label: "COMP6841\\nExt Sec Eng & Cyber Sec" },
            "COMP6443": { color: "#9370DB", label: "COMP6443\\nWeb App Sec & Test" },
            "COMP6445": { color: "#9370DB", label: "COMP6445\\nDigital Forensics" },
            "COMP6447": { color: "#9370DB", label: "COMP6447\\nSys & Software Sec Assess" },
            "COMP6448": { color: "#9370DB", label: "COMP6448\\nSec Eng Masterclass" },
            "COMP6453": { color: "#9370DB", label: "COMP6453\\nApplied Cryptography" },
            "COMP6843": { color: "#9370DB", label: "COMP6843\\nExt Web App Sec & Test" },
            "COMP6845": { color: "#9370DB", label: "COMP6845\\nExt Digital Forensics" },
            "COMP9301": { color: "#9370DB", label: "COMP9301\\nCyber Security Project" },
            "COMP9302": { color: "#9370DB", label: "COMP9302\\nCyber Security Project B" },
            "COMP9447": { color: "#9370DB", label: "COMP9447\\nSec Eng Workshop" },
            "COMP4336": { color: "#9370DB", label: "COMP4336\\nMobile Data Networking" },
            "COMP4337": { color: "#9370DB", label: "COMP4337\\nSec Fixed & Wireless Net" },
            "COMP6733": { color: "#9370DB", label: "COMP6733\\nIoT Exp Design Studio" },

            // Thesis (Light Blue)
            "COMP4951": { color: "#ADD8E6", label: "COMP4951\\nResearch Thesis A" },
            "COMP4952": { color: "#ADD8E6", label: "COMP4952\\nResearch Thesis B" },
            "COMP4953": { color: "#ADD8E6", label: "COMP4953\\nResearch Thesis C" },
            "COMP4961": { color: "#ADD8E6", label: "COMP4961\\nComp Sci Thesis A" },
            "COMP4962": { color: "#ADD8E6", label: "COMP4962\\nComp Sci Thesis B" },
            "COMP4963": { color: "#ADD8E6", label: "COMP4963\\nComp Sci Thesis C" },

            // External Dependencies (Light Grey)
            "MATH1081": { color: "#D3D3D3", label: "MATH1081\\nDiscrete Math" },
            "MATH2400": { color: "#D3D3D3", label: "MATH2400\\nEng Math 3A" },
            "MATH5836": { color: "#D3D3D3", label: "MATH5836\\nData Mining & ML" },
            "ELEC2141": { color: "#D3D3D3", label: "ELEC2141\\nDig Circuit Design" },
            "ELEC2142": { color: "#D3D3D3", label: "ELEC2142\\nEmbed Sys Design" },
            "DPST1091": { color: "#D3D3D3", label: "DPST1091\\nProg Fund (Adv)" },
            "DPST1092": { color: "#D3D3D3", label: "DPST1092\\nData Struct & Algo (Adv)" },
            "MTRN2500": { color: "#D3D3D3", label: "MTRN2500\\nComp for Mechatronics" },
            "MTRN3500": { color: "#D3D3D3", label: "MTRN3500\\nRobotics & Mechatronics Design" },
            "DESN2000": { color: "#D3D3D3", label: "DESN2000\\nEng Design 2" }
        },
        
        // All edges from the complete DOT file
        edges: [
            ["COMP1511", "COMP1521"], ["DPST1091", "COMP1521"],
            ["COMP1511", "COMP1531"], ["DPST1091", "COMP1531"],
            ["COMP1337", "COMP1939"],
            ["COMP1511", "COMP2041"],
            ["COMP1531", "COMP2511"], ["COMP2521", "COMP2511"],
            ["COMP1511", "COMP2521"], ["COMP2521", "COMP3121"],
            ["COMP2511", "COMP3131"],
            ["COMP1531", "COMP3142"], ["COMP2511", "COMP3142"], ["COMP2521", "COMP3142"],
            ["MATH1081", "COMP3153"],
            ["COMP2521", "COMP3161"],
            ["COMP3222", "COMP3211"], ["ELEC2141", "COMP3211"],
            ["COMP2121", "COMP3222"], ["COMP1521", "COMP3222"], ["DPST1092", "COMP3222"],
            ["COMP1521", "COMP3231"], ["DPST1092", "COMP3231"], ["COMP2121", "COMP3231"], ["ELEC2142", "COMP3231"], ["COMP2521", "COMP3231"],
            ["COMP2521", "COMP3311"],
            ["COMP2521", "COMP3331"], ["MTRN2500", "COMP3331"],
            ["COMP2521", "COMP3411"],
            ["COMP2511", "COMP3421"],
            ["COMP2521", "COMP3431"],
            ["DESN2000", "COMP3601"], ["COMP2121", "COMP3601"], ["COMP3222", "COMP3601"],
            ["COMP2521", "COMP3821"], ["MATH1081", "COMP3821"],
            ["COMP1521", "COMP3891"], ["COMP2121", "COMP3891"], ["COMP2521", "COMP3891"],
            ["COMP1531", "COMP3900"], ["COMP2521", "COMP3900"],
            ["COMP3821", "COMP4128"], ["COMP3121", "COMP4128"],
            ["MATH1081", "COMP4141"], ["COMP2521", "COMP4141"],
            ["COMP3331", "COMP4336"],
            ["COMP3331", "COMP4337"],
            ["COMP3411", "COMP4418"],
            ["COMP3211", "COMP4601"], ["COMP3601", "COMP4601"],
            ["COMP4951", "COMP4952"],
            ["COMP4952", "COMP4953"],
            ["COMP4961", "COMP4962"],
            ["COMP4962", "COMP4963"],
            ["COMP1531", "COMP6080"], ["COMP2521", "COMP6080"],
            ["MATH1081", "COMP6131"], ["COMP6771", "COMP6131"], ["COMP2521", "COMP6131"],
            ["COMP1521", "COMP6420"], ["COMP3222", "COMP6420"], ["ELEC2141", "COMP6420"],
            ["COMP6441", "COMP6443"], ["COMP6841", "COMP6443"], ["COMP3441", "COMP6443"],
            ["COMP3441", "COMP6445"], ["COMP6441", "COMP6445"], ["COMP6841", "COMP6445"],
            ["COMP6841", "COMP6447"], ["COMP6441", "COMP6447"], ["COMP3441", "COMP6447"], ["COMP1337", "COMP6447"],
            ["COMP6841", "COMP6448"], ["COMP6441", "COMP6448"], ["COMP1337", "COMP6448"],
            ["COMP1531", "COMP6452"], ["COMP2521", "COMP6452"],
            ["COMP2521", "COMP6453"], ["MATH1081", "COMP6453"], ["MATH2400", "COMP6453"],
            ["MATH1081", "COMP6713"], ["COMP3411", "COMP6713"], ["COMP9444", "COMP6713"],
            ["MATH1081", "COMP6714"], ["COMP1531", "COMP6714"], ["COMP2041", "COMP6714"], ["COMP2521", "COMP6714"],
            ["COMP3331", "COMP6733"],
            ["COMP2511", "COMP6771"],
            ["COMP2521", "COMP6841"],
            ["COMP6441", "COMP6843"], ["COMP6841", "COMP6843"], ["COMP3441", "COMP6843"], ["COMP1337", "COMP6843"],
            ["COMP3441", "COMP6845"], ["COMP6441", "COMP6845"], ["COMP6841", "COMP6845"], ["COMP1337", "COMP6845"],
            ["COMP2521", "COMP6991"],
            ["COMP3231", "COMP9242"], ["COMP3891", "COMP9242"],
            ["COMP6441", "COMP9301"], ["COMP6841", "COMP9301"], ["COMP1337", "COMP9301"], ["COMP6443", "COMP9301"], ["COMP6843", "COMP9301"], ["COMP6445", "COMP9301"], ["COMP6845", "COMP9301"], ["COMP6447", "COMP9301"],
            ["COMP6441", "COMP9302"], ["COMP6841", "COMP9302"], ["COMP1337", "COMP9302"], ["COMP6443", "COMP9302"], ["COMP6843", "COMP9302"], ["COMP6445", "COMP9302"], ["COMP6845", "COMP9302"], ["COMP6447", "COMP9302"],
            ["COMP2521", "COMP9312"], ["COMP3311", "COMP9312"],
            ["COMP2521", "COMP9313"], ["COMP3311", "COMP9313"],
            ["COMP2521", "COMP9315"], ["COMP3311", "COMP9315"],
            ["COMP2521", "COMP9319"],
            ["COMP1531", "COMP9321"], ["COMP2041", "COMP9321"], ["COMP3311", "COMP9321"],
            ["COMP1511", "COMP9334"], ["DPST1091", "COMP9334"],
            ["MATH1081", "COMP9417"], ["COMP1531", "COMP9417"], ["COMP2041", "COMP9417"], ["COMP2521", "COMP9417"],
            ["MATH5836", "COMP9418"], ["COMP9417", "COMP9418"],
            ["COMP2521", "COMP9444"], ["MTRN3500", "COMP9444"],
            ["COMP6441", "COMP9447"], ["COMP6841", "COMP9447"], ["COMP3441", "COMP9447"],
            ["COMP2521", "COMP9517"],
            ["COMP2521", "COMP9727"]
        ]
    };

    function graphvizGetCurrentContainer() {
        return graphvizIsFullscreen ? 'fs' : '';
    }

    function graphvizGetElementId(baseId) {
        const suffix = graphvizGetCurrentContainer();
        return suffix ? `${baseId}-${suffix}` : baseId;
    }

    function graphvizGenerateDOT(layout = 'fdp') {
        let dot = `digraph CoursePrerequisites {
    esep="0.3";
    sep="0.2";
    overlap="false";
    mindist="1.0";
    len="1.5";
    splines="true";
    layout=${layout};
    
    node [style="filled,rounded", shape=box, fontname="Arial", fixedsize=false, width="2.0", height="1.0"];
    edge [arrowhead=vee, penwidth=1.0];

`;

        // Add nodes
        Object.keys(graphvizCourseData.nodes).forEach(nodeId => {
            const node = graphvizCourseData.nodes[nodeId];
            dot += `    "${nodeId}" [fillcolor="${node.color}", label="${node.label}"];\n`;
        });

        dot += '\n';

        // Add edges
        graphvizCourseData.edges.forEach(([from, to]) => {
            dot += `    "${from}" -> "${to}";\n`;
        });

        dot += '}';
        return dot;
    }

    function graphvizInitialize() {
        document.getElementById(graphvizGetElementId('graphviz-loading')).style.display = 'block';
        
        const graphContainer = graphvizIsFullscreen ? "#graphviz-graph-fs" : "#graphviz-graph";
        const instance = d3.select(graphContainer).graphviz()
            .transition(function () {
                return d3.transition("main").ease(d3.easeLinear).duration(500);
            })
            .on("initEnd", function() {
                document.getElementById(graphvizGetElementId('graphviz-loading')).style.display = 'none';
                graphvizUpdateStats();
            });
        
        if (graphvizIsFullscreen) {
            graphvizInstanceFS = instance;
        } else {
            graphvizInstance = instance;
        }
        
        graphvizParseConnections();
        graphvizUpdateGraph();
    }

    function graphvizUpdateGraph() {
        const layoutSelect = document.getElementById(graphvizGetElementId('graphviz-layoutEngine'));
        const layout = layoutSelect ? layoutSelect.value : 'fdp';
        graphvizCurrentLayout = layout;
        
        document.getElementById(graphvizGetElementId('graphviz-loading')).style.display = 'block';
        
        const dotContent = graphvizGenerateDOT(layout);
        const instance = graphvizIsFullscreen ? graphvizInstanceFS : graphvizInstance;
        
        if (instance) {
            instance.renderDot(dotContent);
        }
        
        setTimeout(() => {
            graphvizAddInteractivity();
            document.getElementById(graphvizGetElementId('graphviz-loading')).style.display = 'none';
            graphvizUpdateStats();
        }, 800);
    }

    function graphvizParseConnections() {
        graphvizConnections = {};
        
        // Initialize all nodes
        Object.keys(graphvizCourseData.nodes).forEach(nodeId => {
            graphvizConnections[nodeId] = { prerequisites: [], unlocks: [] };
        });
        
        // Parse edges
        graphvizCourseData.edges.forEach(([from, to]) => {
            if (graphvizConnections[from] && graphvizConnections[to]) {
                graphvizConnections[from].unlocks.push(to);
                graphvizConnections[to].prerequisites.push(from);
            }
        });
    }

    function graphvizAddInteractivity() {
        const graphSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .node' : '#graphviz-graph .node';
        const graphContainer = graphvizIsFullscreen ? '#graphviz-graph-fs' : '#graphviz-graph';
        
        d3.selectAll(graphSelector).on('click', function(event, d) {
            const nodeId = d3.select(this).select('title').text();
            graphvizShowNodeInfo(nodeId, this);
            graphvizHighlightConnections(nodeId);
            event.stopPropagation();
        });

        d3.select(graphContainer).on('click', function() {
            graphvizClearHighlights();
            graphvizHideInfoPanel();
        });

        const searchBox = document.getElementById(graphvizGetElementId('graphviz-searchBox'));
        if (searchBox) {
            searchBox.addEventListener('input', function(e) {
                graphvizSearchNodes(e.target.value);
            });
        }
    }

    function graphvizShowNodeInfo(nodeId, nodeElement) {
        graphvizSelectedNode = nodeId;
        const panel = document.getElementById(graphvizGetElementId('graphviz-infoPanel'));
        const courseName = document.getElementById(graphvizGetElementId('graphviz-selectedCourse'));
        const details = document.getElementById(graphvizGetElementId('graphviz-courseDetails'));
        const prerequisites = document.getElementById(graphvizGetElementId('graphviz-prerequisites'));
        const unlocks = document.getElementById(graphvizGetElementId('graphviz-unlocks'));

        const nodeData = graphvizCourseData.nodes[nodeId];
        const labelParts = nodeData ? nodeData.label.split('\\n') : [nodeId];
        const code = labelParts[0];
        const name = labelParts.slice(1).join(' ') || 'Course';

        if (courseName) courseName.textContent = code || nodeId;
        if (details) details.innerHTML = `<div style="color: #b0b0b0; font-size: 12px;">${name}</div>`;

        const nodeConnections = graphvizConnections[nodeId] || { prerequisites: [], unlocks: [] };
        
        if (prerequisites) {
            prerequisites.innerHTML = '';
            nodeConnections.prerequisites.forEach(prereq => {
                const span = document.createElement('span');
                span.className = 'graphviz-connection-item';
                span.textContent = prereq;
                span.onclick = () => graphvizSearchAndHighlight(prereq);
                prerequisites.appendChild(span);
            });
        }

        if (unlocks) {
            unlocks.innerHTML = '';
            nodeConnections.unlocks.forEach(unlock => {
                const span = document.createElement('span');
                span.className = 'graphviz-connection-item';
                span.textContent = unlock;
                span.onclick = () => graphvizSearchAndHighlight(unlock);
                unlocks.appendChild(span);
            });
        }

        if (panel) panel.style.display = 'block';
    }

    function graphvizHighlightConnections(nodeId) {
        graphvizClearHighlights();
        
        const directionSelect = document.getElementById(graphvizGetElementId('graphviz-direction'));
        const direction = directionSelect ? directionSelect.value : 'all';
        const nodeConnections = graphvizConnections[nodeId] || { prerequisites: [], unlocks: [] };
        
        let nodesToHighlight = [nodeId];
        
        if (direction === 'upstream' || direction === 'all') {
            nodesToHighlight = nodesToHighlight.concat(nodeConnections.prerequisites);
        }
        
        if (direction === 'downstream' || direction === 'all') {
            nodesToHighlight = nodesToHighlight.concat(nodeConnections.unlocks);
        }

        const nodeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .node' : '#graphviz-graph .node';
        const edgeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .edge' : '#graphviz-graph .edge';

        // Dim all nodes and edges
        d3.selectAll(nodeSelector).classed('graphviz-dimmed', true);
        d3.selectAll(edgeSelector).classed('graphviz-dimmed', true);

        // Highlight selected nodes
        nodesToHighlight.forEach(connectedId => {
            d3.selectAll(nodeSelector).filter(function() {
                return d3.select(this).select('title').text() === connectedId;
            }).classed('graphviz-dimmed', false).classed('graphviz-highlighted', true);
        });

        // Highlight relevant edges
        d3.selectAll(edgeSelector).filter(function() {
            const title = d3.select(this).select('title').text();
            const [from, to] = title.split('->').map(s => s.trim());
            
            let shouldHighlight = false;
            
            if (direction === 'upstream' || direction === 'all') {
                shouldHighlight = shouldHighlight || (nodesToHighlight.includes(from) && nodesToHighlight.includes(to));
            }
            
            if (direction === 'downstream' || direction === 'all') {
                shouldHighlight = shouldHighlight || (nodesToHighlight.includes(from) && nodesToHighlight.includes(to));
            }
            
            return shouldHighlight;
        }).classed('graphviz-dimmed', false).classed('graphviz-highlighted', true);
    }

    function graphvizClearHighlights() {
        const nodeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .node, #graphviz-graph-fs .edge' : '#graphviz-graph .node, #graphviz-graph .edge';
        d3.selectAll(nodeSelector).classed('graphviz-highlighted graphviz-dimmed', false);
        graphvizSelectedNode = null;
    }

    function graphvizHideInfoPanel() {
        const panel = document.getElementById(graphvizGetElementId('graphviz-infoPanel'));
        if (panel) panel.style.display = 'none';
    }

    function graphvizSearchNodes(query) {
        graphvizClearHighlights();
        if (!query) return;

        const nodeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .node' : '#graphviz-graph .node';
        let foundNodes = [];

        d3.selectAll(nodeSelector).each(function() {
            const nodeId = d3.select(this).select('title').text();
            const nodeData = graphvizCourseData.nodes[nodeId];
            const labelText = nodeData ? nodeData.label.toLowerCase() : nodeId.toLowerCase();
            
            if (nodeId.toLowerCase().includes(query.toLowerCase()) || 
                labelText.includes(query.toLowerCase())) {
                d3.select(this).classed('graphviz-highlighted', true);
                foundNodes.push({id: nodeId, element: this});
            }
        });

        // If exactly one course found, simulate clicking on it
        if (foundNodes.length === 1) {
            const found = foundNodes[0];
            graphvizShowNodeInfo(found.id, found.element);
            graphvizHighlightConnections(found.id);
        }
    }

    function graphvizSearchAndHighlight(nodeId) {
        const nodeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .node' : '#graphviz-graph .node';
        const nodeElement = d3.selectAll(nodeSelector).filter(function() {
            return d3.select(this).select('title').text() === nodeId;
        }).node();
        
        if (nodeElement) {
            graphvizShowNodeInfo(nodeId, nodeElement);
            graphvizHighlightConnections(nodeId);
        }
    }

    function graphvizApplyZoom() {
        const svgSelector = graphvizIsFullscreen ? '#graphviz-graph-fs svg g' : '#graphviz-graph svg g';
        const sliderSelector = graphvizGetElementId('graphviz-zoomSlider');
        const valueSelector = graphvizGetElementId('graphviz-zoomValue');
        
        d3.select(svgSelector).attr('transform', `scale(${graphvizZoomLevel})`);
        
        const slider = document.getElementById(sliderSelector);
        const valueSpan = document.getElementById(valueSelector);
        
        if (slider) slider.value = graphvizZoomLevel;
        if (valueSpan) valueSpan.textContent = Math.round(graphvizZoomLevel * 100) + '%';
    }

    function graphvizUpdateStats() {
        const nodeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .node' : '#graphviz-graph .node';
        const edgeSelector = graphvizIsFullscreen ? '#graphviz-graph-fs .edge' : '#graphviz-graph .edge';
        
        const nodeCount = d3.selectAll(nodeSelector).size();
        const edgeCount = d3.selectAll(edgeSelector).size();
        
        const statsElement = document.getElementById(graphvizGetElementId('graphviz-graphStats'));
        if (statsElement) {
            statsElement.textContent = `${nodeCount} courses, ${edgeCount} prerequisites`;
        }
    }

    function graphvizSyncControls() {
        // Sync layout
        const normalLayout = document.getElementById('graphviz-layoutEngine');
        const fsLayout = document.getElementById('graphviz-layoutEngine-fs');
        if (normalLayout && fsLayout) {
            if (graphvizIsFullscreen) {
                fsLayout.value = normalLayout.value;
            } else {
                normalLayout.value = fsLayout.value;
            }
        }
        
        // Sync direction
        const normalDirection = document.getElementById('graphviz-direction');
        const fsDirection = document.getElementById('graphviz-direction-fs');
        if (normalDirection && fsDirection) {
            if (graphvizIsFullscreen) {
                fsDirection.value = normalDirection.value;
            } else {
                normalDirection.value = fsDirection.value;
            }
        }
    }

    // Global functions for button clicks
    window.graphvizZoomIn = function() {
        graphvizZoomLevel = Math.min(graphvizZoomLevel * 1.2, 3);
        graphvizApplyZoom();
    };

    window.graphvizZoomOut = function() {
        graphvizZoomLevel = Math.max(graphvizZoomLevel / 1.2, 0.1);
        graphvizApplyZoom();
    };

    window.graphvizResetView = function() {
        graphvizZoomLevel = 1;
        graphvizApplyZoom();
        graphvizClearHighlights();
        graphvizHideInfoPanel();
        const searchBox = document.getElementById(graphvizGetElementId('graphviz-searchBox'));
        if (searchBox) searchBox.value = '';
    };

    window.graphvizHideInfoPanel = function() {
        graphvizHideInfoPanel();
        graphvizClearHighlights();
    };

    window.graphvizEnterFullscreen = function() {
        graphvizIsFullscreen = true;
        graphvizSyncControls();
        document.getElementById('graphviz-fullscreenOverlay').style.display = 'flex';
        document.body.style.overflow = 'hidden';
        
        // Initialize fullscreen instance
        setTimeout(() => {
            graphvizInitialize();
        }, 100);
    };

    window.graphvizExitFullscreen = function() {
        graphvizIsFullscreen = false;
        document.getElementById('graphviz-fullscreenOverlay').style.display = 'none';
        document.body.style.overflow = 'auto';
        graphvizSyncControls();
    };

    window.graphvizExportSVG = function() {
        const svgSelector = graphvizIsFullscreen ? '#graphviz-graph-fs svg' : '#graphviz-graph svg';
        const svgElement = document.querySelector(svgSelector);
        if (svgElement) {
            const serializer = new XMLSerializer();
            const svgString = serializer.serializeToString(svgElement);
            const blob = new Blob([svgString], { type: 'image/svg+xml' });
            const url = URL.createObjectURL(blob);
            
            const a = document.createElement('a');
            a.href = url;
            a.download = 'course-prerequisites.svg';
            a.click();
            
            URL.revokeObjectURL(url);
        }
    };

    // Event listeners for normal view
    function graphvizSetupEventListeners() {
        const layoutEngine = document.getElementById(graphvizGetElementId('graphviz-layoutEngine'));
        const direction = document.getElementById(graphvizGetElementId('graphviz-direction'));
        const zoomSlider = document.getElementById(graphvizGetElementId('graphviz-zoomSlider'));
        
        if (layoutEngine) {
            layoutEngine.addEventListener('change', graphvizUpdateGraph);
        }
        
        if (direction) {
            direction.addEventListener('change', function() {
                if (graphvizSelectedNode) {
                    graphvizHighlightConnections(graphvizSelectedNode);
                }
            });
        }
        
        if (zoomSlider) {
            zoomSlider.addEventListener('input', function(e) {
                graphvizZoomLevel = parseFloat(e.target.value);
                graphvizApplyZoom();
            });
        }
    }

    // Initialize when DOM is ready
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', function() {
            graphvizInitialize();
            graphvizSetupEventListeners();
        });
    } else {
        graphvizInitialize();
        graphvizSetupEventListeners();
    }
})();
</script>


## YAML File With Course Info

Feel free to use this for any projects (but please credit this website for the time taken collecting and cleaning this data):

```yaml
courses:
  COMP1010:
    course_code: COMP1010
    description: This course aims to provide a grounding in computational thinking
      for anyone who wants one. It assumes no previous programming background, but
      does assume that all incoming students have used digital devices, such as tablets
      and smart phones, for a range of tasks (e.g. social networking, reading, essay
      writing, etc.). The course will use Python as the programming medium and use
      real world examples from a variety of domains to motivate understanding.
    offering_terms: Term 1
    prerequisite_raw: 'Exclusion: COMP1511/DPST1091<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1010
    title: The Art of Computing
  COMP1337:
    course_code: COMP1337
    description: 'This course provides students with an introduction to the rapidly
      growing field of cyber security. It is suitable for anyone who wishes to understand
      more about this fascinating and rapidly emerging global challenge. The course
      provides an overview of the main aspects of contemporary cyber security, including
      cyber crime, cyber criminals, cyber war, scams, exploitation, human errors and
      mistakes, vulnerabilities, responding to emergencies, sensible risk assessment
      and risk management.

      We consider the question: We can build a bridge that doesn&#39;t fall down;
      why can&#39;t we yet build a system that can&#39;t be hacked? We consider what
      we can learn from established disciplines, including engineering, safety science,
      investment, and military intelligence, that can help us establish the new discipline
      of cyber security.

      This course introduces an engineering-style approach to how we can design, manage,
      and improve secure systems. However students do not have to be engineers to
      take this course, indeed the interactive parts of the course work best when
      we have a multi-disciplinary, diverse range of students. As well as learning
      core cyber security terminology and concepts (such as introductory cryptography,
      the main classes of computer vulnerabilities that hackers exploit, and how to
      analyse risk), students also work in diverse teams to analyse and respond to
      a range of case studies drawn from historical security and security related
      scenarios (such as incident response to emergencies, system design, root cause
      failure analysis).Â'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: Enrolment in 3777 Bachelor of Cyber Security<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1337
    title: Foundations of Cyber Security
  COMP1511:
    course_code: COMP1511
    description: 'From recent innovations in AI like self-driving cars to humanoid
      robotics navigating complex environments, leapfrogs in battery technology to
      sequencing the human genome - the world is benefiting and evolving thanks to
      computer systems. At the core of all these systems are computers executing instructions
      to solve exciting problems.

      In this course, you will learn the fundamentals of how we instruct computers
      to solve problems. You will explore the architecture and mechanics of how computers
      operate and how you can translate real-world problems to computer programs that
      solve these problems.

      The concepts you learn will provide a foundation for your future endeavours
      in computing and, we hope, will begin to change the way you think about real-world
      problems.

      This course is an introductory course to the basics of computer programming
      and Computer Science. It is intended as an introduction to studying further
      in Computer Science or related fields. Topics include:

      Fundamental programming conceptsIntroduction to Computer ScienceThe C programming
      language and use of a C compilerProgramming styleProgram design and organisation
      conceptsProgram testing and debugging'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: No prerequisites specified
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1511
    title: Programming Fundamentals
  COMP1521:
    course_code: COMP1521
    description: This course introduces students to how computer systems are structured
      in terms of basic electronic components, how they are used to implement procedural
      programs, and how they are structured as a collection of software layers. It
      introduces students to low-level software layers such as operating systems,
      and introduces concurrency concepts. The goal is to give students a solid understanding
      of what happens when high-level programs are executed, as a basis for further
      study in important areas of computing such as computer architecture, operating
      systems.
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1511 or DPST1091 or COMP1911 or COMP1917<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1521
    title: Computer Systems Fundamentals
  COMP1531:
    course_code: COMP1531
    description: 'This course teaches students about software engineering principles
      via exposure to the important practice of building correct products in effectively
      functioning teams.

      You will be exposed to agile software practices, team collaboration and effective
      communication through implementing a group project based on agile software methodologies
      that require you to analyse, design, build and deploy a web-based application.
      This course is typically taken soon after completing COMP1511 but could be delayed
      and taken later. It provides essential background for the teamwork and project
      management required in many later courses.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1511 or DPST1091 or COMP1917 or COMP1921<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1531
    title: Software Engineering Fundamentals
  COMP1911:
    course_code: COMP1911
    description: 'From recent innovations in AI like self-driving cars to humanoid
      robotics navigating complex environments, leapfrogs in battery technology to
      sequencing the human genome - the world is benefiting and evolving thanks to
      computer systems. At the core of all these systems are computers executing instructions
      to solve exciting problems.

      In this course, you will learn the fundamentals of how we instruct computers
      to solve problems. You will explore the architecture and mechanics of how computers
      operate and how you can translate real-world problems to computer programs that
      solve these problems.

      The concepts you learn will provide a foundation for your future endeavours
      in computing and, we hope, will begin to change the way you think about real-world
      problems.

      This course is an introductory course to the basics of Computer Programming
      and Computer Science. It is intended as an introduction to studying further
      in Computer Science or related fields. Topics include:

      Fundamental programming conceptsIntroduction to Computer ScienceThe C programming
      language and use of a C compilerProgramming styleProgram design and organisation
      conceptsProgram testing and debugging

      Additional Information:

      This course is taught within COMP1511.'
    offering_terms: Term 2, Term 3
    prerequisite_raw: 'Prerequisite: Enrolment in a non-CSE major (no BINF, COMP,
      or SENG)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1911
    title: Computing 1A
  COMP1939:
    course_code: COMP1939
    description: 'This workshop course provides students with a series of practical
      challenges and applied exercises to apply and integrate the cyber security and
      computing knowledge and skills they have mastered in their first year of cyber
      security study.Â Â 

      In this hands-on course, students will learn about and apply practice penetration
      testing, including negotiating terms of engagement, recon, reporting, controls,
      OSInt, exploitation, privilege escalation, lateral movement, and data leakage.
      Students will work in teams - led by experienced mentors and carry out a series
      of penetration testing wargames throughout the term.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP1337 and enrolment in 3777 Bachelor of Cyber
      Security<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP1939
    title: Cyber Security Workshop 1 - Penetration Testing
  COMP2041:
    course_code: COMP2041
    description: 'This course is designed for students who have mastered the basics
      of programming. It aims to broaden your knowledge of techniques and tools for
      software construction. It covers: Unix filters, shell scripting and Python (for
      programming), git (for version control), docker (for portable deployment), package
      managers (for configuration and deployment). At the end of this course, you
      should be able to build moderate-sized software systems and configure them so
      that others can download and deploy your work.'
    offering_terms: Term 1, Term 2
    prerequisite_raw: 'Prerequisite: COMP1511 OR DPST1091 OR COMP1917 OR COMP1921<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP2041
    title: 'Software Construction: Techniques and Tools'
  COMP2511:
    course_code: COMP2511
    description: 'COMP2511 builds on the foundation of first-year CSE courses, and
      begins to answer the questions: &#34;What does good software look like?&#34;
      and &#34;How do you build software that is flexible, reusable and maintainable&#34;.
      The course introduces students to the Object-Oriented Programming paradigm and
      explores how OOP attempts to solve the problem of good software design. Students
      explore fundamental Software Patterns in designing, writing and testing software,
      and how to apply these strategies to both theoretical and real-world problems.
      The course also teaches an appreciation for elegantly written code, problem
      solving and finding well-designed solutions to problems that have longevity
      of software in mind.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1531 AND (COMP2521 OR COMP1927)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP2511
    title: Object-Oriented Design & Programming
  COMP2521:
    course_code: COMP2521
    description: 'The aim of this course is to get you toÂ think like a computer scientistÂ .
      This certainly sounds like a noble goal... but what does it really mean? How
      does aÂ scientistÂ , let alone a computer scientist, actually think?

      What many types of scientists try to do is understand natural systems and processes:
      a geologist, for example, tries to understand the structure of the earth; a
      biologist tries to understand living organisms; a chemist tries to understand
      materials and reactions, and so on.

      Computer scientists don&#39;t, as the name might suggest, simply try to understand
      the structure and behaviour of computers, but are more concerned with understanding
      software systems (and the interaction between the software and the hardware
      on which it runs). Also, unlike other scientists, computer scientists frequently
      build the objects that they study.

      The goal of this course is to deepen your understanding of data structures and
      algorithms and how these can be employed effectively in the design of software
      systems. It is an important course in covering a range of core data structures
      and algorithms that will be used in context in later courses. You explore these
      ideas in lectures, tutorials, lab exercises, quizzes and assignments. Assessment
      involves lab exercises, quizzes, assignments and a final exam involving both
      practice and theory. At the end of the course, we want you to be a solid programmer,
      with knowledge of a range of useful data structures and programming techniques,
      and ready to continue with further specialised studies in computing.

      Topics

      This course provides an introduction to the structure, analysis and usage of
      a range of fundamental data types and the core algorithms that operate on them.
      Key topics are:

      RecursionAnalysis of algorithmsAbstract data typesBinary search treesBalanced
      search treesGraphsSorting algorithmsHeapsHashingTries'
    offering_terms: Summer Term, Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1511 or DPST1091 or COMP1917 or COMP1921<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP2521
    title: Data Structures and Algorithms
  COMP3121:
    course_code: COMP3121
    description: 'How would you convince a colleague that your program is correct,
      and that theirs is flawed?

      How do you estimate how long a program will run for, and design test cases to
      find bugs?Can all problems be solved efficiently, or are some problems &#34;too
      hard&#34;?

      In this course, you will learn the building blocks to develop problem-solving
      software in fields as diverse as finance, logistics, policy and entertainment.
      You will apply techniques including divide-and-conquer, greedy selection and
      dynamic programming in order to develop accurate and fast algorithms in contexts
      such as graphs and strings. You will also develop the ability to think critically
      and communicate about algorithms in terms of correctness and efficiency.

      Join us to find out how you can become a better programmer without writing any
      code.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3121
    title: Algorithm Design and Analysis
  COMP3131:
    course_code: COMP3131
    description: This subject focusses on basic compiler techniques needed to implement
      programming languages in a virtual machine (with emphasis on the compiler front
      end). By writing a compiler in Java to translate a variant of C into Java bytecode,
      you are also expected to gain a good understanding of important fundamental
      principles in object-oriented programming languages and develop further your
      programming and software engineering skills.
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP2511 or COMP2911<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3131
    title: Programming Languages and Compilers
  COMP3142:
    course_code: COMP3142
    description: 'Software plays an important role in our daily life. It is important
      to construct robust, operational software, especially under limited development
      budget and time constraints. To address this problem, a thorough verification
      and validation process is needed.

      In this course, you will study classic and modern techniques for the automated
      testing and analysis of software systems for reliability, security, and performance.
      Throughout the course, you will gain insight into a spectrum of software quality
      assurance techniques, including but not limited to fuzz testing and symbolic
      execution. These techniques will be not only studied but also applied in real-world
      scenarios, providing practical skills that are highly relevant in the ever-evolving
      landscape of software development.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP1531, COMP2511 and COMP2521<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3142
    title: Software Testing and Quality Assurance
  COMP3153:
    course_code: COMP3153
    description: It is virtually impossible to guarantee the correctness of a system,
      and in turn the absence of bugs by standard software engineering practice such
      as code review, systematic testing and good software design alone. The formal
      methods community has developed various rigorous, mathematically sound techniques
      and tools that allow the automatic analysis of systems and software. The application
      of these fully automatic techniques is typically called algorithmic verification.
      The course will describe several automatic verification techniques, the algorithms
      they are based on, and the tools that support them. We will discuss examples
      to which the techniques have been applied, and provide experience with the use
      of several tools.
    offering_terms: Term 3
    prerequisite_raw: 'Pre-requisite: MATH1081<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3153
    title: Algorithmic Verification
  COMP3161:
    course_code: COMP3161
    description: This course discusses and relates a range of programming language
      concepts and paradigms, including imperative, object-oriented, functional, concurrent
      &amp; parallel programming. It covers the theoretical foundations (syntax, operational,
      axiomatic and denotational semantics) as well as practical development and implementation
      aspects (dynamic and strong typing, polymorphism, overloading, automatic memory
      management, and concurrency). Due to the course topics, a variety of programming
      languages will be studied and used. Assignments will be in the programming language
      Haskell, but no previous knowledge of Haskell is assumed.
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP2521 or COMP1927<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3161
    title: Concepts of Programming Languages
  COMP3211:
    course_code: COMP3211
    description: Computer architecture plays a crucial role in defining and bridging
      the interface between hardware and software in a computer system. It serves
      as the foundation upon which both hardware and software components are designed
      and interact with each other. A study of computer architecture complements the
      study of programming language and algorithm, compiler, and operating system
      on the software side of the interface, and the study of embedded system, FPGA
      and VLSI design on the hardware side of the interface.
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP3222 or ELEC2141.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3211
    title: Computer Architecture
  COMP3222:
    course_code: COMP3222
    description: 'This course teaches students the fundamentals of digital design.

      The course introduces the components of digital systems, explains how these
      are described in the VHDL hardware description language and familiarizes the
      student with the implementation of digital circuits using FPGA prototyping boards.

      Â'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP2121 OR COMP1521 OR DPST1092<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3222
    title: Digital Circuits and Systems
  COMP3231:
    course_code: COMP3231
    description: 'Operating systems are an essential part of computer systems, so
      a course on operating systems is an essential part of any computer science or
      computer engineering program. This course provides an in-depth understanding
      of the underlying operating systems that students have implicitly relied upon
      when developing applications in the foundational courses within Computer Science
      and Engineering. The knowledge gained will continue to be relevant to your future
      career when developing systems and applications.

      In general terms, the course aims to educate students in the basic concepts,
      components and behaviours of modern monolithic operating systems,Â  including
      the relevant characteristics of hardware. Topics include processes, threads,
      concurrency, file systems, memory management and scheduling.

      Students will apply some of the concepts learnt by implementing parts of a realistic
      teaching operating system.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: (COMP1521 or DPST1092 or COMP2121 or ELEC2142)
      and (COMP2521 or COMP1927)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3231
    title: Operating Systems
  COMP3311:
    course_code: COMP3311
    description: 'This course aims to explore in depth the practice of developing
      database applications and the theory behind relational database systems. It
      will also give a very brief overview of the technologies used in implementing
      database management systems and the past, present and future of database systems.

      Large data resources are critical to the functioning of just about every significant
      modern computer application. Hence, knowledge of how to manage them is clearly
      important to the IT industry. In the context of further study, COMP3311 also
      provides a foundation for further study in advanced database topics, such asÂ COMP9312Â Graph
      Data Analytics, andÂ COMP9315Â Database Systems Implementation. Database concepts
      are also relevant in courses such asÂ COMP9319Â Web Data Compression and Search
      andÂ COMP6714Â Information Retrieval and Web Search.

      By the end of this course, we want you to be capable of building high-quality
      (correct and efficient) applications based on relational databases, to have
      a sound understanding of issues in managing relational database management systems,
      and an overview of how they work internally.'
    offering_terms: Term 1, Term 2
    prerequisite_raw: 'Prerequisite: COMP2521 or COMP1927<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3311
    title: Database Systems
  COMP3331:
    course_code: COMP3331
    description: 'This course is an introductory course on computer networks aimed
      at students with a computer science / electrical engineering background. You
      will focus on common paradigms and protocols used in present data communication.
      Through lectures, in-class activities, labs and assignments, you will learn
      the theory and application of:

      (1) Medium access control, congestion control, flow control, and reliable transmission,

      (2) Addressing and naming,

      (3) Routing and switching,

      (4) Widely used protocols such as Ethernet, IP, TCP, UDP, HTTP, etc.

      (5) Special-purpose networks including content delivery, peer-to-peer, and wireless
      networks, and

      (6) Security threats and standard defensive techniques

      This is a combined undergraduate and postgraduate course.Â The corresponding
      postgraduate course code is COMP9331.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521 or MTRN2500<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3331
    title: Computer Networks and Applications
  COMP3411:
    course_code: COMP3411
    description: COMP3411 is an introductory course on Artificial Intelligence covering
      fundamental topics such as autonomous agents, knowledge representation, problem
      solving, neural networks, reinforcement learning, optimisation, reasoning under
      uncertainty, vision and language processing.Â The course is taught with an orientation
      towards machine learning and with a view to practical applications of Artificial
      Intelligence using Python. Some AI applications that make use of foundational
      concepts will be demonstrated in lectures and tutorials.
    offering_terms: Term 1, Term 3
    prerequisite_raw: 'Prerequisite: COMP2521 or COMP1927<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3411
    title: Artificial Intelligence
  COMP3421:
    course_code: COMP3421
    description: 'In this course, you will delve into the fundamentals and practical
      aspects of Computer Graphics by working with game engines. You will gain knowledge
      and skills in developing 2D, 3D, and Virtual Reality-based graphical objects
      and environments using game engines including Unreal Engine and Unity. Additionally,
      you will understand computer graphics concepts, including lighting, reflection,
      static meshes, 2D Transforms, 3D Transforms, surface, texture maps, materials,
      cameras, object physical behaviors, collision detection, hierarchical modeling
      of objects, shaders, and rendering.

      The objectives of this course are to equip you with the necessary skills to
      work as a computer graphics expert in the industry, specifically in the development
      of optimal graphical interactive environments and game development. Through
      practical experience in computer graphics, you will also acquire the essential
      theoretical foundation that prepares you for a successful career in the industry.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP2511 or COMP2911<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3421
    title: Computer Graphics
  COMP3431:
    course_code: COMP3431
    description: 'An introduction to Intelligent agent design.  Picking actions using
      planning, learning or engineered control.  Both practical and theoretical components.  Practical
      component: Re-implement parts of a real agent architecture on a robot.  Assignment
      based.  Emphasis on engineering a working system.  Theoretical component: Introduction
      to a variety of research agent architectures including classical planning and
      reinforcement learning.  Lecture and lab based.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP2521 or COMP1927, and a WAM of at least 70<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3431
    title: Robotic Software Architecture
  COMP3511:
    course_code: COMP3511
    description: 'The course covers topics related to User Interface Design and Visual
      Design Principles. These topics aim to equip you with the skills necessary to
      design websites, mobile apps, and various software packages. You will learn
      how to apply a User-Centered Design process, which involves data collection
      from users, Requirement Analysis, Design, Prototyping, and Usability Evaluation.
      This process ensures that the final product is tailored to meet users&#39; needs.

      Additionally, the course covers other relevant topics that help you understand
      your users and their needs. These topics include an overview of basic Cognitive
      Capacities, Designing for Accessibility, Internationalization, Levels of Expertise,
      and Collaboration.

      Lastly, the course introduces you to new emerging technologies, such as the
      metaverse, immersive technologies, and smart devices. These technologies require
      special design considerations and will be explored in the course.

      By the end of the course, you will have gained a comprehensive understanding
      of User Interface Design and Visual Design Principles, along with the ability
      to apply User-Centered Design methodologies and adapt products to meet users&#39;
      needs.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: Completion of 48 UOC<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3511
    title: Human Computer Interaction
  COMP3601:
    course_code: COMP3601
    description: In this project-based course, students will be put into groups and
      tasked with carrying out a real-world hardware/software co-design project. Throughout
      the course of the term, students will work over a series of established milestones
      which guide them towards completing a complex engineering challenge. Several
      required parameters for the engineering process, including a budget, will be
      provided to students. As a systems course, COMP3601 brings in and combines and
      contextualizes knowledge from your prior academic career, including skills from
      the software, digital, and electronic domains.
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: (DESN2000 OR COMP2121) AND COMP3222<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3601
    title: Design Project A
  COMP3821:
    course_code: COMP3821
    description: 'How can a practitioner of algorithms help small-scale producers
      interested in sustainable farming?Â How can you solve strategy games by efficiently
      using computation time and memory?Â What do the relationships between difficult
      problems imply about the existence of feasible solutions to them, from both
      theoretical and practical standpoints?

      In this course, you will learn the building blocks to develop problem-solving
      software in fields as diverse as finance, logistics, policy and entertainment.
      You will apply techniques including divide-and-conquer, greedy selection and
      dynamic programming in order to develop fast and accurate algorithms in contexts
      such as graphs and strings, and study reductions involving linear programming
      and NP-hard problems. You will also develop the ability to think critically
      and communicate about algorithms in terms of correctness and efficiency.

      Join us to become a more effective, persistent and creative problem solver.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: (Min mark of 70 in COMP1927 or COMP2521) and
      (min mark of 70 in MATH1081 or enrolment in 3785)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3821
    title: Extended Algorithm Design and Analysis
  COMP3891:
    course_code: COMP3891
    description: 'Operating systems are an essential part of computer systems, a course
      on operating systems is an essential part of any computer science or computer
      engineering program. This course provides an in-depth understanding of the underlying
      operating systems that students have implicitly relied upon when developing
      applications in the foundational courses within Computer Science and Engineering.
      The knowledge gained will continue to be relevant to your future career when
      developing systems and applications.

      In general terms, the course aims to educate students in the basic concepts,
      components and behaviours of modern monolithic operating systems, including
      the relevant characteristics of hardware. Topics include processes, threads,
      concurrency, file systems, memory management and scheduling.

      Students will apply some of the concepts learnt by implementing parts of a realistic
      teaching operating system.

      Compared to COMP3231, students in the extended course will attend additional
      advanced lectures, and be required to implement additional components of the
      teaching operating system.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: (COMP1521 or DPST1092 or COMP2121) and (COMP2521
      or COMP1927) and a WAM of at least 75<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3891
    title: Extended Operating Systems
  COMP3900:
    course_code: COMP3900
    description: 'Welcome to the Information Technology/Computer Science Project Course,
      where theory meets practice in a hands-on exploration of real-world projects.
      This course is designed to elevate your programming skills by immersing you
      in the intricacies of coding, debugging, and optimizing software solutions.
      As you apply your theoretical knowledge of information technology/computer science
      concepts, algorithms, and data structures, you will actively bridge the gap
      between theoretical understanding and practical implementation. This course
      places a strong emphasis on teamwork and effective collaboration. You will navigate
      the challenges of group projects, learning to work seamlessly with peers, share
      responsibilities, and communicate ideas effectively. Beyond coding and collaboration,
      the course offers a comprehensive exploration of project management skills.
      From planning and scheduling to task prioritization, you will gain insights
      into the entire project lifecycle, experiencing firsthand the evolution of a
      concept into a completed solution. Furthermore, you will learn the art of crafting
      clear and concise project reports, and the skills to deliver impactful presentations.

      You will work in teams of ideally five (5) members to define, implement and
      evaluate a real-world project. Project teams meet weekly starting from Week
      1 with project mentors to report on the progress of the project. Assessment
      is based on a project proposal, progressive demonstrations and retrospectives,
      a final project demonstration and report, and on the quality of the software
      system itself. You will also get the chance to reflect on your own contributions
      and provide peer assessments of your team members.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1531, and COMP2521 or COMP1927, and enrolled
      in a BSc Computer Science major or Advanced Computer Science Honours major with
      completion of 102 UOC.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3900
    title: Computer Science Project
  COMP3901:
    course_code: COMP3901
    description: 'Ever wondered what it&#39;s like to do research? Have a burning
      desire to solve an interesting problem of your own?

      This course aims to give excellent students an opportunity to work closely with
      a member of academic staff in a small research project or a substantial development
      project.'
    offering_terms: Summer Term, Term 1, Term 2, Term 3
          of all first and second year core requirements a CSE program, and agreement
    prerequisite_raw: 'Prerequisite: 80+ WAM in COMP, SENG or BINF courses, completion
      of all first and second year core requirements a CSE program, and agreement
      from a suitable CSE academic supervisor.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3901
    title: Special Project A
  COMP3902:
    course_code: COMP3902
    description: 'Ever wondered what it&#39;s like to do research? Have a burning
      desire to solve an interesting problem of your own?

      This course aims to give excellent students an opportunity to work closely with
      a member of academic staff in a small research project or a substantial development
      project.'
    offering_terms: Term 1, Term 2, Term 3
          of all first and second year core requirements a CSE program, and agreement
    prerequisite_raw: 'Prerequisite: 80+ WAM in COMP, SENG or BINF courses, completion
      of all first and second year core requirements a CSE program, and agreement
      from a suitable CSE academic supervisor.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP3902
    title: Special Project B
  COMP4128:
    course_code: COMP4128
    description: 'How do competitive programmers solve complex problems in a matter
      of minutes?

      In this course, you will design and implement advanced algorithms to solve problems
      accurately and quickly. You will discover sophisticated applications of dynamic
      programming, data structures, graph algorithms, mathematics and more. Most importantly,
      you will learn to deconstruct a problem in order to evaluate which algorithm
      design techniques are appropriate, combining ideas from different contexts,
      and address the challenges that lie in both solving the problem conceptually
      and producing a C/C&#43;&#43; program which enacts your solution. Can you rise
      to the challenge?'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP3821, or (COMP3121 and a 75 WAM)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4128
    title: Programming Challenges
  COMP4141:
    course_code: COMP4141
    description: 'Computability: formal languages and problems, Turing Machines (TMs),
      computability, (semi-)decidability, universal TMs, Church-Turing thesis, halting
      problem, reduction and undecidability proofs, examples; Complexity: run time,
      space, complexity classes, non-determinism and NP, polynomial reductions and
      NP completeness, optimisation problems and approximation, randomisation; Languages
      and Automata: regular expressions and languages, finite automata, determinisation,
      context-free grammars and languages (CFLs), Chomsky normal form, word problems,
      pumping lemma, push-down automata, decidability problems for CFLs; Semantics
      and Correctness: while programs, assertions and program correctness, Hoare logic,
      loops and loop invariants, relative completeness of Hoare logic (and its role
      in a proof of GÃ¶del&#39;s incompleteness result)'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: MATH1081, and COMP1927 or COMP2521<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4141
    title: Theory of Computation
  COMP4161:
    course_code: COMP4161
    description: 'This course is about mechanical proof assistants, how they work,
      and what they can be used for. It presents specification and proof techniques
      used in industrial grade theorem provers, teaches the theoretical background
      to the techniques involved, and shows how to use a theorem prover to conduct
      formal proofs in practice. The courses is intended to bring third/fourth year
      and postgraduate students into contact with the current research topics in the
      field of theorem proving and automated deduction and to teach them the necessary
      skills to successfully use industrial grade verification environments in modelling
      and verification.

      Topics covered included: higher order logic, natural deduction, lambda calculus,
      term rewriting, data types and recursive functions, induction principles, calculational
      reasoning, mathematical proofs, decision procedures for a variety of logical
      domains, and proofs about programs.

      Note: experience with (first-order) logic and functional programming is required.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: Completion of 48 UOC<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4161
    title: Advanced Topics in Software Verification
  COMP4336:
    course_code: COMP4336
    description: This is a first course in wireless and mobile networking examining
      the fundamental theories as well as theÂ latest advances in wireless data and
      mobile communication networks. Topics include fundamentalÂ concepts in wireless
      coding, modulation, and signal propagation, WiFi and wireless local areaÂ networks,
      cellular networks, Bluetooth, and Internet of Things networks. The course will
      alsoÂ overview some of the emerging wireless networking concepts, such as wireless
      sensing, and droneassisted mobile networks. Hands-on experiments with mobile
      devices will be part of the learningÂ exercise, which involves wireless packet
      capture, analysis, and programming.
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP3331.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4336
    title: Mobile Data Networking
  COMP4337:
    course_code: COMP4337
    description: 'With the exponential growth of the Internet, the security of a network
      has become increasingly challenging. This subject will explore the security
      vulnerabilities in both fixed and wireless networks and cover the fundamental
      concepts and advanced issues with an emphasis on Internet architecture and protocols.
      The following key concepts will be covered: Network Security, Wireless Communications
      and Security overview, Review of Cryptographic techniques, 802.11 Technologies
      and legacy 802.11 Security: lessons learnt, Encryption Ciphers and Methods in
      Wireless Networks, Wireless Security Risks, Wireless Security Auditing and Pen
      Test Introduction,Â  IPSec and IP layer security,Â  PKI/X.509, Transport Layer
      Security/DTLS,Â Â  Intrusion Detection and Protection Systems, Network Firewalls,
      Secure Neighbor Discovery, Routing and Forwarding Security,Â  Authentication
      and Key Management in Wireless Networks/Sensor Networks,Â Â  Broadcast Security
      and Secure Code Dissemination,Â  Network Privacy and Anonymity, Security and
      Privacy in Location Services, Smart Grid, andÂ Anomaly Detection for Network
      Security.'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP3331.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4337
    title: Securing Fixed and Wireless Networks
  COMP4418:
    course_code: COMP4418
    description: "How can you timetable classes to fit everyoneâ\x80\x99s schedule?\
      \ Could tactical voting occur in Australia? Can a single computer program play\
      \ and solve both sudoku and minesweeper?\nKnowledge Representation and Reasoning\
      \ is at the core of Artificial Intelligence and the corresponding course, COMP4418,\
      \ will equip you to answer the questions above and many other ones, so-called\
      \ combinatorial problems. To do so, I will teach you â\x80\x9Cmodelingâ\x80\x9D\
      , that is the representation of a problem and its solutions in a way the computer\
      \ can understand. You will learn a declarative programming language, a paradigm\
      \ in which one only specifies what constitutes a solution to a problem and then\
      \ leaves it to the computer to actually find the solution. With this new tool\
      \ under your belt, you can effectively tackle most combinatorial problems coming\
      \ your way, from planning and scheduling to digital circuit synthesis or some\
      \ limited program verification."
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP3411<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4418
    title: Knowledge Representation and Reasoning
  COMP4601:
    course_code: COMP4601
    description: 'COMP4601 is a team-based project development course involving the
      analysis, design and implementation of embedded, high performance or low power
      FPGA-based accelerators using high-level synthesis.

      Students study concepts through lectures and guided lab exercises during the
      first half of the course, when project teams are also formed and the project
      goals are developed. During the second half of the course, students are involved
      in presenting group seminars and working on their projects. The course finishes
      with final project presentations.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP3211 and COMP3601.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4601
    title: Design Project B
  COMP4920:
    course_code: COMP4920
    description: 'In this course we will explore ethical issues for computer science,
      widely conceived. We will examine in detail the nature of ethical claims/moral
      judgements themselves, and how it is that our beliefs about their nature can
      affect our understanding of the ethical issues relating to computer science
      that we have examined so far. We will learn about ethical arguments, and how
      to construct and evaluate them.

      We will cover normative ethics, and run test cases past real-world computer
      science cases. We will learn to engage critically with research ethics, as well
      as the relationship between ethical responsibility and AI frameworks and innovation.
      There will be considerable discussion of &#34;ethics washing&#34; - the pretence
      of ethical reasoning by those in positions of power for the purpose of avoiding
      regulation, as well as explore the related issues of trust, accountability,
      and privacy in our current online, informationalised world.

      We will explore equity, bias and fairness in algorithmic and dataset design,
      as well as the ethics of AI more broadly. We will also explore the ethical ramifications
      of transparency and explainability - along with their attendant relationships
      with power, as they relate to computer science in general.'
    offering_terms: Term 1, Term 3
    prerequisite_raw: 'Prerequisite: Completion of 18 UOC of COMP**** courses<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4920
    title: Professional Issues and Ethics in Information Technology
  COMP4951:
    course_code: COMP4951
    description: 'The thesis provides an opportunity for you to bring together engineering
      principles learned over the previous years of study, and apply these principles
      to innovatively solve problems, which may include the development of specific
      designs and/or the investigation of various research hypotheses. Thesis projects
      are complex, open-ended problems that allow room for your creativity, and the
      acquisition, analysis, and interpretation of results. Typically, the project
      you work with will have multiple possible solutions or conclusions and sufficient
      complexity to require a degree of project planning. The thesis requires you
      to formulate problems in engineering terms, manage an engineering project and
      find solutions by applying engineering methods. You will also develop an ability
      to work in a research and development environment.

      Through the thesis, you will put into practice the knowledge and skills that
      you&#39;ve learned in your study up to this point. You do this by investigating
      a research topic, developing a significant software/hardware system, or some
      combination of these.

      Undergraduate theses consist of a single piece of work spread over three courses:
      Part A, Part B and Part C.

      In Thesis A, you will carry out a literature review of your chosen thesis topic
      with the aim to identify a research and/or development gap. Based on the identified
      gap you will plan out the research and/or development work to be carried out
      in Thesis B and Thesis C.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: Enrolled in a CSE BE (Hons) programs, completion
      of 126 UOC and completion of 3rd year core.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4951
    title: Research Thesis A
  COMP4952:
    course_code: COMP4952
    description: 'The thesis provides an opportunity for you to bring together engineering
      principles learned over the previous years of study, and apply these principles
      to innovatively solve problems, which may includeÂ the development of specific
      designs and/or the investigation of various research hypotheses. Thesis projects
      are complex, open-ended problems that allow room for your creativity, and the
      acquisition, analysis, and interpretation of results. Typically, the project
      you work with will have multiple possible solutions or conclusions and sufficient
      complexity to require a degree of project planning. The thesis requires you
      to formulate problems in engineering terms, manage an engineering project and
      find solutions by applying engineering methods. You will also develop an ability
      to work in a research and development environment.

      Through the thesis, you will put into practice the knowledge and skills that
      you&#39;ve learned in your study up to this point. You do this by investigating
      a research topic, developing a significant software/hardware system, or some
      combination of these.

      Undergraduate theses consist of a single piece of work spread over three courses:
      Part A, Part B and Part C.

      Thesis B is a continuation of Thesis A. By the end of Thesis A, you are expected
      to have identified a research and/or development gap and have planned out the
      technical work to be carried out for Thesis B and Thesis C. In Thesis B, you
      will carry out the technical work that you have planned out in Thesis A. At
      the end of Thesis B, you will evaluate the progress of your work, and revise
      the work plan to be carried out in Thesis C based on your findings and progress
      in Thesis B.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP4951<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4952
    title: Research Thesis B
  COMP4953:
    course_code: COMP4953
    description: 'The thesis provides an opportunity for you to bring together engineering
      principles learned over the previous years of study, and apply these principles
      to innovatively solve problems, which may the development ofÂ  specific designs
      and/or the investigation of various research hypotheses. Thesis projects are
      complex, open-ended problems that allow room for your creativity, and the acquisition,
      analysis, and interpretation of results. Typically, the project you work with
      will have multiple possible solutions or conclusions and sufficient complexity
      to require a degree of project planning. The thesis requires you to formulate
      problems in engineering terms, manage an engineering project and find solutions
      by applying engineering methods. You will also develop an ability to work in
      a research and development environment.

      Through the thesis, you will put into practice the knowledge and skills that
      you&#39;ve learned in your study up to this point. You do this by investigating
      a research topic, developing a significant software/hardware system, or some
      combination of these.

      Undergraduate theses consist of a single piece of work spread over three courses:
      Part A, Part B and Part C.

      Thesis C is a continuation of Thesis B. In Thesis C, you will carry out the
      technical work that you have planned out at the end of Thesis B. You will also
      evaluate the merits of your technical work and report your findings.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP4952<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4953
    title: Research Thesis C
  COMP4961:
    course_code: COMP4961
    description: 'The thesis provides an opportunity for you to bring together computer
      science principles learned over the previous years of study, and apply these
      principles to innovatively solve problems such as the development of specific
      designs and/or the investigation of various research hypotheses. Thesis projects
      are complex, open-ended problems that allow room for your creativity, and the
      acquisition, analysis, and interpretation of results. Typically, the project
      you work with will have multiple possible solutions or conclusions and sufficient
      complexity to require a degree of project planning. The thesis requires you
      to formulate research problems, manage a computer science project and find solutions
      by applying computer science methods. You will also develop an ability to work
      in a research and development environment.

      Through the thesis, you will put into practice the knowledge and skills that
      you&#39;ve learned in your study up to this point. You do this by investigating
      a research topic, developing a significant software/hardware system, or some
      combination of these.

      Undergraduate theses consist of a single piece of work spread over three courses:
      Part A, Part B and Part C.

      In Thesis A, you will carry out a literature review of your chosen thesis topic
      with the aim to identify a research and/or development gap. Based on the identified
      gap you will plan out the research and/or development work to be carried out
      in Thesis B and Thesis C.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: Enrolment in 4515 Computer Science (Hons) or
      3779 Advanced Computer Science (Hons)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4961
    title: Computer Science Thesis A
  COMP4962:
    course_code: COMP4962
    description: 'The thesis provides an opportunity for you to bring together computer
      science principles learned over the previous years of study, and apply these
      principles to innovatively solve problems, which may include the development
      of specific designs and/or the investigation of various research hypotheses.
      Thesis projects are complex, open-ended problems that allow room for your creativity,
      and the acquisition, analysis, and interpretation of results. Typically, the
      project you work with will have multiple possible solutions or conclusions and
      sufficient complexity to require a degree of project planning.Â The thesis requires
      you to formulate research problems, manage a computer science project and find
      solutions by applying computer science methods. You will also develop an ability
      to work in a research and development environment.

      Through the thesis, you will put into practice the knowledge and skills that
      you&#39;ve learned in your study up to this point. You do this by investigating
      a research topic, developing a significant software/hardware system, or some
      combination of these.

      Undergraduate theses consist of a single piece of work spread over three courses:
      Part A, Part B and Part C.

      Thesis B is a continuation of Thesis A. By the end of Thesis A, you are expected
      to have identified a research and/or development gap and have planned out the
      technical work to be carried out for Thesis B and Thesis C. In Thesis B, you
      will carry out the technical work that you have planned out in Thesis A. At
      the end of Thesis B, you will evaluate the progress of your work, and revise
      the work plan to be carried out in Thesis C based on your findings and progress
      in Thesis B.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP4961<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4962
    title: Computer Science Thesis B
  COMP4963:
    course_code: COMP4963
    description: 'The thesis provides an opportunity for you to bring together computer
      science principles learned over the previous years of study, and apply these
      principles to innovatively solve problems, which may include the development
      of specific designs and/or the investigation of various research hypotheses.
      Thesis projects are complex, open-ended problems that allow room for your creativity,
      and the acquisition, analysis, and interpretation of results. Typically, the
      project you work with will have multiple possible solutions or conclusions and
      sufficient complexity to require a degree of project planning. The thesis requires
      you to formulate research problems, manage a computer science project and find
      solutions by applying computer science methods. You will also develop an ability
      to work in a research and development environment.

      Through the thesis, you will put into practice the knowledge and skills that
      you&#39;ve learned in your study up to this point. You do this by investigating
      a research topic, developing a significant software/hardware system, or some
      combination of these.

      Undergraduate theses consist of a single piece of work spread over three courses:
      Part A, Part B and Part C.

      Thesis C is a continuation of Thesis B. In Thesis C, you will carry out the
      technical work that you have planned out at the end of Thesis B. You will also
      evaluate the merits of your technical work and report your findings.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP4962<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP4963
    title: Computer Science Thesis C
  COMP6080:
    course_code: COMP6080
    description: 'COMP6080 aims to develop your confidence in building modern web-based
      applications to industry standards. This occurs through the introduction of
      a range of basic concepts surrounding HTML, CSS, Vanilla Javascript, Javascript
      Declarative Frameworks, UI/UX Principles, Accessibility, Network &amp; Asynchronous
      Programming, Front-end Testing, and other basic infrastructure.

      This course has a heavy emphasis on industry voices, and as such a number of
      lectures will be given by current front-end developers from industry. These
      lectures primarily come from employees at Canva, a Sydney-based technology company
      that does a lot of work with front-end technologies.

      COMP6080 is a challenging course. Front-end development is unlike most things
      you&#39;ve experienced at university before. You will find the individual problems
      you solve much simpler than other level 6 courses, but the time you will feel
      that you spend on the aggregate of these issues will feel larger. A number of
      students will find this course quite time consuming if they&#39;re hoping to
      achieve a high mark. We&#39;d encourage you to reflect on this fact before you
      enrol in the course.'
    offering_terms: Term 1, Term 3
    prerequisite_raw: 'Prerequisite: COMP1531 AND (COMP2521 OR COMP1927)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6080
    title: Web Front-End Programming
  COMP6131:
    course_code: COMP6131
    description: This course is designed to provide a systematic exploration of automated
      source code analysis and verification techniques, with the aim of gaining hands-on
      experience in implementing code analysis tools to identify common yet important
      software vulnerabilities in software systems. By taking this course, students
      can put static analysis and verification theories and advanced techniques into
      practice. They will be able to build source code analysis toolsÂ (e.g., written
      in C&#43;&#43;) based on modern compilers and popular open-source frameworks
      to scan, comprehend and detect programming mistakes and vulnerabilities with
      the purpose of enhancing code quality and security.
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: MATH1081 and (COMP6771 or a mark of at least
      70 in COMP2521)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6131
    title: Software Security Analysis
  COMP6420:
    course_code: COMP6420
    description: The functionality and capability of computer systems are defined
      by their real-world hardware. Likewise, the cyber security of computer systems
      depends upon the security of their underlying hardware. This course is designed
      to explore both hardware security and trust in hardware, providing a comprehensive
      understanding of the underpinning principles and practices which apply to both
      the security and trust of computer systems, from printed circuit boards to individual
      integrated circuits. Students will explore theoretical aspects of what makes
      hardware secure, performing threat modelling and understanding the state of
      the art in hardware security, as well as get hands-on experience with practical
      attacks and defences for both virtual and real-world hardware. The course will
      equip students with the skills necessary to analyse, design, and implement hardware
      security measures.Â Â
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP1521 and (COMP3222 or ELEC2141)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6420
    title: Hardware Security
  COMP6441:
    course_code: COMP6441
    description: "In this introductory cybersecurity course we look at Security Engineering\
      \ â\x80\x93 the engineering principles behind designing, monitoring, and maintaining\
      \ security in the face of an adversary.Â  We explore selected case studies and\
      \ examine the practical principles behind effective security. We introduce the\
      \ fundamental ideas of security and then we look at how these are applied in\
      \ current cyber security practice. We will pay particular attention to systems\
      \ which fail and the importance of thinking like an attacker. This course involves\
      \ analysis, critical thinking and design. A cunning and devious mind will be\
      \ an asset. Although our main concern is cybersecurity, the engineering principles\
      \ we cover apply to security more generally.\nThis course introduces modern\
      \ cybersecurity design and practice, and is suitable for anyone with a playful\
      \ analytical mind and a general interest in security. We do not assume a programming\
      \ background.Â  We concentrate on analytical skills and the engineering approach\
      \ to security design. We&#39;ll also bring you up to date with the current main\
      \ trends in cybersecurity.\nIn this course you will undertake an applied self\
      \ directed security project.Â \nThere is an associated &#34;Extended&#34; version\
      \ of this course (COMP6841) for students who can code, ideally in C, and who\
      \ know low level concepts such as memory implementation and function calling.Â \
      \ CSE students will probably choose to take the extended version but are not\
      \ required to.Â  You can transfer between the courses before the census date\
      \ if you can&#39;t make up your mind in advance which is better for you.\nThe\
      \ precise topics covered in this introductory course change somewhat from year\
      \ to year to keep the coverage up-to-date and relevant. As you will see cybersecurity\
      \ has recently been and remains a rapidly changing field. The field is now way\
      \ too big for us to cover everything in detail in just one course but by the\
      \ end of course you will have an overview of the major topics in contemporary\
      \ security, a good understanding of the current state of play, and have begun\
      \ to think like a security engineer.\nAfter completing either of these courses\
      \ (COMP6441 or COMP6841) you can proceed to the other UNSW Computing Security\
      \ Courses covering topics in:\nDigital forensicsPenetration testingMemory corruption\
      \ and exploitationSoftware assuranceIncident responseMalware analysis and reversingCryptanalysisCloud\
      \ SecurityProfessional issues and leadership in securityWeb application securityFun\
      \ special projectsMasterclass\nOur intention is to make this a highly enjoyable\
      \ course. The field is quite stimulating as the security mindset you will develop\
      \ involves understanding know how to break things in creative ways (as well\
      \ as how to create things as is traditional in computing) with puzzles, cunning,\
      \ cloak-and-dagger antics and a never-ending supply of amusing stories. However\
      \ it will not be an easy course â\x80\x93 you are expected to master the underlying\
      \ theory *and* to be able to apply it to real world situations. There is a lot\
      \ to learn and we expect you to work hard and study in your own time."
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: Completion of 30 UOC<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6441
    title: Security Engineering and Cyber Security
  COMP6443:
    course_code: COMP6443
    description: 'Web applications are currently the predominant source of software
      vulnerabilities exploited in in online attacks. There is a growing need and
      growing demand for web programmers to be security aware.

      This course covers the main types of web application vulnerabilities and current
      best practice professional coding and testing practices to be able to successfully
      develop secure web applications.

      The course covers OWASP vulnerabilities cross site scripting browser security
      model and weaknesses Injection attacks DNS Man in the middle Data leakage Spoofing
      UI and Social vulnerabilities Assurance and Testing Standards. Course coverage
      will be constantly updated over time to reflect emerging vulnerabilities and
      practices.

      A programming background is not required but it will be helpful in some of the
      more applied topics. Students need a keen devious and analytical mind. To get
      the most from this course students will need to engage in independent study
      and research and be able to act as independent self directed learners.'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP6441 or COMP6841 or COMP3441<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6443
    title: Web Application Security and Testing
  COMP6445:
    course_code: COMP6445
    description: 'The subject of Digital Forensics is a blend of technical expertise,
      legal procedures for an expert witness, persuasive report writing and your performance
      in the theatre of court. This course covers both forensic theory / professional
      practice, and looks at the underlying engineering of hiding, finding, interpreting
      and responding to traces. Students will use standard forensic tools to extract,
      carve and analyse data as well as learning the low-level technical skills and
      knowledge underlying them. Students will also be introduced to advanced topics
      such as Cloud Forensics and latest anti-forensics techniques.

      By the end of the course students should be able to write and analyse simple
      forensic tools as well as being able to use them. The course covers Memory Forensics,
      Disc Forensics Network, Device Forensics, Stealth Techniques, Anti-forensics,
      Professional Forensic Practice, (chain of custody, records etc), Logging, and
      Mobile Forensics.

      Students of this course will apply forensic methods in controlled environments
      and gain an understanding of the technical process of uncovering hidden data
      and other metadata which may reveal user behaviour. Students will also develop
      skills in reporting their findings and evaluate the ethical consequences of
      their findings. Digital Forensics students are invited to participate in a mock
      courtroom experience involving testimony and cross-examination of digital forensics
      expert witnesses. We plan to run a mock civil trial of company vs rogue employee
      with a presiding Judge, and lecturers acting as advocates and students as expert
      witnesses.

      COMP3445/COMP6445 and COMP6845 run in an overlapping mode. All share a set of
      common activities and assessments; however COMP6845 students have additional
      extension activities and assessments related to digital forensics.Â'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP3441 or COMP6441 or COMP6841<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6445
    title: Digital Forensics
  COMP6447:
    course_code: COMP6447
    description: 'This course looks at cyber attack and defence. Students learn how
      to assess and identify vulnerabilities and how vulnerabilities are exploited.
      Students learn the principles and theory of exploitation, the common security
      models, and how approaches to exploitation and defence have evolved over time.

      Students from this course will engage in wargames, analyse real world case studies
      of vulnerabilities in complex software used on widespread systems, and gain
      an understanding of the technical process of finding and fixing low-level software
      vulnerabilities and also of the economics and causal factors involved with their
      real world use.

      The course covers techniques and skills including vulnerability classes, source
      code auditing,fuzzing, security bugs, software security assurance, taint analysis,
      memory corruption, overflows and return oriented programming. The course coverage
      will be constantly updated over time to reflect emerging attack and defence
      methods.

      There are numerous formative assessments and activities throughout the course
      to provide feedback and learning opportunities.

      Students need a keen, devious and analytical mind.

      To get the most from this course you will need to engage in independent study
      and act as a self-directed learner. Attending lectures alone will not be sufficient
      to pass the course. You will need to devote considerable practice to all the
      techniques we cover and read further on topics which interest you or which you
      do not fully understand. For a credit level result we expect you will spend
      15 hours per week in total on this course.

      Seek feedback from your friendly lecturers, tutors and class peers constantly
      over the term and closely monitor yourself to make sure you are not falling
      behind. Experience has shown that students who do not work hard at the course
      do not do well, and often express disappointment later on at the missed opportunity.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: A mark of at least 65 in COMP6841, or a mark
      of at least 75 in COMP6441, or COMP3441, or COMP1337<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6447
    title: System and Software Security Assessment
  COMP6448:
    course_code: COMP6448
    description: This is an occasional course that will involve a visiting expert
      in the security area giving a series of lectures and workshops on an advanced
      topic in cyber security. The course may run in intensive mode or in standard
      in-person weekly or fortnightly mode. The precise timing and topics depend on
      the visitor.
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP6841 or COMP6441 or COMP1337<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6448
    title: Security Engineering Masterclass
  COMP6452:
    course_code: COMP6452
    description: 'This course addresses the knowledge that is needed in order to build
      applications based on blockchain technology, by offering an architectural view
      of software systems that make beneficial use of it. It provides guidance on
      assessing the suitability of blockchain, on the roles blockchain can play in
      an architecture, on designing blockchain applications, and on assessing different
      architecture designs and tradeoffs. It also serves as a reference on blockchain
      design patterns and design analysis, and refers to practical examples of blockchain-based
      applications.

      The course covers the following:

      a general introduction to the topic and to existing blockchain platforms including
      Bitcoin, Ethereum, and Hyperledger Fabric, and offers examples of blockchain-based
      applications;the functional aspects of software architecture are covered, describing
      the main roles blockchain can play in an architecture, as well as its potential
      suitability and design process;non-functional aspects of blockchain applications,
      which are often cross-cutting concerns including cost estimation, performance,
      security;

      Real-world use cases will be covered, offering additional insights from a practical
      perspective.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP1531 AND (COMP2521 OR COMP1927)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6452
    title: Software Architecture for Blockchain Applications
  COMP6453:
    course_code: COMP6453
    description: 'Cryptography is an indispensable tool for protecting information
      in computer systems. This course is designed to provide an understanding of
      Cryptographic algorithms and Cryptanalysis with an aim of using them to protect
      computer systems, networks, and data protection. The course will emphasise on
      the foundational aspects of encryption and authentication techniques with an
      aim to use them correctly and effectively in applications.

      The technical ideas to be understood include classical algorithms, symmetric
      key encryption algorithms, public key encryption algorithms, cryptanalysis of
      ciphers, hash functions, digital signatures, zero-knowledge proofs. Applications
      like credential management, computing on encrypted data in cloud (homomorphic
      encryption, secure multiparty computation), secure network protocols, blockchains
      will be explored. Post quantum Cryptography will also be studied.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP2521 and (MATH1081 or MATH2400)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6453
    title: Applied Cryptography
  COMP6713:
    course_code: COMP6713
    description: 'Natural language processing (NLP) is a branch of artificial intelligence
      that deals with computational approaches used to process text.

      Human language (i.e., natural language) is inherently ambiguous. Ambiguity resolution
      using computational techniques is at the heart of NLP. As a result, the advancements
      in NLP can be visualized as three generations: rule-based, statistical and neural.
      The course introduces the three generations of NLP through the philosophy of
      ambiguity resolution being the core task of NLP. The content covers different
      NLP sub-problems (such as POS tagging, sentiment classification, named entity
      recognition, machine translation and summarisation), and typical approaches
      in the three generations to tackle these sub-problems.

      With recent advancements in large language models, there has been a renewed
      interest in NLP from industry and research alike. However, NLP precedes large
      language models. The exposition of NLP centered around ambiguity resolution
      helps to develop an understanding of the past and the present of NLP.'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: MATH1081 and (COMP3411 or COMP9444)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6713
    title: Natural Language Processing
  COMP6714:
    course_code: COMP6714
    description: 'Information retrieval (IR) is the process of retrieving relevant
      information by specifying a query to an IR system. Web search is one form of
      an IR system that allows users to search information on the Internet based on
      a search engine. It involves computing a numeric score on how well each result
      matches the query and ranking the results according to this score.

      This course aims to introduce the concepts, theories, and algorithmic issues
      important to Information Retrieval. If time allows, the course will also cover
      some recent topics and common practices. The course is composed of the following
      parts:

      Information Retrieval:

      Document modelingInverted index construction and compressionVector space model
      and ranking methodsProbabilistic and language modelsEvaluation methodsRelevance
      feedback and query expansion.

      Web Search:

      Web search engine architectureWeb crawling and indexingWeb structure and usage
      analytics.

      The lecture materials will be complemented by a non-programming assignment and
      a programming project.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: (MATH1081 and (COMP1531 or COMP2041)) or (COMP1927
      or COMP2521)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6714
    title: Information Retrieval and Web Search
  COMP6733:
    course_code: COMP6733
    description: 'Internet of Things (IoT) will play a key role in the next frontier
      of computing. The new course will provide an experimental approach to cover
      fundamental design principles behind building IoT applications, with hands-on
      experience with a popular end-to-end IoT platform.Topics covered will include
      a selection from: IoT technology and services, IoT system architecture, Low
      Power communications (Bluetooth Low Energy and 6LoWPAN) and security issues,
      sensors and sensor data smoothing and filtering, light-weight in-situ machine
      learning and data fusion, inertial sensing, activity recognition and fitness
      morning, voice-based stress level sensing, biometric authentication, anonymity,
      cloud services for IoT. This course assumes familiarity with a high level programming
      language such as Java and Python AND a low level programming language such as
      C.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: 65 WAM and COMP3331<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6733
    title: Internet of Things Experimental Design Studio
  COMP6771:
    course_code: COMP6771
    description: 'COMP6771 is an advanced programming course teaching practical aspects
      of intermediate/advanced C&#43;&#43; programming. The course focuses on teaching
      the fundamentals of C&#43;&#43;, followed by exploring powerful abstractions
      that C&#43;&#43; enables. This course focuses on using abstractions as well
      as building abstractions.

      COMP6771 is focused on modern, practical programming methods and tools. This
      course is designed for latter year CSE students with a reasonable degree of
      programming competencies.

      The course is heavily supported by Christopher Di Bella , a UNSW CSE graduate
      who is a well-regarded expert on C&#43;&#43;. His knowledge and expertise assists
      in forming and updating the course.

      Our aim for students who complete this course satisfactorily is that they are
      highly competent in understanding C&#43;&#43; and it&#39;s core features, being
      able to build complex programs, data structures, and algorithms with C&#43;&#43;,
      and being ready to immediately move into the workforce in areas that rely heavily
      on C&#43;&#43;.

      Students will need to work consistently each week to maintain progress through
      the course.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP2511 or COMP2911<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6771
    title: Advanced C++ Programming
  COMP6841:
    course_code: COMP6841
    description: "In this introductory cybersecurity course we look at Security Engineering\
      \ â\x80\x93 the engineering principles behind designing, monitoring, and maintaining\
      \ security in the face of an adversary.Â  We explore selected case studies and\
      \ examine the practical principles behind effective security. We introduce the\
      \ fundamental ideas of security and then we look at how these are applied in\
      \ current cyber security practice. We will pay particular attentionÂ to systems\
      \ which fail and the importance of thinking like an attacker. This course involves\
      \ analysis, critical thinking and design. A cunning and devious mind will be\
      \ an asset. Although our main concern is cybersecurity, the engineering principles\
      \ we cover apply to security more generally.\nThis course introduces modern\
      \ cybersecurity design and practice, and is suitable for anyone with a playful\
      \ analytical mind and a general interest in security.Â  We assume knowledge\
      \ of coding, ideally in C, and knowledge of low level computing concepts such\
      \ as memory implementation and function calling.Â  The course provides an introduction\
      \ to applied cyber security, as well asÂ analytical skills and taking an engineering\
      \ approach to security design. We&#39;ll also bring you up to date with the\
      \ current main trends in cybersecurity.\nIn this course you will undertake an\
      \ applied self directed security project.Â \nThere is an associated &#34;Core&#34;\
      \ version of this course (COMP6441).Â  CSE students will probably choose to\
      \ take this Extended course rather than the core course, but are not required\
      \ to.Â  You can transfer between these two associated courses before the census\
      \ date if you can&#39;t make up your mind in advance which is better for you.\n\
      This extended course is the core course plus additional applied technical material.\n\
      After completing COMP6841 you can proceed to the other UNSW Computing Security\
      \ Courses covering topics in:\nDigital forensicsPenetration testingMemory corruption\
      \ and exploitationSoftware assuranceIncident responseMalware analysis and reversingCryptanalysisProfessional\
      \ issues and leadership in securityWeb application securitySpecial projectsMasterclass\n\
      The precise topics covered in this course will change from year to year to keep\
      \ the coverage up-to-date and relevant. The field is now too big for us to cover\
      \ everything in detail in a single course but by the end of this course you\
      \ will have an overview of the major topics in contemporaryÂ security,Â a good\
      \ understandingÂ of the current state of the field,Â andÂ have begunÂ to think\
      \ like a security engineer.\nOur intention is toÂ make this a highly enjoyable\
      \ course. The field is quite stimulating as the security mindset you will develop\
      \ involves understanding know how to break things in creative ways (as well\
      \ as how to create things as is traditional in computing) with puzzles, cunning,\
      \ cloak-and-dagger antics and a never-ending supplyÂ of amusing stories. However\
      \ it will not be an easy course â\x80\x93 you are expected to master the underlying\
      \ theory *and* to be able to apply it to real world situations. There is a lot\
      \ to learn and we expect you to work hard and study it in your own time."
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: Completion of 30 UOC, and COMP1927 or COMP2521<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6841
    title: Extended Security Engineering and Cyber Security
  COMP6843:
    course_code: COMP6843
    description: 'Web applications are currently the predominant source of software
      vulnerabilities exploited in in online attacks. There is a growing need and
      growing demand for web programmers to be security aware.

      This course covers the main types of web application vulnerabilities and current
      best practice professional coding and testing practices to be able to successfully
      develop secure web applications.

      The course covers OWASP vulnerabilities cross site scripting browser security
      model and weaknesses Injection attacks DNS Man in the middle Data leakage Spoofing
      UI and Social vulnerabilities Assurance and Testing Standards. Course coverage
      will be constantly updated over time to reflect emerging vulnerabilities and
      practices.

      There are numerous formative assessments and activities throughout the course
      to provide feedback and learning opportunities. These do not directly contribute
      to your final grade but are expected to be used to provide evidence of your
      capabilities in your portfolio.

      A programming background is required. Students also need a keen devious and
      analytical mind. To get the most from this course students will need to engage
      in independent study and research and be able to act as independent self directed
      learners.

      This is the extended version of COMP6443. This course includes the material
      of COMP6443, a deeper coverage of vulnerability classifications, and more sophisticated
      exploitation techniques. Students will work in teams to conduct application
      tests and report on them to real and simulated clients.'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP6441 or COMP6841 or COMP3441 or COMP1337<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6843
    title: Extended Web Application Security and Testing
  COMP6845:
    course_code: COMP6845
    description: 'The subject of Digital Forensics is a blend of technical expertise,
      legal procedures for an expert witness, persuasive report writing and your performance
      in the theatre of court. This course covers both forensic theory / professional
      practice, and looking at the underlying engineering of hiding, finding, interpreting
      and responding to traces. Students will use of standard forensic tools to extract,
      carve and analyse data as well as learning the low-level technical skills and
      knowledge underlying them. Students will also be introduced to advanced topics
      such as Cloud Forensics and latest anti-forensics techniques.

      COMP3445/COMP6445 and COMP6845 run in an overlapping mode. All share a set of
      common activities and assessments; however COMP6845 students have additional
      extension activities and assessments related to digital forensics. These activities
      offer more advanced and in-depth study of the topic. The main differences between
      COMP3445/COMP6445 and COMP6845 derive from the motivations behind them, unlike
      other SECedu courses, the extended course (COMP6845) is not only exclusively
      a more technical deep dive into core (COMP3445/COMP6445) concepts but also exposes
      students to advanced topics in Digital Forensics such as Splunk Logging, iOS
      and Android Forensics. Hence, COMP6845 includes a deeper dive into more technical
      aspects of digital forensics. This is achieved through additional weekly workshop/lecture
      during the term.

      By the end of the course students should be able to write and analyse simple
      forensic tools as well as being able to use them. The course covers Memory Forensics,
      Disc Forensics Network, Device Forensics, Stealth Techniques, Anti-forensics,
      Professional Forensic Practice, (chain of custody, records etc), Logging, and
      Mobile Forensics. Students of this course will apply forensic methods in controlled
      environments and gain an understanding of the technical process of uncovering
      hidden data and other metadata which may reveal user behaviour. Students will
      also develop skills in reporting their findings and evaluate the ethical consequences
      of their findings. Digital Forensics students are invited to participate in
      a mock courtroom experience involving testimony and cross-examination of digital
      forensics expert witnesses. We plan to run a mock civil trial of company vs
      rogue employee with a presiding Judge, and lecturers acting as advocates and
      students as expert witnesses.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP3441 or COMP6441 or COMP6841 or COMP1337<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6845
    title: Extended Digital Forensics and Incident Response
  COMP6991:
    course_code: COMP6991
    description: 'This course aims to provide commentary and critique on the practice
      of programming, and the tooling used to program (primarily programming languages
      themselves). A variety of programming concepts across many programming languages
      are examined, including: syntax, typing, polymorphism, documentation, testing,
      meta-programming, concurrency, parallelism, safety, and more. The Rust programming
      language is used as a reference language to teach considerations behind these
      concepts. As a language commonly cited to be well-considered, it serves as a
      good foundation to help students understand where countless other languages
      may let them down. Lectures will compare and contrast Rust with other languages,
      but will also discuss where Rust can similarly let students down.

      While proficiency in writing Rust programs is an important learning outcome
      of this course, of greater importance is the ability to write more robust programs
      in whichever language a student happens to be using at the time.

      Per the advice of previous students, COMP6991 is aÂ difficultÂ course with a
      considerable workload. Please reach out to us either on the course forum or
      the course email if you&#39;re not sure whether COMP6991 is right for you.'
    offering_terms: Term 1, Term 3
    prerequisite_raw: COMP1927 or COMP2521<br/><br/>
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP6991
    title: Solving Modern Programming Problems with Rust
  COMP9242:
    course_code: COMP9242
    description: "The course provides students with a deep understanding of modern\
      \ operating system technology, implementation techniques and research issues.\n\
      This course builds upon the basic operating systems course (COMP3231/9201/3891/9283),\
      \ which provides an understanding of the underlying operating systems which\
      \ students have implicitly relied upon in developing applications in foundational\
      \ courses within Computer Science and Engineering, and will rely on in their\
      \ future careers when developing systems and applications. Advanced operating\
      \ systems enables students to specialise in operating systems, giving them the\
      \ skills and insight to become highly competent developers or researchers in\
      \ the general â\x80\x9Csystemsâ\x80\x9D space, including operating systems,\
      \ network systems, embedded and cyberphysical systems. The course produces graduates\
      \ who are highly sought-after by technology companies and systems researchers."
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: a mark of at least 75 in either COMP3231 or COMP3891.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9242
    title: Advanced Operating Systems
  COMP9301:
    course_code: COMP9301
    description: 'Ever wondered what it&#39;s like to do cyber security research?
      Have a burning desire to solve an interesting security problem of your own?

      This course aims to give self motivated students an opportunity to complete
      a small research project or a substantial development project under the supervision
      of an academic member of the school and co-supervision of an industry expert
      as appropriate.

      Interested students are advised to contact and submit an expression of interest
      to potential supervisors and seek their approval to enrol well in advance of
      the start of term.'
    offering_terms: Term 2, Term 3
    prerequisite_raw: (COMP6441 OR COMP6841 OR COMP1337) AND (COMP6443, or COMP6843,
      or COMP6445, or COMP6845, or COMP6447) AND enrolled in final year of program<br/><br/>
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9301
    title: Cyber Security Project
  COMP9302:
    course_code: COMP9302
    description: 'Ever wondered what it&#39;s like to do cyber security research?
      Have a burning desire to solve an interesting security problem of your own?

      This course aims to give self motivated students an opportunity to complete
      a substantial research project or a large development project under the supervision
      of an academic member of the school and co-supervision of an industry expert
      as appropriate.Â 

      Interested students are advised to contact and submit an expression of interest
      to potential supervisors and seek their approval to enrol well in advance of
      the start of term.'
    offering_terms: Term 2, Term 3
    prerequisite_raw: 'For undergrad: (COMP6441 OR COMP6841 or COMP1337) AND (COMP6443,
      or COMP6843, or COMP6445, or COMP6845, or COMP6447) AND enrolled in final year
      of program.<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9302
    title: Cyber Security Project B
  COMP9312:
    course_code: COMP9312
    description: 'Graphs are ubiquitous and are widely used to capture relationships
      between different entities in real-world applications. However, compared with
      traditional sequential data (e.g. text and audio), the unstructured property
      and the sparsity make processing big graphs very challenging. The course will
      introduce a series of data structures and algorithms for graph processing in
      terms of database (i.e., big data) and deep learning. Fundamental methods and
      the state-of-the-art research works will be integrated. The course will serve
      as a launching pad for those interested in graph analytics, big data processing
      and graph neural networks.

      Data structures and algorithms are the building blocks of many complex systems
      and software. Certain fundamental graph algorithms such as Dijkstra&#39;s algorithm
      and depth-first search have been covered by many text books and compulsory courses.
      They may be discussed in terms of pseudocode and time complexity. This course
      will start from studying how to efficiently implement the fundamental algorithms
      in big graphs. Then, the course explores more challenging and more complex algorithms
      step-by-step. When dealing with big graphs, we may consider various scenarios
      such as external memory solutions, distributed solutions, multi-core solutions,
      etc.

      The course also puts some attention to graph neural networks, which is a hotspot
      in the area of AI and deep learning. The course will not study theoretical details
      about machine learning and deep learning but just introduce several representative
      graph neural networks. The students will play with basic graph learning tasks
      and understand learning-based techniques for graph problems such as link prediction
      and node classification.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521, and COMP3311<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9312
    title: Data Analytics for Graphs
  COMP9313:
    course_code: COMP9313
    description: This course introduces the core concepts and technologies involved
      in managing Big Data. It will first introduce the characteristics of big data
      and big data analysis. Then, we will learn the open-source big data management
      framework Hadoop. We will mainly focus on Hadoop MapReduce programming. YARN,
      HDFS, HBase, and Hive will be briefly introduced as well. We will also learn
      an open-source memory-based distributed computing framework Spark. Another major
      focus of this course is algorithm design on large-scale data sets based on big
      data management frameworks, in various domains such as data stream mining, graph
      data processing, and finding similar items.
    offering_terms: Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521, and COMP3311<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9313
    title: Big Data Management
  COMP9315:
    course_code: COMP9315
    description: 'This course aims to introduce students to the detailed internal
      structure of database management systems (DBMSs) such as Oracle or SQL Server.
      DBMSs contain a variety of interesting data structures and algorithms that are
      also potentially useful outside the DBMS context; knowing about them is a useful
      way of extending your general programming background. While the focus is on
      relational DBMSs, given that they have the best-developed technological foundation,
      we will also consider more recent developments in the management of large data
      repositories.

      Relational DBMSs need to deal with a variety of issues: storage structures and
      management, implementation of relational operations, query optimisation, transactions,
      concurrency, recovery, security. The course will address most of these, along
      with a brief look at emerging database systems trends. The level of detail on
      individual topics will vary; some will be covered in significant detail, others
      will be covered relatively briefly.

      An important aspect of this course is to give you a chance to explore the internals
      of aÂ realÂ DBMS: PostgreSQL. Lectures will discuss the general principles of
      how DBMSs are implemented, and will also illustrate them with examples from
      PostgreSQL where possible. Since DBMSs are very large pieces of software, it
      won&#39;t be possible to explore the entire PostgreSQL system in depth.'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521, and COMP3311<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9315
    title: Database Systems Implementation
  COMP9319:
    course_code: COMP9319
    description: 'As the amount of Web data increases, it is becoming vital to not
      only be able to search and retrieve this information quickly, but also to store
      it in a compact manner. This is especially important for mobile devices which
      are becoming increasingly popular. Without loss of generality, within this course,
      we assume Web data (excluding media content) will be in XML and its like (e.g.,
      HTML, JSON).

      If time allows, we may cover optional topics such as: streaming algorithms,
      text analytics, Web data optimization for mobile devices. The lecture materials
      will be complemented by two programming assignments and numerous tutorial-type,
      written exercises.'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP2521 or COMP1927<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9319
    title: Web Data Compression and Search
  COMP9321:
    course_code: COMP9321
    description: This course aims to introduce the student to core concepts and practical
      skills for engineering the data in Web-service-oriented data-driven applications.
      Specifically, the course aims to expose students to basic infrastructure for
      building data services on the Web, including techniques to access and ingest
      data in internal/external sources, develop software services to curate (e.g.
      extract, transform, correct, aggregate the data), develop services to apply
      various analytics and develop services to visualize the data to communicate
      effectively usingÂ data. The course uses the Python programming language as
      the practical basis for its modules. However, the concepts taught are universal
      and can be applied to any other web development language/framework.Â
    offering_terms: Term 1, Term 3
    prerequisite_raw: 'Prerequisite: (COMP1531 or COMP2041) and COMP3311<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9321
    title: Data Services Engineering
  COMP9334:
    course_code: COMP9334
    description: 'We live in a world that events do not happen instantly. It takes
      a certain amount of time to download a video from a server to your own mobile
      device. It takes a certain amount of time for a computer to finish the execution
      of an algorithm. The time to completion (or response time in performance analysis
      terminology) is a performance metric that computer scientists and computer engineers
      should be concerned about because no one wants to wait unnecessarily. If you
      can understand the factors that determine the response time, then you can influence
      those factors so that the response time becomes acceptable. This course will
      take a mathematical modelling and analytical approach to understand response
      time in computer systems and networks. The primary goal is to explore how mathematical
      modelling and mathematical methods can be used to model, analyse and design
      computer systems and networks so that they have good performance. There are
      three major topics that will be covered by this course:

      Queuing analysis (Note: Queues are important because they give rise to waiting
      time.)Discrete event simulationInteger programming for network design'
    offering_terms: Term 1
    prerequisite_raw: 'Prerequisite: COMP1511 OR DPST1091 OR COMP1917 OR COMP1921<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9334
    title: Capacity Planning of Computer Systems and Networks
  COMP9417:
    course_code: COMP9417
    description: 'Machine learning is the algorithmic approach to learning from data.
      The course also covers aspects of data mining, the application of machine learning
      to obtain insight from data. In this course machine learning algorithms are
      placed in the context of their theoretical foundations in order to understand
      their derivation and correct application. Machine learning also is an empirical
      science, where performance of algorithms must be rigorously evaluated on datasets.
      Completion of this course will contribute to further learning in advanced topics
      such as deep learning, bioinformatics, computer vision, and robotics. Topics
      covered in the course include: linear models for regression and classification,
      local methods (nearest neighbour), tree learning, kernel machines, neural networks,
      unsupervised learning, ensemble learning, and learning theory. To expand and
      extend the development of theory and algorithms presented in lectures, practical
      examples will be given in tutorials and programming tasks during the project.'
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: (MATH1081 and (COMP1531 or COMP2041)) or (COMP1927
      or COMP2521)<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9417
    title: Machine Learning and Data Mining
  COMP9418:
    course_code: COMP9418
    description: 'This course presents an in-depth study of statistical machine learning
      approaches. It aims to provide the student with a solid understanding of methods
      for learning and inference in structured probabilistic models, with a healthy
      balance of theory and practice. It will cover topics on the semantics of direct
      and undirected representations in probabilistic graphical models, exact and
      approximate inference, and learning of model parameters and structure.

      In this course, we will study a class of statistical inference models known
      as Probabilistic Graphical Models (PGMs). PGMs are a great example of how Computer
      Science and Statistics can work together. PGMs use graph data structures to
      represent domains with large amounts of variables and specialised algorithms
      for efficient inference over these graphical models. Therefore, PGMs have pushed
      the limits of probability theory to the scale and rate necessary to provide
      automated reasoning in modern AI systems.

      During this course, we will cover several graphical models, including Bayesian
      networks, Markov networks, Conditional Random Fields, Markov chains, Hidden
      Markov Models, Kalman Filters and Markov decision processes. We will have a
      clear understanding of how these models work as well as their main algorithms
      for inference and learning. We will also cover several algorithms used to learn
      parameters and make inferences such as Monte Carlo Markov Chains (MCMC), Gibbs
      Sampling, Viterbi and the Baum-Welch algorithms, among others.'
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: MATH5836 or COMP9417<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9418
    title: Advanced Topics in Statistical Machine Learning
  COMP9444:
    course_code: COMP9444
    description: 'Neural networks and deep learning play a critical role in pushing
      the boundaries of what AI can achieve, making them indispensable for various
      industries and applications. Their ability to learn and adapt from data has
      revolutionised many fields and opened up new opportunities for solving complex
      problems. This course provides an introduction to and deep exploration of neural
      networks and deep learning principles and practice.

      Topics chosen from: perceptrons, feedforward neural networks, backpropagation,
      deep convolutional networks, image processing; geometric analysis of trained
      networks; recurrent networks, language processing, semantic analysis, long short
      term memory; deep reinforcement learning; autoencoders, generative models, adversarial
      training; designing successful applications of neural networks; recent developments
      in neural networks and deep learning.'
    offering_terms: Term 1, Term 2
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521 or MTRN3500<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9444
    title: Neural Networks and Deep Learning
  COMP9447:
    course_code: COMP9447
    description: 'Applied workshop in an emerging area in cyber security.Â  Â The
      course is supervised and taught by relevant academics and/or industry experts
      in the field.Â  Areas vary depending on the availability of subject domain experts
      from time to time.Â  Examples of relevant areas include: Cloud Security, Cryptanalysis
      on commercial products, mainframe security audit and penetration testing.

      Â'
    offering_terms: Term 3
    prerequisite_raw: 'Prerequisite: COMP6441 or COMP6841 or COMP3441<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9447
    title: Security Engineering Workshop
  COMP9517:
    course_code: COMP9517
    description: Computer vision is the interdisciplinary scientific field that develops
      theories and methods allowing computers to extract high-level information from
      digital images or videos. From an engineering perspective it seeks to automate
      perceptual tasks normally performed by the human visual system. Generally, vision
      is difficult because it is an inverse problem, where only insufficient information
      is available about the objects of interest in the image data. Physics-based
      mathematical and statistical models as well as machine-learning methods are
      used to assist in the task. Current real-world applications are wide-ranging,
      and include optical character recognition, machine inspection, retail object
      recognition, 3D model building, remote sensing, medical imaging, autonomous
      driving, motion capture, surveillance, face recognition, biometrics, and many
      others. This course provides an introduction to fundamental concepts and an
      opportunity to develop a real-world application of computer vision.
    offering_terms: Term 1, Term 2, Term 3
    prerequisite_raw: 'Prerequisite: COMP2521 or COMP1927<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9517
    title: Computer Vision
  COMP9727:
    course_code: COMP9727
    description: Recommender systems are information filtering and search tools for
      providing personalized user interaction and navigation through a complex space
      of products or services. The aim is to address information overload by providing
      users with items that better meet their needs and support their decision making.
      Recommender systems are important commercial tools that are widely used by e-commerce
      and social media companies to drive sales and user engagement. This course will
      cover the basic types of recommender system, the main recommendation algorithms,
      and machine learning and natural language processing techniques used to support
      recommender systems.
    offering_terms: Term 2
    prerequisite_raw: 'Prerequisite: COMP1927 or COMP2521<br/><br/>'
    source_url: https://www.handbook.unsw.edu.au/undergraduate/courses/2025/COMP9727
    title: Recommender Systems
metadata:
  postgraduate_courses:
  - COMP9020
  - COMP9021
  - COMP9024
  - COMP9032
  - COMP9044
  - COMP9101
  - COMP9102
  - COMP9153
  - COMP9164
  - COMP9201
  - COMP9211
  - COMP9222
  - COMP9283
  - COMP9311
  - COMP9331
  - COMP9336
  - COMP9337
  - COMP9414
  - COMP9415
  - COMP9434
  - COMP9511
  - COMP9801
  - COMP9814
  - COMP9900
  - COMP9991
  - COMP9992
  - COMP9993
```