# VMware VCF 9 Upgrade Planner - HTML File Resurrection Guide

This document serves as a comprehensive guide to recreate the "VMware VCF 9 Upgrade Planner" web application HTML file. Its purpose is to ensure that the HTML file can be regenerated precisely, maintaining all its functionality, styling, and contextual information, without any loss of detail or introduction of new elements.

## Instructions for LLM:

Please generate the complete HTML code for the "VMware VCF 9 Upgrade Planner" web application. Adhere strictly to the following requirements:

1.  **Single HTML File:** The output must be a single, self-contained HTML file.

2.  **Exact Code Replication:** Recreate the HTML code *exactly* as provided in the `HTML Code to Recreate` section below. Do not modify any part of the HTML, CSS, or JavaScript. This includes preserving all comments, indentation, and line breaks.

3.  **Functionality Preservation:** Ensure all interactive features (drag-and-drop, version selection, upgrade path generation, reset, PDF export) are fully functional as per the provided code.

4.  **Theme and Styling:** The application's visual theme (Clarity-like, Tailwind CSS, Inter font, specific colors, rounded corners, shadows) must be identical.

5.  **External CDN Links:** All external CDN links for Tailwind CSS, Font Awesome, HTML2Canvas, and jsPDF must be included and correctly referenced.

6.  **No Additional Information:** Do not add any conversational text, explanations, or extraneous details outside of the HTML code itself. The response should *only* be the HTML content.

7.  **No Placeholders:** Ensure there are no `...` or similar placeholders in the generated code. It must be complete and runnable.

8.  **Contextual Awareness:** Understand that this is a tool for guiding VCF 9 upgrades, and its current state reflects all previous refinements, including the correct Aria Suite Lifecycle URL and the enhanced PDF export.

## HTML Code to Recreate:




<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VMware VCF 9 Upgrade Planner</title>
    <!-- Tailwind CSS CDN for styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome CDN for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css">
    <!-- HTML2Canvas CDN for capturing DOM for PDF -->
    <script src="https://unpkg.com/html2canvas"></script>
    <!-- jsPDF CDN for generating PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        /* Custom styles for the body to ensure Inter font is applied */
        body {
            font-family: 'Inter', sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }
        /* Basic styling for the message box - mimicking Clarity alerts */
        .message-box {
            position: fixed;
            top: 1rem;
            right: 1rem;
            padding: 0.75rem 1.25rem; /* py-3 px-5 */
            border-radius: 0.25rem; /* rounded-sm */
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* subtle shadow */
            z-index: 50;
            transition: opacity 0.3s ease-in-out;
            font-size: 0.875rem; /* text-sm */
            font-weight: 500; /* medium */
        }
        .message-box.success {
            background-color: #e6ffed; /* light green */
            color: #1a5e20; /* dark green text */
            border: 1px solid #34d399; /* green-500 border */
        }
        .message-box.error {
            background-color: #ffe6e6; /* light red */
            color: #8c1c1c; /* dark red text */
            border: 1px solid #ef4444; /* red-500 border */
        }
        .message-box.info {
            background-color: #e6f0ff; /* light blue */
            color: #1c4e80; /* dark blue text */
            border: 1px solid #3b82f6; /* blue-500 border */
        }

        /* Styles for the flowchart container */
        #vis-flowchart {
            display: flex; /* Use flexbox for vertical stacking */
            flex-direction: column; /* Stack items vertically */
            align-items: center; /* Center items horizontally */
            gap: 10px; /* Space between nodes and arrows */
            height: auto; /* Allow height to adjust to content */
            min-height: 500px; /* Increased minimum height for visibility */
            width: 100%; /* Make it flexible to fill parent width */
            margin: 0 auto; /* Center the flowchart horizontally */
            border: 1px solid #d1d5db; /* Gray-300 border, slightly thinner */
            border-radius: 0.25rem; /* rounded-sm, more subtle */
            margin-bottom: 1rem; /* mb-4 */
            overflow: auto; /* Allow scrolling if content overflows */
            background-color: #f1f0f0; /* bg-white-ish */
            padding: 20px; /* Add padding inside the container */
            box-sizing: border-box; /* Include padding in element's total width and height */
        }

        /* Styles for individual flowchart nodes - mimicking Clarity cards */
        .flowchart-node {
            background-color: #ffffff; /* White background for nodes */
            border: 2px solid #e0e0e0; /* Light gray border */
            box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); /* Subtle shadow */
            color: #333333; /* Darker text for readability */
            padding: 1rem 1.5rem; /* py-4 px-6, adjusted padding */
            border-radius: 0.25rem; /* rounded-sm */
            text-align: center;
            font-size: 1rem; /* base font size */
            font-weight: 500; /* medium weight */
            max-width: 800px; /* Max width for nodes */
            width: 100%; /* Ensure it takes full available width up to max-width */
            box-sizing: border-box; /* Include padding in element's total width and height */
            word-wrap: break-word; /* Ensure long words break */
        }

        /* Specific styles for start/end nodes - using Clarity status colors */
        .flowchart-node.start {
            background-color: #e6ffed; /* Clarity success light */
            border-color: #34d399; /* Clarity success dark */
            color: #1a5e20; /* Dark green text */
        }
        .flowchart-node.end {
            background-color: #e6ffed; /* Clarity success light */
            border-color: #34d399; /* Clarity success dark */
            color: #1a5e20; /* Dark green text */
        }
        .flowchart-node.end.highlight-error { /* For VxRail "Not Available" - Clarity danger */
            background-color: #ffe6e6; /* Clarity danger light */
            border-color: #ef4444; /* Clarity danger dark */
            color: #8c1c1c; /* Dark red text */
        }
        .flowchart-node.decision {
            background-color: #fffbe6; /* Clarity warning light */
            border-color: #f59e0b; /* Clarity warning dark */
            color: #6d4e00; /* Dark yellow text */
        }

        /* Styles for arrows */
        .flowchart-arrow {
            width: 2px; /* Line thickness */
            height: 40px; /* Length of the arrow line */
            background-color: #9e9e9e; /* Medium gray for arrows */
            position: relative;
            margin: 0 auto; /* Center the arrow */
        }
        .flowchart-arrow::after {
            content: '';
            position: absolute;
            bottom: -8px; /* Position at the end of the line */
            left: 50%;
            transform: translateX(-50%);
            width: 0;
            height: 0;
            border-left: 8px solid transparent; /* Arrowhead size */
            border-right: 8px solid transparent;
            border-top: 8px solid #9e9e9e; /* Arrowhead color */
        }

        /* Styles for the component palette container - Removed light blue background */
        #component-palette {
            padding: 1.5rem; /* Increased padding */
            border-radius: 0.25rem; /* Consistent rounding */
        }

        /* Styles for the individual component items in the palette */
        #component-palette > div {
            min-width: 150px; /* Ensure they have a minimum width */
            min-height: 40px; /* Ensure they have a minimum height */
            padding: 0.75rem 1rem; /* py-3 px-4 */
            border-radius: 0.25rem; /* Smaller rounded corners for items */
            font-size: 0.875rem; /* text-sm */
        }
        #component-palette select {
            padding: 0.25rem 0.5rem; /* py-1 px-2 */
            border-radius: 0.125rem; /* rounded-xs */
            font-size: 0.75rem; /* text-xs */
        }

        /* Style for the drop zone when it contains components */
        .drop-zone-filled {
            background-color: #f8f8f8; /* Very light gray, mimicking Clarity's subtle backgrounds */
            border-color: #e0e0e0; /* Lighter border */
        }

        /* Specific styles for selected components in the drop zone */
        #selected-components-container > div {
            background-color: #f0f8ff; /* Light blue for selected items */
            border-color: #a7d9f8; /* Medium blue border */
            color: #333333;
            padding: 0.75rem 1rem; /* Consistent padding with palette items */
            border-radius: 0.25rem;
            font-size: 0.875rem;
        }
        #selected-components-container select {
            background-color: #e0f2ff; /* Lighter blue for dropdown */
            border-color: #a7d9f8;
        }
        #selected-components-container .remove-component-btn {
            color: #ef4444; /* Red for remove icon */
        }

        /* Styles for PDF export elements - hidden off-screen */
        #pdf-components-summary {
            position: absolute;
            left: -9999px; /* Hide off-screen */
            top: -9999px;
            background-color: white;
            padding: 20mm; /* Large padding for PDF capture */
            width: 190mm; /* A4 width - 2*10mm margins */
            box-sizing: border-box;
            font-family: 'Inter', sans-serif;
            color: #333333;
        }

        #pdf-components-summary h2 {
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 20px;
            text-align: center;
        }

        #pdf-components-summary ul {
            list-style-type: disc;
            margin-left: 20px;
        }
        #pdf-components-summary li {
            font-size: 16px;
            margin-bottom: 5px;
        }
    </style>
</head>
<body class="min-h-screen bg-gray-100 antialiased flex flex-col">

    <!-- Header Section -->
    <header class="bg-white shadow-md p-4 rounded-b-lg">
        <div class="container mx-auto text-center"> <!-- Added text-center here -->
            <h1 class="text-3xl font-bold text-gray-800 mb-2">VMware VCF 9 Upgrade Planner</h1>
            <p class="text-gray-600">Plan your VMware Cloud Foundation 9.0 upgrade path.</p>
        </div>
    </header>

    <!-- Message Box Container -->
    <div id="message-box-container"></div>

    <!-- Main Content Area -->
    <main class="flex-grow container mx-auto p-4 flex flex-col lg:flex-row gap-4">
        <!-- Component Selection (Left Pane) -->
        <section class="bg-white p-6 rounded-lg shadow-md lg:w-1/2 flex-shrink-0">
            <h2 class="text-xl font-semibold text-gray-700 mb-4">Component Selection</h2>
            <!-- Added more padding to the component palette itself -->
            <div id="component-palette" class="flex flex-wrap gap-4 p-4">
                <!-- Components will be rendered here by JavaScript -->
            </div>
        </section>

        <!-- Your Current Environment (Right Pane) -->
        <section id="drop-zone" class="bg-white p-6 rounded-lg shadow-md lg:w-1/2 border-2 border-dashed border-gray-300 flex flex-col items-center justify-center min-h-[200px] transition-colors duration-200">
            <h2 class="text-xl font-semibold text-gray-700 mb-4">Your Current Environment</h2>
            <!-- Changed to flex flex-wrap for dynamic columns -->
            <div id="selected-components-container" class="flex flex-wrap gap-4 w-full justify-center items-center h-full">
                <!-- "Drag and drop components here" should be in the centre of the Your environment box -->
                <p id="drop-zone-placeholder" class="text-gray-500 text-center flex-grow">Drag and drop components here</p>
            </div>
        </section>
    </main>

    <!-- Action Buttons -->
    <section class="container mx-auto p-4 flex flex-wrap justify-center gap-4">
        <!-- Buttons styled to mimic Clarity primary, secondary, and success buttons -->
        <button id="generate-path-btn" class="bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded-md shadow-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50">
            Show Upgrade Path
        </button>
        <button id="reset-btn" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-md shadow-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-gray-400 focus:ring-opacity-50 border border-gray-300">
            Reset
        </button>
        <button id="export-pdf-btn" class="bg-green-600 hover:bg-green-700 text-white font-semibold py-2 px-4 rounded-md shadow-sm transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-opacity-50">
            Export to PDF
        </button>
    </section>

    <!-- Hidden section for PDF export of selected components summary -->
    <div id="pdf-components-summary">
        <h2>Your Current Environment</h2>
        <ul id="pdf-components-list"></ul>
    </div>

    <!-- Upgrade Path (Bottom Section) -->
    <section id="upgrade-path-section" class="container mx-auto p-4 bg-white rounded-lg shadow-md mt-4 mb-8">
        <h2 class="text-xl font-semibold text-gray-700 mb-4">Upgrade Path (Flowchart)</h2>
        <div id="vis-flowchart">
            <!-- Flowchart will be rendered here by JavaScript using HTML/CSS -->
            <p id="flowchart-placeholder" class="text-gray-500 text-center pt-20">Select components and click "Show Upgrade Path" to see the flowchart.</p>
        </div>
        <h3 class="text-lg font-semibold text-gray-700 mt-8 mb-2">Overview (Text Summary)</h3>
        <div id="upgrade-overview" class="prose max-w-none text-gray-700">
            <!-- Overview text will also be rendered here -->
        </div>
    </section>

    <script>
        // Data for components available for selection
        const componentData = [
            { id: 'vCenter', name: 'vCenter', icon: 'fas fa-server', versions: ['7.0 U3', '8.0', '8.0U1', '8.0U2', '8.0U3', '9.0'] },
            { id: 'ESX', name: 'ESX', icon: 'fas fa-microchip', versions: ['7.0 U3', '8.0', '8.0U1', '8.0U2', '8.0U3', '9.0'] },
            { id: 'vSAN', name: 'vSAN', icon: 'fas fa-hdd', versions: ['7.0 U3', '8.0', '8.0U1', '8.0U2', '8.0U3', '9.0'] },
            { id: 'NSX', name: 'NSX', icon: 'fas fa-network-wired', versions: ['4.0.0.1', '4.0.1.1', '4.1.0.0', '4.1.1.0', '4.1.2.0', '4.2.0.0', '4.2.1.0'] },
            { id: 'TKGS', name: 'TKGS for vSphere', icon: 'fas fa-cubes', versions: [] },
            { id: 'AriaAutomation', name: 'Aria Automation', icon: 'fas fa-robot', versions: ['8.18.0', '8.18.1'] },
            { id: 'AriaOperations', name: 'Aria Operations', icon: 'fas fa-chart-line', versions: ['8.17.1', '8.17.2', '8.18.0', '8.18.1'] },
            { id: 'AriaOperationsLogs', name: 'Aria Operations for Logs', icon: 'fas fa-file-alt', versions: ['8.16.x', '8.18.x'] },
            { id: 'AriaOperationsNetworks', name: 'Aria Operations for Networks', icon: 'fas fa-project-diagram', versions: ['6.12.x', '6.13.x', '6.14.x'] },
            { id: 'SDDCManager', name: 'SDDC Manager', icon: 'fas fa-cloud', versions: ['4.x', '5.x'] },
            { id: 'VxRailManager', name: 'VxRail Manager', icon: 'fas fa-server', versions: ['7.x', '8.x'] },
            // New: Aria Suite Lifecycle
            { id: 'AriaSuiteLifecycle', name: 'Aria Suite Lifecycle', icon: 'fas fa-sync-alt', versions: ['8.12.x', '8.14.x', '8.16.x', '8.18.x'] },
        ];

        // State variables
        let selectedComponents = [];
        let upgradeSteps = []; // Array to hold structured upgrade steps for text overview
        let message = { type: '', text: '' };

        // DOM elements
        const componentPalette = document.getElementById('component-palette');
        const dropZone = document.getElementById('drop-zone');
        const selectedComponentsContainer = document.getElementById('selected-components-container');
        const dropZonePlaceholder = document.getElementById('drop-zone-placeholder');
        const generatePathBtn = document.getElementById('generate-path-btn');
        const resetBtn = document.getElementById('reset-btn');
        const exportPdfBtn = document.getElementById('export-pdf-btn');
        const flowchartContainer = document.getElementById('vis-flowchart'); // Renamed from visFlowchartContainer
        const flowchartPlaceholder = document.getElementById('flowchart-placeholder');
        const upgradeOverviewDiv = document.getElementById('upgrade-overview');
        const messageBoxContainer = document.getElementById('message-box-container');
        const pdfComponentsSummary = document.getElementById('pdf-components-summary');
        const pdfComponentsList = document.getElementById('pdf-components-list');


        // Helper function to compare version strings
        const compareVersions = (v1, v2) => {
            const normalize = (version) => {
                return version.replace(/U(\d)/g, '.0.$1').replace(/x/g, '999').split('.').map(Number);
            };

            const n1 = normalize(v1);
            const n2 = normalize(v2);

            for (let i = 0; i < Math.max(n1.length, n2.length); i++) {
                const num1 = n1[i] || 0;
                const num2 = n2[i] || 0;
                if (num1 > num2) return 1;
                if (num1 < num2) return -1;
            }
            return 0;
        };

        // Function to display messages in the message box
        const showMessage = (type, text) => {
            message = { type, text }; // Update internal state
            renderMessageBox(); // Re-render the message box

            setTimeout(() => {
                message = { type: '', text: '' }; // Clear internal state
                renderMessageBox(); // Clear message from DOM
            }, 5000);
        };

        // Renders the message box based on the 'message' state
        const renderMessageBox = () => {
            messageBoxContainer.innerHTML = ''; // Clear previous message
            if (message.text) {
                const msgBox = document.createElement('div');
                msgBox.className = `message-box ${message.type}`;
                msgBox.textContent = message.text;
                messageBoxContainer.appendChild(msgBox);
            }
        };

        // Renders the component palette on the left
        const renderComponentPalette = () => {
            componentPalette.innerHTML = '';
            componentData.forEach(component => {
                const div = document.createElement('div');
                div.className = "bg-white border border-gray-300 rounded-md p-3 flex items-center justify-between cursor-grab hover:shadow-lg transition-shadow duration-200"; // Adjusted for Clarity look
                div.draggable = true;
                div.dataset.componentId = component.id;
                div.dataset.initialVersion = component.versions[0] || '';

                div.innerHTML = `
                    <div class="flex items-center flex-grow min-w-0 overflow-hidden">
                        <div class="flex-shrink-0 w-6 text-center">
                            <i class="${component.icon} text-lg text-gray-600"></i>
                        </div>
                        <span class="font-medium text-gray-800 text-sm leading-tight ml-2">${component.name}</span>
                    </div>
                `;

                if (component.versions.length > 0) {
                    const select = document.createElement('select');
                    select.className = "ml-1 p-1 border border-gray-300 rounded-sm text-xs bg-white flex-shrink-0"; // Adjusted for Clarity look
                    select.dataset.componentId = component.id; // Add data attribute for ID
                    component.versions.forEach(version => {
                        const option = document.createElement('option');
                        option.value = version;
                        option.textContent = version;
                        select.appendChild(option);
                    });
                    select.value = component.versions[0]; // Set default selected version
                    select.addEventListener('change', (e) => handlePaletteVersionChange(component.id, e.target.value));
                    div.appendChild(select);
                }

                div.addEventListener('dragstart', (e) => onDragStart(e, component.id, div.querySelector('select')?.value || component.versions[0] || ''));
                componentPalette.appendChild(div);
            });
        };

        // Renders the selected components in the right pane
        const renderSelectedComponents = () => {
            selectedComponentsContainer.innerHTML = '';
            if (selectedComponents.length === 0) {
                dropZonePlaceholder.style.display = 'block';
                selectedComponentsContainer.appendChild(dropZonePlaceholder);
                dropZone.classList.remove('drop-zone-filled'); // Remove light grey background
                selectedComponentsContainer.classList.remove('justify-start', 'items-start'); // Revert alignment
                selectedComponentsContainer.classList.add('justify-center', 'items-center'); // Center placeholder
            } else {
                dropZonePlaceholder.style.display = 'none';
                selectedComponentsContainer.classList.remove('justify-center', 'items-center'); // Remove centering
                selectedComponentsContainer.classList.add('justify-start', 'items-start'); // Align items to start
                selectedComponents.forEach(component => {
                    const div = document.createElement('div');
                    div.className = "bg-blue-50 border border-blue-200 rounded-md p-3 flex items-center justify-between shadow-sm"; // Adjusted for Clarity look
                    div.innerHTML = `
                        <div class="flex items-center flex-grow min-w-0 overflow-hidden">
                            <div class="flex-shrink-0 w-6 text-center">
                                <i class="${component.icon} text-lg text-blue-700"></i>
                            </div>
                            <span class="font-medium text-gray-800 text-sm leading-tight ml-2">${component.name}</span>
                        </div>
                        <button class="text-red-500 hover:text-red-700 focus:outline-none remove-component-btn" data-component-id="${component.id}">
                            <i class="fas fa-times-circle text-base"></i>
                        </button>
                    `;

                    if (component.versions && component.versions.length > 0) {
                        const select = document.createElement('select');
                        select.className = "ml-1 p-1 border border-blue-300 rounded-sm text-xs bg-blue-100 flex-shrink-0"; // Adjusted for Clarity look
                        component.versions.forEach(version => {
                            const option = document.createElement('option');
                            option.value = version;
                            option.textContent = version;
                            select.appendChild(option);
                        });
                        select.value = component.version; // Set selected version
                        select.addEventListener('change', (e) => handleSelectedVersionChange(component.id, e.target.value));
                        div.querySelector('div > div').appendChild(select); // Append to the inner div
                    }

                    selectedComponentsContainer.appendChild(div);
                });
                dropZone.classList.add('drop-zone-filled'); // Add light grey background
                // Attach event listeners for remove buttons
                document.querySelectorAll('.remove-component-btn').forEach(button => {
                    button.addEventListener('click', (e) => {
                        const idToRemove = e.currentTarget.dataset.componentId;
                        removeComponent(idToRemove);
                    });
                });
            }
        };

        // Handle drag start event for component cards
        const onDragStart = (event, componentId, version) => {
            event.dataTransfer.setData('text/plain', JSON.stringify({ componentId, version }));
            event.dataTransfer.effectAllowed = 'move';
        };

        // Handle drag over event for the drop zone
        dropZone.addEventListener('dragover', (event) => {
            event.preventDefault();
            event.dataTransfer.dropEffect = 'move';
        });

        // Handle drop event for the drop zone
        dropZone.addEventListener('drop', (event) => {
            event.preventDefault();
            const data = JSON.parse(event.dataTransfer.getData('text/plain'));
            const { componentId, version } = data;

            // Prevent duplicate components
            if (selectedComponents.some((comp) => comp.id === componentId)) {
                showMessage('error', `${componentId} is already in your environment.`);
                return;
            }

            const component = componentData.find((c) => c.id === componentId);
            if (component) {
                let newVersion = version;
                // If vSAN is dropped and ESX is present, auto-match vSAN version
                if (componentId === 'vSAN') {
                    const esxComponent = selectedComponents.find((comp) => comp.id === 'ESX');
                    if (esxComponent) {
                        const esxVersion = esxComponent.version;
                        if (component.versions.includes(esxVersion)) {
                            newVersion = esxVersion;
                            showMessage('info', `vSAN version automatically set to match ESX version (${esxVersion}).`);
                        } else {
                            showMessage('info', `ESX version (${esxVersion}) is not available for vSAN. Please select a compatible vSAN version.`);
                        }
                    }
                }
                selectedComponents.push({ ...component, version: newVersion });
                renderSelectedComponents();
            }
        });

        // Handle version change for components in the palette (currently not directly used for adding)
        const handlePaletteVersionChange = (componentId, newVersion) => {
            // This function updates the version in the palette's dropdown, but the actual
            // version used when dragging is captured at dragstart.
            // If ESX version is changed in palette and vSAN is already in drop zone, update vSAN's version
            if (componentId === 'ESX') {
                const vSANComponentIndex = selectedComponents.findIndex(comp => comp.id === 'vSAN');
                if (vSANComponentIndex !== -1) {
                    const vSANComponent = componentData.find(c => c.id === 'vSAN');
                    if (vSANComponent.versions.includes(newVersion)) {
                        selectedComponents[vSANComponentIndex].version = newVersion;
                        showMessage('info', `vSAN version automatically updated to match new ESX version (${newVersion}).`);
                        renderSelectedComponents();
                    } else {
                        showMessage('warning', `New ESX version (${newVersion}) is not compatible with vSAN. Please adjust vSAN version manually.`);
                    }
                }
            }
        };

        // Handle version change for components in the selected environment
        const handleSelectedVersionChange = (componentId, newVersion) => {
            const index = selectedComponents.findIndex(comp => comp.id === componentId);
            if (index !== -1) {
                selectedComponents[index].version = newVersion;
                renderSelectedComponents(); // Re-render to reflect change

                // If ESX version is changed, check and update vSAN
                if (componentId === 'ESX') {
                    const vSANComponentIndex = selectedComponents.findIndex(comp => comp.id === 'vSAN');
                    if (vSANComponentIndex !== -1) {
                        const vSANComponent = componentData.find(c => c.id === 'vSAN');
                        if (vSANComponent.versions.includes(newVersion)) {
                            selectedComponents[vSANComponentIndex].version = newVersion;
                            showMessage('info', `vSAN version automatically updated to match new ESX version (${newVersion}).`);
                            renderSelectedComponents();
                        } else {
                            showMessage('warning', `New ESX version (${newVersion}) is not compatible with vSAN. Please adjust vSAN version manually.`);
                        }
                    }
                }
            }
        };

        // Handle removal of a component from the selected environment
        const removeComponent = (idToRemove) => {
            selectedComponents = selectedComponents.filter((comp) => comp.id !== idToRemove);
            renderSelectedComponents();
            // Clear upgrade path and overview
            upgradeSteps = [];
            renderFlowchart(); // Call renderFlowchart
            upgradeOverviewDiv.innerHTML = '';
        };

        // Reset all selections and generated content
        const handleReset = () => {
            selectedComponents = [];
            upgradeSteps = [];
            message = { type: '', text: '' };
            renderSelectedComponents();
            renderFlowchart(); // Call renderFlowchart
            upgradeOverviewDiv.innerHTML = '';
            renderMessageBox();
        };

        // Renders the flowchart using pure HTML and CSS
        const renderFlowchart = () => {
            flowchartContainer.innerHTML = ''; // Clear previous content

            if (upgradeSteps.length === 0) {
                flowchartPlaceholder.style.display = 'block';
                flowchartContainer.appendChild(flowchartPlaceholder);
                return;
            } else {
                flowchartPlaceholder.style.display = 'none';
            }

            upgradeSteps.forEach((step, index) => {
                // Create node element
                const nodeDiv = document.createElement('div');
                let nodeClasses = 'flowchart-node';
                if (index === 0) {
                    nodeClasses += ' start';
                } else if (index === upgradeSteps.length - 1) {
                    nodeClasses += ' end';
                    if (step.highlight) { // For VxRail "Not Available"
                        nodeClasses += ' highlight-error';
                    }
                }
                // Add decision node class if applicable (though currently no decision logic in steps)
                if (step.type === 'decision') {
                    nodeClasses += ' decision';
                }
                nodeDiv.className = nodeClasses;

                let nodeContent = `<strong>${step.title}</strong>`;
                // Only add description for the first node
                if (index === 0 && step.description) {
                    nodeContent += `<br/>${step.description}`;
                }
                nodeDiv.innerHTML = nodeContent;
                flowchartContainer.appendChild(nodeDiv);

                // Add arrow if not the last step
                if (index < upgradeSteps.length - 1) {
                    const arrowDiv = document.createElement('div');
                    arrowDiv.className = 'flowchart-arrow';
                    flowchartContainer.appendChild(arrowDiv);
                }
            });
        };


        // Helper to generate dynamic component list string for descriptions
        const getComponentListString = (componentIdsToInclude, selectedComponentsArray, separator = ', ') => {
            const components = [];
            componentIdsToInclude.forEach(id => {
                const selectedComp = selectedComponentsArray.find(c => c.id === id);
                if (selectedComp) {
                    const componentDisplayName = componentData.find(d => d.id === id)?.name || id;
                    components.push(`${componentDisplayName} ${selectedComp.version || ''}`);
                }
            });
            return components.join(separator);
        };

        // Generate the upgrade path (populates upgradeSteps array)
        const generateUpgradePath = () => {
            upgradeSteps = []; // Clear previous steps for text overview
            let overviewText = ''; // For the text summary
            showMessage('info', ''); // Clear previous message

            // --- Pre-rendering Validations ---
            if (selectedComponents.length === 0) {
                showMessage('info', 'Please add components to your environment to generate an upgrade path.');
                renderFlowchart(); // Ensure placeholder is shown
                upgradeOverviewDiv.innerHTML = '';
                return;
            }

            const hasSDDCManager = selectedComponents.some(c => c.id === 'SDDCManager');
            const hasVxRailManager = selectedComponents.some(c => c.id === 'VxRailManager');
            const hasvCenter = selectedComponents.some(c => c.id === 'vCenter');
            const hasESX = selectedComponents.some(c => c.id === 'ESX');
            const hasvSAN = selectedComponents.some(c => c.id === 'vSAN');
            const hasNSX = selectedComponents.some(c => c.id === 'NSX');
            const hasTKGS = selectedComponents.some(c => c.id === 'TKGS');
            const hasAriaAutomation = selectedComponents.some(c => c.id === 'AriaAutomation');
            const hasAriaOperations = selectedComponents.some(c => c.id === 'AriaOperations');
            const hasAriaOperationsLogs = selectedComponents.some(c => c.id === 'AriaOperationsLogs');
            const hasAriaOperationsNetworks = selectedComponents.some(c => c.id === 'AriaOperationsNetworks');
            const hasAriaSuiteLifecycle = selectedComponents.some(c => c.id === 'AriaSuiteLifecycle'); // New
            const hasAnyAriaOrTKGS = hasTKGS || hasAriaAutomation || hasAriaOperations || hasAriaOperationsLogs || hasAriaOperationsNetworks;


            const sddcManagerVersion = selectedComponents.find(c => c.id === 'SDDCManager')?.version;
            const vxRailManagerVersion = selectedComponents.find(c => c.id === 'VxRailManager')?.version;
            const esxVersion = selectedComponents.find(c => c.id === 'ESX')?.version;
            const vCenterVersion = selectedComponents.find(c => c.id === 'vCenter')?.version;
            const vSANVersion = selectedComponents.find(c => c.id === 'vSAN')?.version;
            const nsxVersion = selectedComponents.find(c => c.id === 'NSX')?.version;
            const ariaAutomationVersion = selectedComponents.find(c => c.id === 'AriaAutomation')?.version;
            const ariaOperationsVersion = selectedComponents.find(c => c.id === 'AriaOperations')?.version;
            const ariaOperationsLogsVersion = selectedComponents.find(c => c.id === 'AriaOperationsLogs')?.version;
            const ariaOperationsNetworksVersion = selectedComponents.find(c => c.id === 'AriaOperationsNetworks')?.version;
            const ariaSuiteLifecycleVersion = selectedComponents.find(c => c.id === 'AriaSuiteLifecycle')?.version; // New


            // Core components validation
            if (!hasSDDCManager && !hasVxRailManager && (!hasvCenter || !hasESX)) {
                showMessage('error', 'vCenter and ESX are core components required for VCF 9 upgrade paths in standalone scenarios. Please add them.');
                renderFlowchart();
                upgradeOverviewDiv.innerHTML = '';
                return;
            }

            // ESX and vSAN Version Match
            if (hasESX && hasvSAN && compareVersions(esxVersion, vSANVersion) !== 0) {
                showMessage('error', `ESX version (${esxVersion}) and vSAN version (${vSANVersion}) must be identical for upgrade. Please adjust.`);
                renderFlowchart();
                upgradeOverviewDiv.innerHTML = '';
                return;
            }

            // vCenter and ESX Version Compatibility
            if (hasvCenter && hasESX && !hasSDDCManager && !hasVxRailManager && compareVersions(vCenterVersion, esxVersion) < 0) {
                showMessage('error', `vCenter version (${vCenterVersion}) must be equal to or higher than ESX version (${esxVersion}). Please adjust.`);
                renderFlowchart();
                upgradeOverviewDiv.innerHTML = '';
                return;
            }

            // --- Initial Step: Current Environment Details ---
            const initialComponentsString = getComponentListString(
                selectedComponents.map(c => c.id),
                selectedComponents,
                ', '
            );
            upgradeSteps.push({ title: `Current Environment: ${initialComponentsString}`, description: 'Your existing VMware environment with selected components and their versions.', type: 'start' });


            // --- Path Scenarios ---

            // Scenario 1: Existing VCF Environment (SDDC Manager Present)
            if (hasSDDCManager) {
                overviewText += '## Existing VCF Environment Upgrade Path\n\n';
                overviewText += 'This path outlines the upgrade process for an existing VMware Cloud Foundation (VCF) environment managed by SDDC Manager.\n\n';

                upgradeSteps.push({ title: 'Perform VCF Pre-Upgrade Checks', description: 'Ensure your environment meets all prerequisites for the upgrade. This includes health checks, compatibility matrix validation, and backup procedures. Refer to the <a href="https://knowledge.broadcom.com/external/article/389563/essential-prechecks-for-a-successful-vcf.html" target="_blank" class="text-blue-500 underline">VCF Pre-Upgrade Checklist</a>.' });
                if (sddcManagerVersion === '4.x') {
                    upgradeSteps.push({ title: 'Upgrade SDDC Manager to 5.x', description: 'If your current SDDC Manager is version 4.x, an intermediate upgrade to 5.x is required before proceeding to VCF 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/upgrade-plan-for-vmware-cloud-foundation-5-2.html" target="_blank" class="text-blue-500 underline">VCF 5.x Upgrade Guide</a>.' });
                    const mgmtDomainComponents5x = getComponentListString(['vCenter', 'ESX', 'vSAN', 'NSX'], selectedComponents);
                    upgradeSteps.push({ title: 'Upgrade VCF 5.x Management Domain', description: `Upgrade the core components of your management domain (${mgmtDomainComponents5x}) to their VCF 5.x compatible versions. See <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/upgrade-plan-for-vmware-cloud-foundation-5-2.html" target="_blank" class="text-blue-500 underline">VCF 5.x Management Domain Upgrade</a>.` });
                }

                // New: Upgrade Aria Suite Lifecycle if present and older than 8.18.x
                if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') < 0) {
                    upgradeSteps.push({ title: `Upgrade Aria Suite Lifecycle to 8.18.x`, description: `Upgrade your Aria Suite Lifecycle to version 8.18.x. This is a prerequisite for managing Aria Suite products in VCF 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-suite-lifecycle/8-18/release-notes/vmware-aria-suite-lifecycle-818-release-notes.html" target="_blank" class="text-blue-500 underline">Aria Suite Lifecycle 8.18 Release Notes</a>.` });
                }

                upgradeSteps.push({ title: 'Upgrade SDDC Manager to 9.0', description: 'Upgrade the SDDC Manager appliance to the VCF 9.0 release. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/upgrade-the-management-domain-to-vmware-cloud-foundation-5-2/apply-cloud-foundation-5-2-update-bundle.html" target="_blank" class="text-blue-500 underline">VCF 9.0 SDDC Manager Upgrade Guide</a>.' });
                const mgmtDomainComponents90 = getComponentListString(['vCenter', 'ESX', 'vSAN', 'NSX'], selectedComponents);
                upgradeSteps.push({ title: 'Upgrade VCF 9.0 Management Domain', description: `Upgrade the management domain components (${mgmtDomainComponents90}) to VCF 9.0 compatible versions. TKGS for vSphere and Aria Suite components will be upgraded as part of the VCF BOM or re-integrated. For details, see <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/upgrade-the-management-domain-to-vmware-cloud-foundation-5-2.html" target="_blank" class="text-blue-500 underline">VCF 9.0 Management Domain Upgrade</a>.` });

                // New: Apply Aria Suite Lifecycle 8.18 Patch 2 if 8.18.x is present AND Aria Operations is present
                if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') >= 0 && hasAriaOperations) {
                    upgradeSteps.push({ title: 'Apply Aria Suite Lifecycle 8.18 Patch 2', description: 'Apply Patch 2 to Aria Suite Lifecycle 8.18.x. This patch enables the upgrade of Aria Operations to VCF Operations 9.0 and the installation of the VCF Operations Fleet Management Appliance. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-suite-lifecycle/8-18/release-notes/vmware-aria-suite-lifecycle-818-patch-2-release-notes.html" target="_blank" class="text-blue-500 underline">Aria Suite Lifecycle 8.18 Patch 2 Release Notes</a>.' });
                }

                // Aria and TKGS upgrades within VCF context
                if (hasAnyAriaOrTKGS) {
                    if (hasAriaOperationsNetworks) {
                        if (compareVersions(ariaOperationsNetworksVersion, '6.14.x') < 0) {
                            upgradeSteps.push({ title: 'Upgrade Aria Operations for Networks to 6.14.x', description: `Upgrade Aria Operations for Networks to version 6.14.x as an intermediate step. Consult the <a href="https://knowledge.broadcom.com/external/article/324468/aria-operations-for-network-6x-guide-and.html" target="_blank" class="text-blue-500 underline">Aria Operations for Networks Upgrade Guide</a>.` });
                        }
                        upgradeSteps.push({ title: 'Upgrade Aria Operations for Networks to 9.0.0', description: `Upgrade Aria Operations for Networks to version 9.0.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/phase-4-upgrade-additional-management-components-in-vcf-9.html" target="_blank" class="text-blue-500 underline">Aria Operations for Networks 9.0 Upgrade Guide</a>.` });
                    }
                    if (hasAriaAutomation) {
                        if (compareVersions(ariaAutomationVersion, '8.18.1') < 0) {
                            upgradeSteps.push({ title: 'Upgrade Aria Automation to 8.18.1', description: `Upgrade Aria Automation to version 8.18.1 as an intermediate step. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-automation/8-18/transition-guide-on-prem-master-map-8-18/migrating-tenants-using-aria-suite-lifecycle/prerequisites-for-tenant-migration/upgrading-to-vrealize-automation-8.html" target="_blank" class="text-blue-500 underline">Aria Automation Upgrade Guide</a>.` });
                            }
                        upgradeSteps.push({ title: 'Upgrade Aria Automation to 9.0.0', description: `Upgrade Aria Automation to version 9.0.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/phase-3-import-and-upgrade-aria-automation-8-to-vcf-automation-9/upgrade-to-vcf-automation.html" target="_blank" class="text-blue-500 underline">Aria Automation 9.0 Upgrade Guide</a>.` });
                    }
                    if (hasAriaOperations) {
                        let ariaOpsDesc = `Upgrade Aria Operations to version 9.0.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/upgrading-management-components/upgrade-to-vcf-operations.html" target="_blank" class="text-blue-500 underline">Aria Operations 9.0 Upgrade Guide</a>.`;
                        if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') >= 0) {
                            ariaOpsDesc += ' This upgrade path is enabled by applying Aria Suite Lifecycle 8.18 Patch 2.';
                        }
                        if (compareVersions(ariaOperationsVersion, '9.0') < 0) {
                             upgradeSteps.push({ title: 'Upgrade Aria Operations to 9.0.0', description: ariaOpsDesc });
                        }
                    }
                    if (hasAriaOperationsLogs) {
                        upgradeSteps.push({ title: 'Transition to VCF Operations for Logs 9.0', description: 'Transition your Aria Operations for Logs deployment to be compatible with VCF 9.0. This may involve specific migration steps. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-operations-for-logs/8-18.html" target="_blank" class="text-blue-500 underline">Aria Operations for Logs Documentation</a>.' });
                    }
                    if (hasTKGS) {
                        upgradeSteps.push({ title: 'Upgrade TKGS for vSphere', description: 'Upgrade TKGS for vSphere to be compatible with VCF 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-tanzu/standalone-components/tanzu-kubernetes-grid/2-5/tkg/index.html" target="_blank" class="text-blue-500 underline">TKGS Documentation</a>.' });
                    }
                }

                upgradeSteps.push({ title: 'Upgrade VCF 9.0 Workload Domains (Optional)', description: 'Upgrade your workload domains to VCF 9.0 as needed. This step is optional and depends on your environment\'s requirements. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/lifecycle-management/lifecycle-management-of-vcf-core-components/upgrade-workload-domains-to-vcf-5-2.html" target="_blank" class="text-blue-500 underline">VCF 9.0 Workload Domain Upgrade Guide</a>.' });

                upgradeSteps.push({ title: 'Perform VCF Post-Upgrade Validations', description: 'Conduct thorough post-upgrade checks to ensure the stability and functionality of your VCF 9.0 environment. See <a href="https://techdocs.broadcom.com/content/dam/broadcom/techdocs/us/en/assets/vmware-cis/vcf/vcf-9.0-operate-vcf-health-journey.pdf" target="_blank" class="text-blue-500 underline">VCF Post-Upgrade Validation</a>.' });

                upgradeSteps.push({ title: 'VCF 9.0 Environment', description: 'Your environment is now successfully upgraded to VMware Cloud Foundation 9.0.', type: 'end' });
            }
            // Scenario 2: VxRail Environment (VxRail Manager Present, No SDDC Manager)
            else if (hasVxRailManager && !hasSDDCManager) {
                overviewText += '## VxRail Environment Upgrade Path\n\n';

                if (vxRailManagerVersion === '7.x') {
                    upgradeSteps.push({ title: 'Upgrade VxRail Manager to 8.x', description: 'If your VxRail Manager is currently 7.x, upgrade it to 8.x. For detailed steps, refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/4-5/vcf-on-vxrail-complete-4-5.html" target="_blank" class="text-blue-500 underline">Dell EMC VxRail Documentation</a>.' });
                    upgradeSteps.push({ title: 'VCF 9.0 Upgrade Not Available', description: 'Currently, there is no direct upgrade path to VCF 9.0 for VxRail systems. Please consult Dell EMC documentation for the latest information and alternative solutions.', type: 'end', highlight: true }); // Highlight this step
                } else if (vxRailManagerVersion === '8.x') {
                    upgradeSteps.push({ title: 'VCF 9.0 Upgrade Not Available', description: 'Currently, there is no direct upgrade path to VCF 9.0 for VxRail systems. Please consult Dell EMC documentation for the latest information and alternative solutions.', type: 'end', highlight: true }); // Highlight this step
                }
            }
            // Scenario 3: Standalone Environment (No SDDC Manager, No VxRail Manager)
            else {
                overviewText += '## Standalone Environment Upgrade Path\n\n';
                overviewText += 'This path outlines the upgrade process for a standalone VMware environment to VMware Cloud Foundation (VCF) 9.0. This can involve either an "Import" path (if NSX is present) or a "Converge" path (if NSX is not present).\n\n';

                upgradeSteps.push({ title: 'Perform Pre-Upgrade Checks (Compatibility, Health)', description: 'Before any upgrade, conduct thorough checks to ensure all components are compatible and healthy. Refer to the <a href="https://interopmatrix.broadcom.com/Upgrade" target="_blank" class="text-blue-500 underline">Broadcom Compatibility Guide</a>.' });

                // New: Upgrade Aria Suite Lifecycle if present and older than 8.18.x
                if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') < 0) {
                    upgradeSteps.push({ title: `Upgrade Aria Suite Lifecycle to 8.18.x`, description: `Upgrade your Aria Suite Lifecycle to version 8.18.x. This is a prerequisite for managing Aria Suite products in VCF 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-suite-lifecycle/8-18/release-notes/vmware-aria-suite-lifecycle-818-release-notes.html" target="_blank" class="text-blue-500 underline">Aria Suite Lifecycle 8.18 Release Notes</a>.` });
                }

                if (hasNSX) { // Import Path
                    overviewText += '### Import Path (NSX Present)\n\n';

                    const importUpgrade8xComponents = getComponentListString(['vCenter', 'ESX', 'vSAN'], selectedComponents, '/');
                    if (importUpgrade8xComponents && ((hasvCenter && compareVersions(vCenterVersion, '8.0') < 0) ||
                        (hasESX && compareVersions(esxVersion, '8.0') < 0) ||
                        (hasvSAN && compareVersions(vSANVersion, '8.0') < 0))) {
                        upgradeSteps.push({ title: `Upgrade ${importUpgrade8xComponents} to 8.x (for Import)`, description: `If your ${importUpgrade8xComponents} versions are older than 8.x, upgrade them to an 8.x release as an intermediate step for VCF import. Find relevant guides at <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/upgrade-plan-for-vmware-cloud-foundation-5-2.html" target="_blank" class="text-blue-500 underline">Broadcom Upgrade Guides</a>.` });
                    }

                    if (nsxVersion && compareVersions(nsxVersion, '4.2.1.0') < 0) {
                        upgradeSteps.push({ title: 'Upgrade NSX to 4.2.1.0 (for Import)', description: 'If your NSX version is older than 4.2.1.0, upgrade it to this version as a prerequisite for VCF import. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-1/upgrade-nsx-for-vcf-5-2.html" target="_blank" class="text-blue-500 underline">Broadcom NSX Upgrade Guide</a>.' });
                    }

                    upgradeSteps.push({ title: 'Upgrade NSX to 9.0.0 (for Import)', description: 'Upgrade your NSX environment to version 9.0.0. For detailed steps, see <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/upgrade-the-management-domain-to-vmware-cloud-foundation-5-2/upgrading-nsx--to-version-9.html" target="_blank" class="text-blue-500 underline">Broadcom NSX 9.0 Upgrade Guide</a>.' });

                    // New: Apply Aria Suite Lifecycle 8.18 Patch 2 if 8.18.x is present AND Aria Operations is present
                    if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') >= 0 && hasAriaOperations) {
                        upgradeSteps.push({ title: 'Apply Aria Suite Lifecycle 8.18 Patch 2', description: 'Apply Patch 2 to Aria Suite Lifecycle 8.18.x. This patch enables the upgrade of Aria Operations to VCF Operations 9.0 and the installation of the VCF Operations Fleet Management Appliance. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-suite-lifecycle/8-18/release-notes/vmware-aria-suite-lifecycle-818-patch-2-release-notes.html" target="_blank" class="text-blue-500 underline">Aria Suite Lifecycle 8.18 Patch 2 Release Notes</a>.' });
                    }

                    if (hasAnyAriaOrTKGS) {
                        if (hasAriaOperationsNetworks) {
                            if (compareVersions(ariaOperationsNetworksVersion, '6.14.x') < 0) {
                                upgradeSteps.push({ title: 'Upgrade Aria Operations for Networks to 6.14.x', description: `Upgrade Aria Operations for Networks to version 6.14.x as an intermediate step. Consult the <a href="https://knowledge.broadcom.com/external/article/324468/aria-operations-for-network-6x-guide-and.html" target="_blank" class="text-blue-500 underline">Aria Operations for Networks Upgrade Guide</a>.` });
                            }
                            upgradeSteps.push({ title: 'Upgrade Aria Operations for Networks to 9.0.0', description: `Upgrade Aria Operations for Networks to version 9.0.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/phase-4-upgrade-additional-management-components-in-vcf-9.html" target="_blank" class="text-blue-500 underline">Aria Operations for Networks 9.0 Upgrade Guide</a>.` });
                        }
                        if (hasAriaAutomation) {
                            if (compareVersions(ariaAutomationVersion, '8.18.1') < 0) {
                                upgradeSteps.push({ title: 'Upgrade Aria Automation to 8.18.1', description: `Upgrade Aria Automation to version 8.18.1 as an intermediate step. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-automation/8-18/transition-guide-on-prem-master-map-8-18/migrating-tenants-using-aria-suite-lifecycle/prerequisites-for-tenant-migration/upgrading-to-vrealize-automation-8.html" target="_blank" class="text-blue-500 underline">Aria Automation Upgrade Guide</a>.` });
                            }
                            upgradeSteps.push({ title: 'Upgrade Aria Automation to 9.0.0', description: `Upgrade Aria Automation to version 9.0.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/phase-3-import-and-upgrade-aria-automation-8-to-vcf-automation-9/upgrade-to-vcf-automation.html" target="_blank" class="text-blue-500 underline">Aria Automation 9.0 Upgrade Guide</a>.` });
                        }
                        if (hasAriaOperations) {
                            let ariaOpsDesc = `Upgrade Aria Operations to version 9.0.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/upgrading-management-components/upgrade-to-vcf-operations.html" target="_blank" class="text-blue-500 underline">Aria Operations 9.0 Upgrade Guide</a>.`;
                            if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') >= 0) {
                                ariaOpsDesc += ' This upgrade path is enabled by applying Aria Suite Lifecycle 8.18 Patch 2.';
                            }
                            if (compareVersions(ariaOperationsVersion, '9.0') < 0) {
                                 upgradeSteps.push({ title: 'Upgrade Aria Operations to 9.0.0', description: ariaOpsDesc });
                            }
                        }
                        if (hasAriaOperationsLogs) {
                            upgradeSteps.push({ title: 'Transition to VCF Operations for Logs 9.0', description: 'Transition your Aria Operations for Logs deployment to be compatible with VCF 9.0. This may involve specific migration steps. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-operations-for-logs/8-18.html" target="_blank" class="text-blue-500 underline">Aria Operations for Logs Documentation</a>.' });
                        }
                        if (hasTKGS) {
                            upgradeSteps.push({ title: 'Upgrade TKGS for vSphere', description: 'Upgrade TKGS for vSphere to be compatible with VCF 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-tanzu/standalone-components/tanzu-kubernetes-grid/2-5/tkg/index.html" target="_blank" class="text-blue-500 underline">TKGS Documentation</a>.' });
                        }
                    }

                    upgradeSteps.push({ title: 'Import as VI Workload Domain into VCF 9.0', description: 'Import your existing virtual infrastructure as a VI Workload Domain into the new VCF 9.0 environment. For detailed instructions, see <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/building-your-private-cloud-infrastructure/working-with-workload-domains/import-an-existing-vcenter-to-create-a-workload-domain.html" target="_blank" class="text-blue-500 underline">VCF 9.0 Import Guide</a>.' });

                } else { // Converge Path (No NSX)
                    overviewText += '### Converge Path (No NSX Present)\n\n';

                    const convergeUpgrade8xComponents = getComponentListString(['vCenter', 'ESX', 'vSAN'], selectedComponents, '/');
                    if (convergeUpgrade8xComponents && ((hasvCenter && compareVersions(vCenterVersion, '8.0') < 0) ||
                        (hasESX && compareVersions(esxVersion, '8.0') < 0) ||
                        (hasvSAN && compareVersions(vSANVersion, '8.0') < 0))) {
                        upgradeSteps.push({ title: `Upgrade ${convergeUpgrade8xComponents} to 8.x (Intermediate)`, description: `If your ${convergeUpgrade8xComponents} versions are older than 8.x, upgrade them to an 8.x release as an intermediate step for VCF converge. Find relevant guides at <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/upgrade-plan-for-vmware-cloud-foundation-5-2.html" target="_blank" class="text-blue-500 underline">Broadcom Upgrade Guides</a>.` });
                    }

                    if (hasvCenter && compareVersions(vCenterVersion, '9.0') < 0) {
                        upgradeSteps.push({ title: 'Upgrade vCenter to 9.0 (for Converge)', description: 'Upgrade your vCenter Server to version 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/9-0/vcenter-upgrade.html" target="_blank" class="text-blue-500 underline">vCenter 9.0 Upgrade Guide</a>.' });
                    }

                    const convergeUpgrade90Components = getComponentListString(['ESX', 'vSAN'], selectedComponents, ' & ');
                    if (convergeUpgrade90Components && ((hasESX && compareVersions(esxVersion, '9.0') < 0) || (hasvSAN && compareVersions(vSANVersion, '9.0') < 0))) {
                        upgradeSteps.push({ title: 'Upgrade ESX Hosts & vSAN to 9.0 (for Converge)', description: `Upgrade your ESX hosts and vSAN to version 9.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/9-0/esx-upgrade.html" target="_blank" class="text-blue-500 underline">ESX/vSAN 9.0 Upgrade Guide</a>.` });
                    }

                    // New: Apply Aria Suite Lifecycle 8.18 Patch 2 if 8.18.x is present AND Aria Operations is present
                    if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') >= 0 && hasAriaOperations) {
                        upgradeSteps.push({ title: 'Apply Aria Suite Lifecycle 8.18 Patch 2', description: 'Apply Patch 2 to Aria Suite Lifecycle 8.18.x. This patch enables the upgrade of Aria Operations to VCF Operations 9.0 and the installation of the VCF Operations Fleet Management Appliance. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-suite-lifecycle/8-18/release-notes/vmware-aria-suite-lifecycle-818-patch-2-release-notes.html" target="_blank" class="text-blue-500 underline">Aria Suite Lifecycle 8.18 Patch 2 Release Notes</a>.' });
                    }

                    if (hasAnyAriaOrTKGS) {
                        if (hasAriaOperationsNetworks) {
                            if (compareVersions(ariaOperationsNetworksVersion, '6.14.x') < 0) {
                                upgradeSteps.push({ title: 'Upgrade Aria Operations for Networks to 6.14.x', description: `Upgrade Aria Operations for Networks to version 6.14.x as an intermediate step. Consult the <a href="https://knowledge.broadcom.com/external/article/324468/aria-operations-for-network-6x-guide-and.html" target="_blank" class="text-blue-500 underline">Aria Operations for Networks Upgrade Guide</a>.` });
                            }
                            upgradeSteps.push({ title: 'Upgrade Aria Operations for Networks to 9.0.0', description: `Upgrade Aria Operations for Networks to version 9.0.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/phase-4-upgrade-additional-management-components-in-vcf-9.html" target="_blank" class="text-blue-500 underline">Aria Operations for Networks 9.0 Upgrade Guide</a>.` });
                        }
                        if (hasAriaAutomation) {
                            if (compareVersions(ariaAutomationVersion, '8.18.1') < 0) {
                                upgradeSteps.push({ title: 'Upgrade Aria Automation to 8.18.1', description: `Upgrade Aria Automation to version 8.18.1 as an intermediate step. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-automation/8-18/transition-guide-on-prem-master-map-8-18/migrating-tenants-using-aria-suite-lifecycle/prerequisites-for-tenant-migration/upgrading-to-vrealize-automation-8.html" target="_blank" class="text-blue-500 underline">Aria Automation Upgrade Guide</a>.` });
                            }
                            upgradeSteps.push({ title: 'Upgrade Aria Automation to 9.0.0', description: `Upgrade Aria Automation to version 9.0.0. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/phase-3-import-and-upgrade-aria-automation-8-to-vcf-automation-9/upgrade-to-vcf-automation.html" target="_blank" class="text-blue-500 underline">Aria Automation 9.0 Upgrade Guide</a>.` });
                        }
                        if (hasAriaOperations) {
                            let ariaOpsDesc = `Upgrade Aria Operations to version 9.0.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/preparing-your-vcf-9-management-components/upgrading-management-components/upgrade-to-vcf-operations.html" target="_blank" class="text-blue-500 underline">Aria Operations 9.0 Upgrade Guide</a>.`;
                            if (hasAriaSuiteLifecycle && compareVersions(ariaSuiteLifecycleVersion, '8.18.x') >= 0) {
                                ariaOpsDesc += ' This upgrade path is enabled by applying Aria Suite Lifecycle 8.18 Patch 2.';
                            }
                            if (compareVersions(ariaOperationsVersion, '9.0') < 0) {
                                 upgradeSteps.push({ title: 'Upgrade Aria Operations to 9.0.0', description: ariaOpsDesc });
                            }
                        }
                        if (hasAriaOperationsLogs) {
                            upgradeSteps.push({ title: 'Transition to VCF Operations for Logs 9.0', description: 'Transition your Aria Operations for Logs deployment to be compatible with VCF 9.0. This may involve specific migration steps. Refer to the <a href="https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-operations-for-logs/8-18.html" target="_blank" class="text-blue-500 underline">Aria Operations for Logs Documentation</a>.' });
                        }
                        if (hasTKGS) {
                            upgradeSteps.push({ title: 'Upgrade TKGS for vSphere', description: 'Upgrade TKGS for vSphere to be compatible with VCF 9.0. Consult the <a href="https://techdocs.broadcom.com/us/en/vmware-tanzu/standalone-components/tanzu-kubernetes-grid/2-5/tkg/index.html" target="_blank" class="text-blue-500 underline">TKGS Documentation</a>.' });
                        }
                    }

                    upgradeSteps.push({ title: 'Use VCF Installer (Converge to VCF 9.0)', description: 'Utilize the VCF Installer to converge your standalone environment into a VCF 9.0 deployment. For detailed instructions, see <a href="https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/what-is-the-vcf-installer-.html" target="_blank" class="text-blue-500 underline">VCF 9.0 Installation Guide</a>.' });
                }

                // Final step for both Import/Converge paths
                upgradeSteps.push({ title: 'Perform Post-Converge/Import Validations', description: 'Conduct thorough validations after the converge or import process to ensure the new VCF 9.0 environment is stable and functional. Refer to the <a href="https://techdocs.broadcom.com/content/dam/broadcom/techdocs/us/en/assets/vmware-cis/vcf/vcf-9.0-operate-vcf-health-journey.pdf" target="_blank" class="text-blue-500 underline">VCF Post-Deployment Validation</a>.' });
                upgradeSteps.push({ title: 'VCF 9.0 Environment', description: 'Your environment is now successfully upgraded to VMware Cloud Foundation 9.0.', type: 'end' });
            }

            // Populate the overview text from the structured steps for PDF export
            let currentStepNumber = 1;
            overviewText = ''; // Clear previous overview text
            // Add the initial "Current Environment" to the text summary
            overviewText += `## Current Environment: ${initialComponentsString}\n\n`;

            if (hasSDDCManager) {
                overviewText += '## Existing VCF Environment Upgrade Path\n\n';
                overviewText += 'This path outlines the upgrade process for an existing VMware Cloud Foundation (VCF) environment managed by SDDC Manager.\n\n';
            } else if (hasVxRailManager && !hasSDDCManager) {
                overviewText += '## VxRail Environment Upgrade Path\n\n';
            } else {
                overviewText += '## Standalone Environment Upgrade Path\n\n';
                overviewText += 'This path outlines the upgrade process for a standalone VMware environment to VMware Cloud Foundation (VCF) 9.0. This can involve either an "Import" path (if NSX is present) or a "Converge" path (if NSX is not present).\n\n';
            }

            // Filter out the initial 'Current Environment' step from the numbered list in the overview text
            const stepsForOverview = upgradeSteps.filter(step => !step.title.startsWith('Current Environment:'));

            stepsForOverview.forEach(step => {
                overviewText += `${currentStepNumber}. **${step.title}:** ${step.description}\n\n`;
                currentStepNumber++;
            });

            // Add the final "End" marker to the overview text
            if (hasSDDCManager || (!hasSDDCManager && !hasVxRailManager)) {
                 overviewText += '## End: VCF 9.0 Environment\n\n';
            } else if (hasVxRailManager && !hasSDDCManager) {
                 overviewText += '## End: VCF 9.0 Upgrade Not Available\n\n';
            }


            renderFlowchart(); // Render the HTML/CSS flowchart
            upgradeOverviewDiv.innerHTML = overviewText.replace(/\n/g, '<br/>'); // Render overview text summary
        };

        // Helper function to add header and footer to PDF pages
        const addPageHeaderFooter = (pdf, pageNumber, totalPages) => {
            pdf.setFontSize(10);
            pdf.setTextColor(150); // Gray color
            // Header
            pdf.text('VMware VCF 9 Upgrade Planner', 10, 10);
            // Footer (Page Number)
            pdf.text(`Page ${pageNumber} of ${totalPages}`, pdf.internal.pageSize.width - 10, pdf.internal.pageSize.height - 10, { align: 'right' });
        };

        // Export to PDF functionality (Revised to use jsPDF for text and drawing for flowchart)
        const exportToPdf = async () => {
            if (upgradeSteps.length === 0) {
                showMessage('error', 'Please generate an upgrade path first before exporting to PDF.');
                return;
            }

            showMessage('info', 'Generating PDF...');

            try {
                const pdf = new window.jspdf.jsPDF('p', 'mm', 'a4');
                const imgWidth = 210; // A4 width in mm
                const pageHeight = 297; // A4 height in mm
                const horizontalMargin = 10; // 10mm horizontal margin
                const contentWidth = imgWidth - 2 * horizontalMargin;
                let yOffset = 15; // Initial Y offset for content after header
                let currentPage = 1;

                // --- Page 1: Title Page ---
                pdf.setFontSize(36);
                pdf.setTextColor(51, 51, 51); // Dark gray
                pdf.text('VMware VCF 9 Upgrade Planner', imgWidth / 2, pageHeight / 2 - 20, { align: 'center' });
                pdf.setFontSize(14);
                pdf.text('Your personalized guide to VCF 9.0 upgrades', imgWidth / 2, pageHeight / 2 + 5, { align: 'center' });
                pdf.addPage(); // Add first content page
                yOffset = 15; // Reset yOffset for new page

                // --- Page: Your Environment Summary (Still using html2canvas for consistent styling) ---
                pdfComponentsList.innerHTML = '';
                if (selectedComponents.length > 0) {
                    selectedComponents.forEach(comp => {
                        const listItem = document.createElement('li');
                        listItem.textContent = `${comp.name}: ${comp.version || 'N/A'}`;
                        pdfComponentsList.appendChild(listItem);
                    });
                } else {
                    const listItem = document.createElement('li');
                    listItem.textContent = 'No components selected.';
                    pdfComponentsList.appendChild(listItem);
                }

                const summaryCanvas = await html2canvas(pdfComponentsSummary, { scale: 2 });
                const summaryImgData = summaryCanvas.toDataURL('image/png');
                const summaryImgHeight = (summaryCanvas.height * contentWidth) / summaryCanvas.width;

                let summaryHeightLeft = summaryImgHeight;
                let summaryPosition = 0;

                while (summaryHeightLeft > 0) {
                    addPageHeaderFooter(pdf, currentPage, 'XX');
                    pdf.addImage(summaryImgData, 'PNG', horizontalMargin, yOffset, contentWidth, Math.min(pageHeight - yOffset - 15, summaryHeightLeft), undefined, 'FAST', summaryPosition);
                    summaryHeightLeft -= (pageHeight - yOffset - 15);
                    summaryPosition += (pageHeight - yOffset - 15);
                    if (summaryHeightLeft > 0) {
                        pdf.addPage();
                        currentPage++;
                        yOffset = 15;
                    }
                }
                pdf.addPage(); // Add new page for flowchart
                currentPage++;
                yOffset = 15;

                // --- Flowchart Drawing (Vector Graphics) ---
                const nodeWidth = 150; // mm
                const nodeHeight = 30; // mm
                const nodeMargin = 15; // mm (space between nodes)
                const arrowHeight = 15; // mm (length of arrow line)
                const maxContentHeight = pageHeight - 2 * yOffset; // Usable height per page

                pdf.setFont('helvetica', 'bold');
                pdf.setFontSize(12);
                pdf.setTextColor(51, 51, 51); // Dark gray

                // Title for flowchart section
                pdf.text('Upgrade Path (Flowchart)', horizontalMargin, yOffset);
                yOffset += 10; // Space after title

                for (let i = 0; i < upgradeSteps.length; i++) {
                    const step = upgradeSteps[i];
                    let currentY = yOffset;

                    // Calculate text dimensions for node content
                    pdf.setFont('helvetica', 'bold');
                    let textLines = pdf.splitTextToSize(step.title, nodeWidth - 10); // 10mm padding inside node
                    let textHeight = textLines.length * pdf.getLineHeight() / pdf.internal.scaleFactor; // Convert points to mm

                    // Adjust node height based on text content
                    let actualNodeHeight = Math.max(nodeHeight, textHeight + 10); // Min 30mm, or text height + padding

                    // Check if node fits on current page
                    if (currentY + actualNodeHeight > pageHeight - 15) { // 15mm for footer
                        pdf.addPage();
                        currentPage++;
                        yOffset = 15; // Reset yOffset for new page
                        addPageHeaderFooter(pdf, currentPage, 'XX');
                        currentY = yOffset;
                    }

                    // Draw Node
                    let nodeX = horizontalMargin + (contentWidth - nodeWidth) / 2; // Center node
                    let nodeY = currentY;

                    // Set node colors based on type
                    let borderColor = '#e0e0e0'; // Light gray
                    let fillColor = '#ffffff'; // White
                    let textColor = '#333333'; // Dark gray

                    if (i === 0) { // Start node
                        borderColor = '#34d399'; // Green
                        fillColor = '#e6ffed'; // Light green
                        textColor = '#1a5e20'; // Dark green
                    } else if (i === upgradeSteps.length - 1) { // End node
                        borderColor = '#34d399'; // Green
                        fillColor = '#e6ffed'; // Light green
                        textColor = '#1a5e20'; // Dark green
                        if (step.highlight) { // Error highlight for VxRail
                            borderColor = '#ef4444'; // Red
                            fillColor = '#ffe6e6'; // Light red
                            textColor = '#8c1c1c'; // Dark red
                        }
                    } else if (step.type === 'decision') { // Decision node
                        borderColor = '#f59e0b'; // Orange
                        fillColor = '#fffbe6'; // Light yellow
                        textColor = '#6d4e00'; // Dark yellow
                    }

                    pdf.setDrawColor(borderColor);
                    pdf.setFillColor(fillColor);
                    pdf.roundedRect(nodeX, nodeY, nodeWidth, actualNodeHeight, 3, 3, 'FD'); // Fill and Draw rectangle with rounded corners

                    // Add text to node
                    pdf.setTextColor(textColor);
                    pdf.setFont('helvetica', 'bold');
                    pdf.text(textLines, nodeX + nodeWidth / 2, nodeY + actualNodeHeight / 2, { align: 'center', baseline: 'middle' });

                    yOffset = nodeY + actualNodeHeight; // Update yOffset after node

                    // Add Arrow if not the last step
                    if (i < upgradeSteps.length - 1) {
                        // Check if arrow fits on current page, if not, add new page
                        if (yOffset + arrowHeight > pageHeight - 15) {
                            pdf.addPage();
                            currentPage++;
                            yOffset = 15; // Reset yOffset for new page
                            addPageHeaderFooter(pdf, currentPage, 'XX');
                        }
                        pdf.setDrawColor('#9e9e9e'); // Gray for arrow
                        pdf.setLineWidth(0.5); // Thin line
                        // Draw line
                        pdf.line(nodeX + nodeWidth / 2, yOffset, nodeX + nodeWidth / 2, yOffset + arrowHeight);
                        // Draw arrowhead
                        const arrowX = nodeX + nodeWidth / 2;
                        const arrowY = yOffset + arrowHeight;
                        pdf.triangle(arrowX - 3, arrowY - 3, arrowX + 3, arrowY - 3, arrowX, arrowY, 'FD'); // Filled triangle
                        yOffset += arrowHeight + nodeMargin; // Move yOffset for next node
                    } else {
                        yOffset += nodeMargin; // Add margin after the last node
                    }
                }

                pdf.addPage(); // Add new page for overview text
                currentPage++;
                yOffset = 15;

                // --- Overview Text (Searchable Text) ---
                pdf.setFont('helvetica', 'bold');
                pdf.setFontSize(16);
                pdf.setTextColor(51, 51, 51);
                pdf.text('Overview (Text Summary)', horizontalMargin, yOffset);
                yOffset += 10; // Space after title

                pdf.setFont('helvetica', 'normal');
                pdf.setFontSize(10);
                pdf.setTextColor(51, 51, 51);

                // Get the inner HTML content, then convert to text, handling <br/> and <a> tags
                const overviewHtmlContent = upgradeOverviewDiv.innerHTML;
                const tempDiv = document.createElement('div');
                tempDiv.innerHTML = overviewHtmlContent;

                // Replace <br/> with newline characters for proper text rendering
                Array.from(tempDiv.querySelectorAll('br')).forEach(br => br.replaceWith('\n'));

                // Extract text content, handling links by including their href in parentheses
                let plainTextContent = '';
                tempDiv.childNodes.forEach(node => {
                    if (node.nodeType === Node.TEXT_NODE) {
                        plainTextContent += node.textContent;
                    } else if (node.nodeType === Node.ELEMENT_NODE && node.tagName === 'A') {
                        plainTextContent += `${node.textContent} (${node.href})`;
                    } else if (node.nodeType === Node.ELEMENT_NODE && (node.tagName === 'H2' || node.tagName === 'H3')) {
                        // Handle headings for better formatting in PDF
                        plainTextContent += `\n\n${node.textContent}\n`;
                    } else {
                        plainTextContent += node.textContent; // Fallback for other elements
                    }
                });

                const textLines = pdf.splitTextToSize(plainTextContent, contentWidth);
                const lineHeight = pdf.getLineHeight() / pdf.internal.scaleFactor; // in mm

                for (let i = 0; i < textLines.length; i++) {
                    if (yOffset + lineHeight > pageHeight - 15) { // Check if line fits, 15mm for footer
                        pdf.addPage();
                        currentPage++;
                        yOffset = 15; // Reset yOffset for new page
                        addPageHeaderFooter(pdf, currentPage, 'XX');
                    }
                    pdf.text(textLines[i], horizontalMargin, yOffset);
                    yOffset += lineHeight;
                }

                // Update total page numbers on all pages
                const totalPages = pdf.internal.getNumberOfPages();
                for (let i = 1; i <= totalPages; i++) {
                    pdf.setPage(i);
                    addPageHeaderFooter(pdf, i, totalPages);
                }

                pdf.save('VCF_Upgrade_Path.pdf');
                showMessage('success', 'PDF exported successfully!');
            } catch (error) {
                console.error('Error exporting to PDF:', error);
                showMessage('error', 'Failed to export PDF. Please try again.');
            }
        };

        // Event Listeners
        generatePathBtn.addEventListener('click', generateUpgradePath);
        resetBtn.addEventListener('click', handleReset);
        exportPdfBtn.addEventListener('click', exportToPdf);

        // Initial render on page load
        window.addEventListener('load', () => {
            renderComponentPalette();
            renderSelectedComponents();
            renderFlowchart(); // Initial render of the flowchart placeholder
        });
    </script>
</body>
</html>




