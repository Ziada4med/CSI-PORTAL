<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSI Attribute Portal</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        /* Custom scrollbar for better aesthetics */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #e0e0e0;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #9ca3af; /* Gray-400 */
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #6b7280; /* Gray-500 */
        }
        /* Visual indentation for nested attributes */
        .attribute-input-row[data-level="0"] { margin-left: 0rem; }
        .attribute-input-row[data-level="1"] { margin-left: 1.5rem; }
        .attribute-input-row[data-level="2"] { margin-left: 3rem; }
        .attribute-input-row[data-level="3"] { margin-left: 4.5rem; }
        .attribute-input-row[data-level="4"] { margin-left: 6rem; }
        /* Add more levels if needed */

        .collected-attribute-row[data-level="0"] { padding-left: 1.5rem; } /* Base padding for top level */
        .collected-attribute-row[data-level="1"] { padding-left: 3.0rem; }
        .collected-attribute-row[data-level="2"] { padding-left: 4.5rem; }
        .collected-attribute-row[data-level="3"] { padding-left: 6.0rem; }
        .collected-attribute-row[data-level="4"] { padding-left: 7.5rem; }
        /* Add more levels if needed for collected data display */

        /* Modal specific styles */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        .modal-content {
            background-color: white;
            padding: 2rem;
            border-radius: 0.5rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            width: 90%;
            max-width: 600px;
            max-height: 80vh;
            overflow-y: auto;
            position: relative;
        }
        .modal-close-btn {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: #6b7280; /* Gray-500 */
        }
    </style>
</head>
<body class="min-h-screen flex items-center justify-center p-4">

    <!-- Login Page Container -->
    <div id="login-page" class="bg-white p-8 rounded-lg shadow-xl w-full max-w-md">
        <h1 class="text-3xl font-bold text-center text-gray-800 mb-8">Login to CSI Portal</h1>
        <form id="login-form" class="space-y-6">
            <div>
                <label for="role-select" class="block text-sm font-medium text-gray-700 mb-2">Select Role:</label>
                <select id="role-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm">
                    <option value="">-- Select Role --</option>
                    <option value="developer">Developer</option>
                    <option value="editor">Editor</option>
                </select>
            </div>
            <div>
                <label for="username-input" class="block text-sm font-medium text-gray-700 mb-2">Username:</label>
                <input type="text" id="username-input" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Enter username">
            </div>
            <div>
                <label for="password-input" class="block text-sm font-medium text-gray-700 mb-2">Password:</label>
                <input type="password" id="password-input" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Enter password">
            </div>
            <button type="submit" class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                Login
            </button>
        </form>
        <p id="login-error-message" class="text-red-500 text-center mt-4 hidden"></p>
    </div>

    <!-- Main Application Container (Initially Hidden) -->
    <div id="main-app" class="bg-white p-8 rounded-lg shadow-xl w-full max-w-4xl hidden">
        <h1 class="text-3xl font-bold text-center text-gray-800 mb-8">CSI Division Attribute Portal</h1>

        <!-- Selection Section -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
            <!-- Division Selection (Dropdown) -->
            <div>
                <label for="division-select" class="block text-sm font-medium text-gray-700 mb-2">CSI Division:</label>
                <select id="division-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm">
                    <option value="">Select Division</option>
                </select>
            </div>

            <!-- Section Selection (Dropdown + "Modify" Button) -->
            <div>
                <label for="section-select" class="block text-sm font-medium text-gray-700 mb-2 flex justify-between items-center">
                    Section:
                    <button id="modify-sections-btn" class="ml-2 px-3 py-1 bg-blue-500 text-white text-xs rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150" disabled>
                        Modify Sections
                    </button>
                </label>
                <select id="section-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm" disabled>
                    <option value="">Select Section</option>
                </select>
            </div>

            <!-- CSI Section (Detailed) Selection (Dropdown + "Modify" Button) -->
            <div>
                <label for="subsection-select" class="block text-sm font-medium text-gray-700 mb-2 flex justify-between items-center">
                    CSI Section (Detailed):
                    <button id="modify-subsections-btn" class="ml-2 px-3 py-1 bg-blue-500 text-white text-xs rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150" disabled>
                        Modify Detailed Sections
                    </button>
                </label>
                <select id="subsection-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm" disabled>
                    <option value="">Select Detailed Section</option>
                </select>
            </div>

            <!-- CSI Item (Detailed) Selection (NEW Dropdown + "Modify" Button) -->
            <div>
                <label for="item-select" class="block text-sm font-medium text-gray-700 mb-2 flex justify-between items-center">
                    CSI Item (Detailed):
                    <button id="modify-items-btn" class="ml-2 px-3 py-1 bg-blue-500 text-white text-xs rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150" disabled>
                        Modify Items
                    </button>
                </label>
                <select id="item-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm" disabled>
                    <option value="">Select Item</option>
                </select>
            </div>
        </div>

        <!-- Attribute Input Section -->
        <div id="attribute-input-section" class="border border-gray-200 p-6 rounded-lg mb-8 bg-gray-50 hidden">
            <h2 class="text-xl font-semibold text-gray-800 mb-4">Attributes for <span id="current-item-name" class="text-indigo-600"></span></h2>
            <div id="attributes-container" class="space-y-4">
                <!-- Attribute input fields will be added here dynamically -->
            </div>
            <div class="flex justify-end mt-6">
                <button id="save-all-attributes-btn" class="px-4 py-2 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500 transition ease-in-out duration-150">
                    Save All Attributes
                </button>
            </div>
        </div>

        <!-- Collected Data Display -->
        <div id="collected-data-section" class="hidden">
            <h2 class="text-xl font-semibold text-gray-800 mb-4">Collected Data</h2>
            <div class="overflow-x-auto rounded-lg shadow-md border border-gray-200">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-100">
                        <tr>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Division</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Section</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">CSI Section (Detailed)</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">CSI Item (Detailed)</th> <!-- NEW COLUMN -->
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Attribute Name</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Standard Values</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Mandatory</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="collected-data-body" class="bg-white divide-y divide-gray-200">
                        <!-- Collected data rows will be appended here -->
                    </tbody>
                </table>
            </div>
            <button id="export-excel-btn" class="mt-6 w-full px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-purple-600 hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-500 transition ease-in-out duration-150">
                Export to Excel
            </button>
        </div>

        <!-- Message Box for alerts -->
        <div id="message-box" class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center hidden">
            <div class="bg-white p-6 rounded-lg shadow-xl max-w-sm w-full text-center">
                <p id="message-text" class="text-lg font-medium text-gray-800 mb-4"></p>
                <button id="message-box-ok-btn" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">OK</button>
            </div>
        </div>

    </div>

    <!-- Modify Sections Modal -->
    <div id="modify-sections-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <button class="modal-close-btn" id="close-modify-sections-modal">&times;</button>
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Modify Sections for <span id="modal-division-name" class="text-indigo-600 font-normal"></span></h2>

            <!-- Non-modifiable Division Code -->
            <div class="mb-4">
                <label class="block text-sm font-medium text-gray-700 mb-1">Division Code:</label>
                <input type="text" id="modal-division-code" class="block w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm bg-gray-100 text-gray-700 cursor-not-allowed" readonly>
            </div>

            <div id="modal-sections-list" class="space-y-3 mb-6">
                <!-- Editable section inputs will be rendered here -->
            </div>

            <div class="flex gap-2 mb-6">
                <input type="text" id="new-section-num" placeholder="New Sec Num (2 digits)" maxlength="2" pattern="\d{2}" class="w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm text-gray-700">
                <input type="text" id="new-section-desc" placeholder="New Section Description" class="flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm text-gray-700">
                <button id="add-new-section-btn" class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>
            </div>

            <div class="flex justify-end gap-4">
                <button id="save-sections-modal-btn" class="px-6 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2 transition ease-in-out duration-150">Save Changes</button>
                <button id="cancel-sections-modal-btn" class="px-6 py-2 bg-gray-300 text-gray-800 rounded-md hover:bg-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition ease-in-out duration-150">Cancel</button>
            </div>
        </div>
    </div>

    <!-- Modify Subsections Modal -->
    <div id="modify-subsections-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <button class="modal-close-btn" id="close-modify-subsections-modal">&times;</button>
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Modify CSI Sections (Detailed) for <span id="modal-subsection-division-name" class="text-indigo-600 font-normal"></span> - <span id="modal-subsection-section-name" class="text-indigo-600 font-normal"></span></h2>

            <!-- Non-modifiable Division and Section Codes -->
            <div class="mb-4 flex gap-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Division Code:</label>
                    <input type="text" id="modal-subsection-division-code" class="block w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm bg-gray-100 text-gray-700 cursor-not-allowed" readonly>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Parent Section Code:</label>
                    <input type="text" id="modal-subsection-section-code" class="block w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm bg-gray-100 text-gray-700 cursor-not-allowed" readonly>
                </div>
            </div>

            <div id="modal-subsections-list" class="space-y-3 mb-6">
                <!-- Editable subsection inputs will be rendered here -->
            </div>

            <div class="flex gap-2 mb-6">
                <input type="text" id="new-subsection-num" placeholder="New Detailed Sec Num (2 digits)" maxlength="2" pattern="\d{2}" class="w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm text-gray-700">
                <input type="text" id="new-subsection-desc" placeholder="New Detailed Section Description" class="flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm text-gray-700">
                <button id="add-new-subsection-btn" class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>
            </div>

            <div class="flex justify-end gap-4">
                <button id="save-subsections-modal-btn" class="px-6 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2 transition ease-in-out duration-150">Save Changes</button>
                <button id="cancel-subsections-modal-btn" class="px-6 py-2 bg-gray-300 text-gray-800 rounded-md hover:bg-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition ease-in-out duration-150">Cancel</button>
            </div>
        </div>
    </div>

    <!-- Modify Items Modal (NEW) -->
    <div id="modify-items-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <button class="modal-close-btn" id="close-modify-items-modal">&times;</button>
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Modify CSI Items (Detailed) for <span id="modal-item-division-name" class="text-indigo-600 font-normal"></span> - <span id="modal-item-section-name" class="text-indigo-600 font-normal"></span> - <span id="modal-item-subsection-name" class="text-indigo-600 font-normal"></span></h2>

            <!-- Non-modifiable Division, Section, and Detailed Section Codes -->
            <div class="mb-4 flex gap-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Division Code:</label>
                    <input type="text" id="modal-item-division-code" class="block w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm bg-gray-100 text-gray-700 cursor-not-allowed" readonly>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Parent Section Code:</label>
                    <input type="text" id="modal-item-section-code" class="block w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm bg-gray-100 text-gray-700 cursor-not-allowed" readonly>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Parent Detailed Section Code:</label>
                    <input type="text" id="modal-item-subsection-code" class="block w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm bg-gray-100 text-gray-700 cursor-not-allowed" readonly>
                </div>
            </div>

            <div id="modal-items-list" class="space-y-3 mb-6">
                <!-- Editable item inputs will be rendered here -->
            </div>

            <div class="flex gap-2 mb-6">
                <input type="text" id="new-item-num" placeholder="New Item Num (XX.YY)" maxlength="5" pattern="\d{2}\.\d{2}" class="w-36 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm text-gray-700">
                <input type="text" id="new-item-desc" placeholder="New Item Description" class="flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm text-gray-700">
                <button id="add-new-item-btn" class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>
            </div>

            <div class="flex justify-end gap-4">
                <button id="save-items-modal-btn" class="px-6 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2 transition ease-in-out duration-150">Save Changes</button>
                <button id="cancel-items-modal-btn" class="px-6 py-2 bg-gray-300 text-gray-800 rounded-md hover:bg-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition ease-in-out duration-150">Cancel</button>
            </div>
        </div>
    </div>


    <!-- SheetJS CDN for Excel export -->
    <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>

    <script>
        // Initial CSI Data structure with 4 levels.
        // Division -> Section -> Detailed Section (Subsection) -> Array of Items
        let csiData = {
            "Division 00 - Procurement and Contracting Requirements": {
                "Section 00 10 00 - Solicitations": {
                    "Subsection 00 11 00 - Advertisement for Bids": [],
                    "Subsection 00 12 00 - Request for Proposals": [],
                    "Subsection 00 13 00 - Invitation to Bid": []
                },
                "Section 00 20 00 - Instructions for Procurement": {
                    "Subsection 00 21 00 - Instructions to Bidders": [],
                    "Subsection 00 22 00 - Instructions to Proposers": []
                },
                "Section 00 30 00 - Available Information": {
                    "Subsection 00 31 00 - Geotechnical Data": [],
                    "Subsection 00 32 00 - Existing Conditions Data": [],
                    "Subsection 00 33 00 - Hazardous Material Information": []
                },
                "Section 00 40 00 - Procurement Forms": {
                    "Subsection 00 41 00 - Bid Form": [],
                    "Subsection 00 42 00 - Proposal Form": [],
                    "Subsection 00 43 00 - Bid Bond Form": []
                },
                "Section 00 45 00 - Representations and Certifications": {
                    "Subsection 00 45 10 - Non-Collusion Affidavit": [],
                    "Subsection 00 45 20 - Equal Opportunity Certification": []
                },
                "Section 00 50 00 - Contracting Forms": {
                    "Subsection 00 51 00 - Agreement Form": [],
                    "Subsection 00 52 00 - Performance Bond Form": [],
                    "Subsection 00 53 00 - Payment Bond Form": []
                },
                "Section 00 60 00 - Project Forms": {
                    "Subsection 00 61 00 - Change Order Form": [],
                    "Subsection 00 62 00 - Application for Payment Form": [],
                    "Subsection 00 63 00 - Certificate of Substantial Completion": []
                },
                "Section 00 70 00 - Conditions of the Contract": {
                    "Subsection 00 72 00 - General Conditions": [],
                    "Subsection 00 73 00 - Supplementary Conditions": []
                },
                "Section 00 80 00 - Supplementary Conditions": {
                    "Subsection 00 81 00 - Supplementary Conditions to General Conditions": []
                },
                "Section 00 90 00 - Addenda and Modifications": {
                    "Subsection 00 91 00 - Addenda": [],
                    "Subsection 00 92 00 - Modifications": []
                }
            },
            "Division 01 - General Requirements": {
                "Section 01 10 00 - Summary": {
                    "Subsection 01 11 00 - Summary of Work": [],
                    "Subsection 01 11 13 - Work Covered by Contract Documents": [],
                    "Subsection 01 11 16 - Work by Owner": [],
                    "Subsection 01 11 19 - Purchase Contracts": [],
                    "Subsection 01 12 00 - Multiple Contract Summary": [],
                    "Subsection 01 12 13 - Summary of Contracts": [],
                    "Subsection 01 12 16 - Work Sequence": [],
                    "Subsection 01 12 19 - Contract Interface": []
                },
                "Section 01 14 00 - Work Restrictions": {
                    "Subsection 01 14 13 - Access to Site": [],
                    "Subsection 01 14 16 - Coordination with Occupants": [],
                    "Subsection 01 14 19 - Use of Site": [],
                    "Subsection 01 14 33 - Work in Rights-of-Way": []
                },
                "Section 01 18 00 - Project Utility Sources": {
                    "Subsection 01 18 13 - Utility Service Connections": []
                },
                "Section 01 20 00 - Price and Payment Procedures": {
                    "Subsection 01 21 00 - Allowances": [],
                    "Subsection 01 21 13 - Cash Allowances": [],
                    "Subsection 01 21 16 - Contingency Allowances": [],
                    "Subsection 01 21 19 - Testing and Inspecting Allowances": [],
                    "Subsection 01 21 23 - Installation Allowances": [],
                    "Subsection 01 21 26 - Product Allowances": [],
                    "Subsection 01 21 29 - Quantity Allowances": [],
                    "Subsection 01 21 43 - Time Allowances": [],
                    "Subsection 01 22 00 - Unit Prices": [],
                    "Subsection 01 22 13 - Unit Price Measurement": [],
                    "Subsection 01 22 16 - Unit Price Payment": [],
                    "Subsection 01 23 00 - Alternates": [],
                    "Subsection 01 24 00 - Value Analysis": [],
                    "Subsection 01 24 13 - Value Engineering": [],
                    "Subsection 01 25 00 - Substitution Procedures": [],
                    "Subsection 01 25 13 - Product Substitution Procedures": [],
                    "Subsection 01 25 16 - Execution Substitution Procedures": [],
                    "Subsection 01 26 00 - Contract Modification Procedures": [],
                    "Subsection 01 26 13 - Requests for Information": [],
                    "Subsection 01 26 19 - Clarification Notices": [],
                    "Subsection 01 26 33 - Minor Changes in the Work": [],
                    "Subsection 01 26 36 - Supplemental Instructions": [],
                    "Subsection 01 26 39 - Field Orders": [],
                    "Subsection 01 26 43 - Amendments": [],
                    "Subsection 01 26 46 - Construction Change Directives": [],
                    "Subsection 01 26 49 - Work Change Directives": [],
                    "Subsection 01 26 53 - Proposal Requests": [],
                    "Subsection 01 26 54 - Proposal Worksheet Summaries": [],
                    "Subsection 01 26 57 - Change Order Requests": [],
                    "Subsection 01 26 63 - Change Orders": [],
                    "Subsection 01 29 00 - Payment Procedures": [],
                    "Subsection 01 29 73 - Schedule of Values": [],
                    "Subsection 01 29 76 - Progress Payment Procedures": [],
                    "Subsection 01 29 83 - Payment Procedures for Testing Laboratory Services": []
                },
                "Section 01 30 00 - Administrative Requirements": {
                    "Subsection 01 31 00 - Project Management and Coordination": [],
                    "Subsection 01 31 13 - Project Coordination": [],
                    "Subsection 01 31 14 - Facility Services Coordination": [],
                    "Subsection 01 31 16 - Multiple Contract Coordination": [],
                    "Subsection 01 31 19 - Project Meetings": [
                        "Item 01 31 19.13 - Preconstruction Meetings",
                        "Item 01 31 19.16 - Site Mobilization Meetings",
                        "Item 01 31 19.23 - Progress Meetings",
                        "Item 01 31 19.33 - Preinstallation Meetings"
                    ],
                    "Subsection 01 31 23 - Project Web Site": [],
                    "Subsection 01 31 26 - Electronic Communication Protocols": [],
                    "Subsection 01 32 00 - Construction Progress Documentation": [],
                    "Subsection 01 32 13 - Scheduling of Work": [],
                    "Subsection 01 32 16 - Construction Progress Schedule": [],
                    "Subsection 01 32 16.13 - Network Analysis Schedules": [],
                    "Subsection 01 32 19 - Submittals Schedule": [],
                    "Subsection 01 32 23 - Survey and Layout Data": [],
                    "Subsection 01 32 26 - Construction Progress Reporting": [],
                    "Subsection 01 32 29 - Periodic Work Observation": [],
                    "Subsection 01 32 33 - Photographic Documentation": [],
                    "Subsection 01 32 36 - Video Monitoring and Documentation": [],
                    "Subsection 01 32 43 - Purchase Order Tracking": [],
                    "Subsection 01 33 00 - Submittal Procedures": [],
                    "Subsection 01 33 13 - Certificates": [],
                    "Subsection 01 33 16 - Design Data": [],
                    "Subsection 01 33 19 - Field Test Reporting": [],
                    "Subsection 01 33 23 - Shop Drawings, Product Data, and Samples": [],
                    "Subsection 01 33 26 - Source Quality Control Reporting": [],
                    "Subsection 01 33 29 - Sustainable Design Reporting": [],
                    "Subsection 01 33 29.01 - Material Cost Summary Form": [],
                    "Subsection 01 33 29.02 - Wood-Containing Product List": [],
                    "Subsection 01 33 29.03 - Metal-Containing Product List": [],
                    "Subsection 01 33 29.04 - Material Content Form": [],
                    "Subsection 01 33 29.05 - New Product Source Form": [],
                    "Subsection 01 33 29.06 - Reused Product Form": [],
                    "Subsection 01 33 29.07 - Prohibited Content Installer Certification": [],
                    "Subsection 01 35 00 - Special Procedures": [],
                    "Subsection 01 35 03 - Conservation Treatment Procedures": [],
                    "Subsection 01 35 13 - Special Project Procedures": [],
                    "Subsection 01 35 13.13 - Special Project Procedures for Airport Facilities": [],
                    "Subsection 01 35 13.16 - Special Project Procedures for Detention Facilities": [],
                    "Subsection 01 35 13.19 - Special Project Procedures for Healthcare Facilities": [],
                    "Subsection 01 35 13.26 - Special Project Procedures for Clean Rooms": [],
                    "Subsection 01 35 13.43 - Special Project Procedures for Contaminated Sites": [],
                    "Subsection 01 35 16 - Alteration Project Procedures": [],
                    "Subsection 01 35 23 - Owner Safety Requirements": [],
                    "Subsection 01 35 26 - Governmental Safety Requirements": [],
                    "Subsection 01 35 29 - Health, Safety, and Emergency Response Procedures": [],
                    "Subsection 01 35 29.13 - Health, Safety, and Emergency Response Procedures for Contaminated Sites": [],
                    "Subsection 01 35 43 - Environmental Procedures": [],
                    "Subsection 01 35 43.13 - Environmental Procedures for Hazardous Materials": [],
                    "Subsection 01 35 43.16 - Environmental Procedures for Toxic Materials": [],
                    "Subsection 01 35 46 - Indoor Air Quality Procedures": [],
                    "Subsection 01 35 53 - Security Procedures": [],
                    "Subsection 01 35 63 - Sustainability Certification Project Requirements": [],
                    "Subsection 01 35 66 - Sustainability Certification Project Procedures": [],
                    "Subsection 01 35 73 - Delegated Design Procedures": [],
                    "Subsection 01 35 91 - Period Treatment Procedures": []
                },
                "Section 01 40 00 - Quality Requirements": {
                    "Subsection 01 41 00 - Regulatory Requirements": [],
                    "Subsection 01 41 13 - Codes": [],
                    "Subsection 01 41 16 - Laws": [],
                    "Subsection 01 41 19 - Rules": [],
                    "Subsection 01 41 23 - Fees": [],
                    "Subsection 01 41 26 - Permit Requirements": [],
                    "Subsection 01 42 00 - References": [],
                    "Subsection 01 42 13 - Abbreviations and Acronyms": [],
                    "Subsection 01 42 16 - Definitions": [],
                    "Subsection 01 42 19 - Reference Standards": [],
                    "Subsection 01 43 00 - Quality Assurance": [],
                    "Subsection 01 43 13 - Manufacturer Qualifications": [],
                    "Subsection 01 43 16 - Supplier Qualifications": [],
                    "Subsection 01 43 19 - Fabricator Qualifications": [],
                    "Subsection 01 43 23 - Installer Qualifications": [],
                    "Subsection 01 43 26 - Testing and Inspecting Agency Qualifications": [],
                    "Subsection 01 43 29 - Code-Required Special Inspector Qualifications": [],
                    "Subsection 01 43 33 - Manufacturer's Field Services": [],
                    "Subsection 01 43 36 - Field Samples": [],
                    "Subsection 01 43 39 - Mockups": [],
                    "Subsection 01 45 00 - Quality Control": [],
                    "Subsection 01 45 13 - Source Quality Control Procedures": [],
                    "Subsection 01 45 16 - Field Quality Control Procedures": [],
                    "Subsection 01 45 16.13 - Contractor Quality Control": [],
                    "Subsection 01 45 23 - Testing and Inspecting Services": [],
                    "Subsection 01 45 26 - Plant Inspection Procedures": [],
                    "Subsection 01 45 29 - Testing Laboratory Services": [],
                    "Subsection 01 45 31 - Climate Resiliency Quality Control Procedures": [],
                    "Subsection 01 45 33 - Code-Required Special Inspections and Procedures": []
                },
                "Section 01 50 00 - Temporary Facilities and Controls": {
                    "Subsection 01 51 00 - Temporary Utilities": [],
                    "Subsection 01 52 00 - Construction Facilities": [],
                    "Subsection 01 57 00 - Storm Water Pollution Control": []
                },
                "Section 01 60 00 - Product Requirements": {
                    "Subsection 01 61 00 - Common Product Requirements": [],
                    "Subsection 01 62 00 - Product Options and Substitutions": []
                },
                "Section 01 70 00 - Execution and Closeout Requirements": {
                    "Subsection 01 73 00 - Execution": [],
                    "Subsection 01 77 00 - Closeout Procedures": [],
                    "Subsection 01 78 00 - Closeout Submittals": []
                },
                "Section 01 80 00 - Facility Operation": {
                    "Subsection 01 81 00 - Commissioning": [],
                    "Subsection 01 82 00 - Demonstration and Training": []
                }
            },
            "Division 02 - Existing Conditions": {
                "Section 02 10 00 - Existing Site Remediation": {
                    "Subsection 02 11 00 - Site Decontamination": [],
                    "Subsection 02 12 00 - Hazardous Material Remediation": []
                },
                "Section 02 20 00 - Assessment": {
                    "Subsection 02 21 00 - Building Demolition Assessment": [],
                    "Subsection 02 22 00 - Site Assessment": []
                },
                "Section 02 40 00 - Demolition": {
                    "Subsection 02 41 00 - Demolition of Structures": [],
                    "Subsection 02 42 00 - Selective Demolition": []
                },
                "Section 02 65 00 - Underground Storage Tank Removal": {},
                "Section 02 70 00 - Water Remediation": {},
                "Section 02 80 00 - Hazardous Material Abatement": {
                    "Subsection 02 81 00 - Asbestos Abatement": [],
                    "Subsection 02 82 00 - Lead Abatement": []
                }
            },
            "Division 03 - Concrete": {
                "Section 03 10 00 - Concrete Forming and Accessories": {
                    "Subsection 03 11 00 - Concrete Forming": [],
                    "Subsection 03 12 00 - Concrete Accessories": [],
                    "Subsection 03 13 00 - Permanent Forms": []
                },
                "Section 03 20 00 - Concrete Reinforcing": {
                    "Subsection 03 21 00 - Reinforcing Steel": [],
                    "Subsection 03 22 00 - Welded Wire Fabric Reinforcing": [],
                    "Subsection 03 23 00 - Stressed Tendon Reinforcing": []
                },
                "Section 03 30 00 - Cast-in-Place Concrete": {
                    "Subsection 03 31 00 - Structural Concrete": [],
                    "Subsection 03 32 00 - Non-Structural Concrete": [],
                    "Subsection 03 33 00 - Architectural Concrete": [],
                    "Subsection 03 34 00 - Low Density Concrete": []
                },
                "Section 03 40 00 - Precast Concrete": {
                    "Subsection 03 41 00 - Plant-Cast Architectural Precast Concrete": [],
                    "Subsection 03 42 00 - Plant-Cast Structural Precast Concrete": [],
                    "Subsection 03 49 43 - Glass-Fiber-Reinforced Concrete Trim": []
                },
                "Section 03 50 00 - Cementitious Decks and Underlayment": {
                    "Subsection 03 51 00 - Cementitious Decking": [],
                    "Subsection 03 52 00 - Cementitious Underlayment": []
                }
            },
            "Division 04 - Masonry": {
                "Section 04 20 00 - Masonry Units": {
                    "Subsection 04 21 00 - Clay Unit Masonry": [],
                    "Subsection 04 22 00 - Concrete Unit Masonry": [],
                    "Subsection 04 23 00 - Glass Unit Masonry": []
                },
                "Section 04 40 00 - Stone": {
                    "Subsection 04 41 00 - Stone Veneer Masonry": [],
                    "Subsection 04 42 00 - Rough Stone Masonry": []
                },
                "Section 04 70 00 - Manufactured Masonry": {
                    "Subsection 04 71 00 - Manufactured Stone Masonry": []
                }
            },
            "Division 05 - Metals": {
                "Section 05 10 00 - Structural Metal Framing": {
                    "Subsection 05 11 00 - Structural Steel Framing": [],
                    "Subsection 05 12 00 - Steel Joist Framing": []
                },
                "Section 05 20 00 - Metal Joists": {
                    "Subsection 05 21 00 - Steel Joist Framing": []
                },
                "Section 05 30 00 - Metal Decking": {
                    "Subsection 05 31 00 - Steel Decking": [],
                    "Subsection 05 32 00 - Composite Steel Decking": []
                },
                "Section 05 40 00 - Cold-Formed Metal Framing": {
                    "Subsection 05 41 00 - Load-Bearing Metal Stud Framing": [],
                    "Subsection 05 42 00 - Non-Load-Bearing Metal Stud Framing": []
                },
                "Section 05 50 00 - Metal Fabrications": {
                    "Subsection 05 51 00 - Metal Stairs": [],
                    "Subsection 05 52 00 - Metal Railings": [],
                    "Subsection 05 53 00 - Metal Ladders": [],
                    "Subsection 05 59 63 - Detention Enclosures": []
                }
            },
            "Division 06 - Wood, Plastics, and Composites": {
                "Section 06 10 00 - Rough Carpentry": {
                    "Subsection 06 11 00 - Wood Framing": [],
                    "Subsection 06 11 16 - Stress-Rated Lumber": [],
                    "Subsection 06 12 00 - Sheathing": [],
                    "Subsection 06 12 16 - Stressed Skin Panels": [],
                    "Subsection 06 16 00 - Subflooring": []
                },
                "Section 06 20 00 - Finish Carpentry": {
                    "Subsection 06 21 00 - Interior Architectural Woodwork": [],
                    "Subsection 06 25 00 - Wood Paneling": []
                },
                "Section 06 40 00 - Architectural Woodwork": {
                    "Subsection 06 41 00 - Custom Casework": [],
                    "Subsection 06 42 00 - Wood Paneling": [],
                    "Subsection 06 43 00 - Wood Trim": []
                },
                "Section 06 60 00 - Plastic Fabrications": {
                    "Subsection 06 61 00 - Plastic Laminate Fabrications": []
                }
            },
            "Division 07 - Thermal and Moisture Protection": {
                "Section 07 10 00 - Dampproofing and Waterproofing": {
                    "Subsection 07 11 00 - Bituminous Dampproofing": [],
                    "Subsection 07 12 00 - Fluid-Applied Waterproofing": [],
                    "Subsection 07 13 00 - Sheet Waterproofing": []
                },
                "Section 07 20 00 - Thermal Protection": {
                    "Subsection 07 21 00 - Building Insulation": [],
                    "Subsection 07 22 00 - Roof and Deck Insulation": [],
                    "Subsection 07 22 13 - Asphaltic Perlite Concrete Deck": [],
                    "Subsection 07 24 00 - Exterior Insulation and Finish Systems (EIFS)": [],
                    "Subsection 07 27 00 - Preformed Air Barriers": []
                },
                "Section 07 30 00 - Steep Slope Roofing": {
                    "Subsection 07 31 00 - Shingles": [],
                    "Subsection 07 32 00 - Roof Tiles": []
                },
                "Section 07 40 00 - Roofing and Siding Panels": {
                    "Subsection 07 41 00 - Metal Roof Panels": [],
                    "Subsection 07 42 00 - Metal Wall Panels": []
                },
                "Section 07 50 00 - Membrane Roofing": {
                    "Subsection 07 51 00 - Built-Up Asphalt Roofing": [],
                    "Subsection 07 53 00 - Elastomeric Membrane Roofing": [],
                    "Subsection 07 55 00 - Protected Membrane Roofing": [],
                    "Subsection 07 55 63 - Vegetated Protected Membrane Roofing": []
                },
                "Section 07 60 00 - Flashing and Sheet Metal": {
                    "Subsection 07 61 00 - Sheet Metal Roofing": [],
                    "Subsection 07 62 00 - Sheet Metal Flashing and Trim": []
                },
                "Section 07 72 00 - Roof Accessories": {
                    "Subsection 07 72 26 - Ridge Vents": [],
                    "Subsection 07 72 33 - Roof Hatches": [],
                    "Subsection 07 72 36 - Smoke Vents": [],
                    "Subsection 07 72 39 - Operable Roof Vents": [],
                    "Subsection 07 72 43 - Roof Walk Boards": [],
                    "Subsection 07 72 46 - Roof Walkways": [],
                    "Subsection 07 72 53 - Snow Guards": [],
                    "Subsection 07 72 63 - Waste Containment Assemblies": [],
                    "Subsection 07 72 73 - Vegetated Roof Systems": []
                },
                "Section 07 76 00 - Roof Pavers": {},
                "Section 07 80 00 - Fire and Smoke Protection": {
                    "Subsection 07 81 00 - Applied Fireproofing": [],
                    "Subsection 07 84 00 - Firestopping": []
                },
                "Section 07 90 00 - Joint Protection": {
                    "Subsection 07 91 00 - Joint Sealants": [],
                    "Subsection 07 91 00 - Compression Seals and Gaskets": [],
                    "Subsection 07 92 00 - Joint Fillers": []
                }
            },
            "Division 08 - Openings": {
                "Section 08 10 00 - Doors and Frames": {
                    "Subsection 08 11 00 - Steel Doors and Frames": [],
                    "Subsection 08 11 63 - Metal Storm and Screen Doors and Frames": [],
                    "Subsection 08 12 00 - Wood Doors": [],
                    "Subsection 08 13 00 - Aluminum Doors and Frames": [],
                    "Subsection 08 14 29 - Prefinished Wood Doors": [],
                    "Subsection 08 14 66 - Wood Screen Doors and Frames": [],
                    "Subsection 08 14 69 - Wood Storm Doors and Frames": [],
                    "Subsection 08 15 66 - Plastic Screen Doors": [],
                    "Subsection 08 15 69 - Plastic Storm Doors": [],
                    "Subsection 08 15 73 - Sliding Plastic Doors": []
                },
                "Section 08 30 00 - Specialty Doors": {
                    "Subsection 08 31 00 - Access Doors and Frames": [],
                    "Subsection 08 33 00 - Overhead Coiling Doors": [],
                    "Subsection 08 34 49 - Radiation Shielding Doors": [],
                    "Subsection 08 34 53 - Security Doors and Frames": [],
                    "Subsection 08 39 53 - Security Entrances": []
                },
                "Section 08 40 00 - Entrances, Storefronts, and Curtain Walls": {
                    "Subsection 08 41 00 - Aluminum-Framed Entrances and Storefronts": [],
                    "Subsection 08 42 00 - Unitized Curtain Walls": [],
                    "Subsection 08 44 00 - Glazed Aluminum Curtain Walls": []
                },
                "Section 08 50 00 - Windows": {
                    "Subsection 08 51 00 - Steel Windows": [],
                    "Subsection 08 52 00 - Aluminum Windows": [],
                    "Subsection 08 53 00 - Vinyl Windows": [],
                    "Subsection 08 56 49 - Radiation Shielding Windows": []
                },
                "Section 08 70 00 - Hardware": {
                    "Subsection 08 71 00 - Door Hardware": [],
                    "Subsection 08 72 00 - Sliding Door Hardware": []
                },
                "Section 08 80 00 - Glazing": {
                    "Subsection 08 81 00 - Glass Glazing": [],
                    "Subsection 08 82 00 - Plastic Glazing": [],
                    "Subsection 08 88 49 - Radiation-Resistant Glazing": [],
                    "Subsection 08 88 53 - Security Glazing": []
                }
            },
            "Division 09 - Finishes": {
                "Section 09 20 00 - Plaster and Gypsum Board": {
                    "Subsection 09 21 00 - Gypsum Board Assemblies": [],
                    "Subsection 09 22 00 - Plaster": [],
                    "Subsection 09 23 00 - Gypsum Plastering": [],
                    "Subsection 09 29 00 - Prefinished Gypsum Board": []
                },
                "Section 09 30 00 - Tiling": {
                    "Subsection 09 31 00 - Ceramic Tiling": [],
                    "Subsection 09 32 00 - Quarry Tiling": []
                },
                "Section 09 50 00 - Ceilings": {
                    "Subsection 09 51 00 - Acoustical Ceilings": [],
                    "Subsection 09 53 00 - Wood Ceilings": []
                },
                "Section 09 60 00 - Flooring": {
                    "Subsection 09 61 00 - Resilient Flooring": [],
                    "Subsection 09 62 00 - Wood Flooring": [],
                    "Subsection 09 63 00 - Stone Flooring": [],
                    "Subsection 09 64 00 - Wood Floors": []
                },
                "Section 09 70 00 - Wall Finishes": {
                    "Subsection 09 71 00 - Wall Coverings": [],
                    "Subsection 09 72 00 - Fabric Wall Coverings": []
                },
                "Section 09 90 00 - Painting and Coating": {
                    "Subsection 09 91 00 - Exterior Painting": [],
                    "Subsection 09 92 00 - Interior Painting": [],
                    "Subsection 09 93 00 - Stains and Transparent Finishes": []
                }
            },
            "Division 10 - Specialties": {
                "Section 10 10 00 - Visual Display Boards": {
                    "Subsection 10 11 00 - Chalkboards": [],
                    "Subsection 10 12 00 - Whiteboards": []
                },
                "Section 10 20 00 - Louvers and Vents": {
                    "Subsection 10 21 00 - Fixed Louvers": [],
                    "Subsection 10 22 00 - Operable Louvers": []
                },
                "Section 10 40 00 - Fire Protection Specialties": {
                    "Subsection 10 41 00 - Fire Extinguishers": [],
                    "Subsection 10 42 00 - Fire Extinguisher Cabinets": []
                },
                "Section 10 50 00 - Lockers": {
                    "Subsection 10 51 00 - Metal Lockers": [],
                    "Subsection 10 52 00 - Plastic Lockers": []
                },
                "Section 10 70 00 - Exterior Specialties": {
                    "Subsection 10 71 00 - Exterior Sun Control Devices": [],
                    "Subsection 10 73 00 - Street Shelters": []
                }
            },
            "Division 11 - Equipment": {
                "Section 11 10 00 - Parking Control Equipment": {
                    "Subsection 11 11 00 - Parking Gates": [],
                    "Subsection 11 12 00 - Parking Meters": []
                },
                "Section 11 30 00 - Loading Dock Equipment": {
                    "Subsection 11 31 00 - Dock Levelers": [],
                    "Subsection 11 32 00 - Dock Seals": []
                },
                "Section 11 40 00 - Food Service Equipment": {
                    "Subsection 11 41 00 - Commercial Kitchen Equipment": [],
                    "Subsection 11 42 00 - Refrigeration Equipment": []
                },
                "Section 11 50 00 - Laboratory Equipment": {
                    "Subsection 11 51 00 - Laboratory Casework": [],
                    "Subsection 11 52 00 - Fume Hoods": [],
                    "Subsection 11 53 00 - Laboratory Equipment": []
                },
                "Section 11 60 00 - Athletic and Recreational Equipment": {
                    "Subsection 11 61 00 - Gymnasium Equipment": [],
                    "Subsection 11 61 43 - Proscenium Drapery and Curtains": [],
                    "Subsection 11 62 00 - Playground Equipment": []
                },
                "Section 11 70 00 - Healthcare Equipment": {},
                "Section 11 74 00 - Dental Equipment": {},
                "Subsection 11 98 00 - Detention Equipment": {}
            },
            "Division 12 - Furnishings": {
                "Section 12 20 00 - Window Treatments": {
                    "Subsection 12 21 00 - Blinds": [],
                    "Subsection 12 22 00 - Shades": [],
                    "Subsection 12 24 00 - Curtains and Drapes": []
                },
                "Section 12 30 00 - Casework": {
                    "Subsection 12 31 00 - Manufactured Wood Casework": [],
                    "Subsection 12 32 00 - Manufactured Metal Casework": [],
                    "Subsection 12 35 50 - Laboratory Casework": []
                },
                "Section 12 40 00 - Furnishings and Accessories": {
                    "Subsection 12 41 00 - Artwork": [],
                    "Subsection 12 42 00 - Decorative Accessories": []
                },
                "Section 12 50 00 - Furniture": {
                    "Subsection 12 51 00 - Office Furniture": [],
                    "Subsection 12 51 83 - Office Custom Furniture": [],
                    "Subsection 12 52 00 - Seating": [],
                    "Subsection 12 52 13 - Portable Chairs": [],
                    "Subsection 12 52 83 - Retail Custom Furniture": [],
                    "Subsection 12 53 83 - Retail Custom Furniture": [],
                    "Subsection 12 54 83 - Hospitality Custom Furniture": [],
                    "Subsection 12 55 83 - Detention Custom Furniture": [],
                    "Subsection 12 56 83 - Institutional Custom Furniture": [],
                    "Subsection 12 57 83 - Industrial Custom Furniture": [],
                    "Subsection 12 58 83 - Residential Custom Furniture": []
                },
                "Section 12 60 00 - Multiple Seating": {
                    "Subsection 12 61 00 - Fixed Audience Seating": []
                },
                "Subsection 12 92 33 - Interior Planters": {}
            },
            "Division 13 - Special Construction": {
                "Section 13 05 05 - Special Construction Systems": {},
                "Section 13 10 00 - Special Facility Components": {
                    "Subsection 13 11 00 - Cleanrooms": [],
                    "Subsection 13 12 00 - Sound and Vibration Control Rooms": []
                },
                "Section 13 20 00 - Special Purpose Rooms": {
                    "Subsection 13 21 00 - Anechoic Chambers": [],
                    "Subsection 13 22 00 - Screened Rooms": []
                },
                "Section 13 30 00 - Special Structures": {
                    "Subsection 13 31 00 - Air-Supported Structures": [],
                    "Subsection 13 32 00 - Fabric Structures": []
                },
                "Section 13 40 00 - Integrated Construction": {
                    "Subsection 13 41 00 - Integrated Automation Systems": []
                },
                "Section 13 45 00 - Radiation Protection": {
                    "Subsection 13 45 13 - Radiation Shielding": []
                },
                "Section 13 46 00 - Lightning Protection Systems": {},
                "Section 13 47 00 - Seismic Control Systems": {},
                "Section 13 48 00 - Sound Control Systems": {},
                "Section 13 49 00 - Vibration Control Systems": {},
                "Section 13 50 00 - Restoration of Special Construction": {
                    "Subsection 13 50 13 - Historic Restoration of Special Construction": []
                },
                "Section 13 51 00 - Special Construction Systems": {},
                "Section 13 52 00 - Special Purpose Systems": {},
                "Section 13 53 00 - Special Structures": {},
                "Section 13 54 00 - Special Construction Components": {},
                "Section 13 55 00 - Special Construction Fabrication": {},
                "Section 13 56 00 - Special Construction Installation": {},
                "Section 13 57 00 - Special Construction Maintenance": {},
                "Section 13 58 00 - Special Construction Repair": {},
                "Section 13 59 00 - Special Construction Removal": {}
            },
            "Division 14 - Conveying Equipment": {
                "Section 14 10 00 - Dumbwaiters": {
                    "Subsection 14 11 00 - Electric Dumbwaiters": [],
                    "Subsection 14 12 00 - Hydraulic Dumbwaiters": []
                },
                "Section 14 20 00 - Elevators": {
                    "Subsection 14 21 00 - Passenger Elevators": [],
                    "Subsection 14 22 00 - Freight Elevators": [],
                    "Subsection 14 24 00 - Hydraulic Elevators": []
                },
                "Section 14 30 00 - Escalators and Moving Walks": {
                    "Subsection 14 31 00 - Escalators": [],
                    "Subsection 14 32 00 - Moving Walks": []
                },
                "Section 14 40 00 - Lifts": {
                    "Subsection 14 41 00 - Wheelchair Lifts": [],
                    "Subsection 14 42 00 - Platform Lifts": []
                },
                "Section 14 50 00 - Material Handling Systems": {
                    "Subsection 14 51 00 - Conveyors": [],
                    "Subsection 14 52 00 - Chutes": []
                },
                "Section 14 60 00 - Hoists and Cranes": {
                    "Subsection 14 61 00 - Hoists": [],
                    "Subsection 14 62 00 - Cranes": []
                },
                "Section 14 70 00 - Vehicle Lifts": {},
                "Section 14 80 00 - Waste Handling Systems": {
                    "Subsection 14 81 00 - Waste Compactors": [],
                    "Subsection 14 82 00 - Waste Chutes": []
                }
            },
            "Division 15 - (Reserved for Future Expansion)": {
                "Section 15 00 00 - Placeholder Section A": {
                    "Subsection 15 00 10 - Placeholder Sub A.1": [],
                    "Subsection 15 00 20 - Placeholder Sub A.2": []
                }
            },
            "Division 16 - (Reserved for Future Expansion)": {
                "Section 16 00 00 - Placeholder Section B": {
                    "Subsection 16 00 10 - Placeholder Sub B.1": [],
                    "Subsection 16 00 20 - Placeholder Sub B.2": []
                }
            },
            "Division 17 - (Reserved for Future Expansion)": {
                "Section 17 00 00 - Placeholder Section C": {
                    "Subsection 17 00 10 - Placeholder Sub C.1": [],
                    "Subsection 17 00 20 - Placeholder Sub C.2": []
                }
            },
            "Division 18 - (Reserved for Future Expansion)": {
                "Section 18 00 00 - Placeholder Section D": {
                    "Subsection 18 00 10 - Placeholder Sub D.1": [],
                    "Subsection 18 00 20 - Placeholder Sub D.2": []
                }
            },
            "Division 19 - (Reserved for Future Expansion)": {
                "Section 19 00 00 - General": {
                    "Subsection 19 00 10 - Placeholder Sub E.1": [],
                    "Subsection 19 00 20 - Placeholder Sub E.2": []
                }
            },
            "Division 20 - (Reserved for Future Expansion)": {
                "Section 20 00 00 - General": {
                    "Subsection 20 00 10 - Placeholder Sub F.1": [],
                    "Subsection 20 00 20 - Placeholder Sub F.2": []
                }
            },
            "Division 21 - Fire Suppression": {
                "Section 21 05 00 - Common Work Results for Fire Suppression": {
                    "Subsection 21 05 10 - Fire Suppression Piping": [],
                    "Subsection 21 05 20 - Fire Suppression Valves": []
                },
                "Section 21 10 00 - Water-Based Fire Suppression Systems": {
                    "Subsection 21 11 00 - Wet-Pipe Sprinkler Systems": [],
                    "Subsection 21 12 00 - Dry-Pipe Sprinkler Systems": [],
                    "Subsection 21 13 00 - Preaction Sprinkler Systems": []
                },
                "Section 21 20 00 - Fire Extinguishing Systems": {
                    "Subsection 21 21 00 - Carbon Dioxide Systems": [],
                    "Subsection 21 22 00 - Clean Agent Systems": [],
                    "Subsection 21 23 00 - Foam Fire Extinguishing Systems": []
                },
                "Section 21 30 00 - Fire Pumps": {
                    "Subsection 21 31 00 - Electric Motor Driven Fire Pumps": [],
                    "Subsection 21 32 00 - Diesel Engine Driven Fire Pumps": []
                }
            },
            "Division 22 - Plumbing": {
                "Section 22 05 00 - Common Work Results for Plumbing": {
                    "Subsection 22 05 10 - Plumbing Piping": [],
                    "Subsection 22 05 20 - Plumbing Valves": []
                },
                "Section 22 10 00 - Plumbing Piping": {
                    "Subsection 22 11 00 - Domestic Water Piping": [],
                    "Subsection 22 12 00 - Sanitary Waste and Vent Piping": [],
                    "Subsection 22 13 00 - Storm Drainage Piping": []
                },
                "Section 22 20 00 - Plumbing Fixtures": {
                    "Subsection 22 21 00 - Water Closets": [],
                    "Subsection 22 22 00 - Lavatories": [],
                    "Subsection 22 23 00 - Urinals": []
                },
                "Section 22 30 00 - Plumbing Equipment": {
                    "Subsection 22 31 00 - Water Heaters": [],
                    "Subsection 22 32 00 - Water Coolers": []
                },
                "Section 22 40 00 - Plumbing Systems": {
                    "Subsection 22 41 00 - Domestic Water Systems": [],
                    "Subsection 22 42 00 - Sanitary Sewer Systems": [],
                    "Subsection 22 43 00 - Storm Drainage Systems": []
                },
                "Section 22 50 00 - Gas and Vacuum Systems": {
                    "Subsection 22 51 00 - Medical Gas Systems": [],
                    "Subsection 22 52 00 - Laboratory Gas Systems": [],
                    "Subsection 22 53 00 - Vacuum Systems": []
                },
                "Section 22 60 00 - Special Plumbing Systems": {
                    "Subsection 22 61 00 - Deionized Water Systems": [],
                    "Subsection 22 62 00 - Reverse Osmosis Systems": []
                }
            },
            "Division 23 - Heating, Ventilating, and Air Conditioning (HVAC)": {
                "Section 23 05 00 - Common Work Results for HVAC": {
                    "Subsection 23 05 10 - HVAC Piping": [],
                    "Subsection 23 05 20 - HVAC Valves": []
                },
                "Section 23 10 00 - Facility Fuel Systems": {
                    "Subsection 23 11 00 - Fuel Oil Storage Tanks": [],
                    "Subsection 23 12 00 - Natural Gas Piping": []
                },
                "Section 23 20 00 - HVAC Air Distribution": {
                    "Subsection 23 21 00 - HVAC Ducts": [],
                    "Subsection 23 22 00 - Air Outlets and Inlets": []
                },
                "Section 23 30 00 - HVAC Equipment": {
                    "Subsection 23 31 00 - Air Handling Units": [],
                    "Subsection 23 32 00 - Fans": []
                },
                "Section 23 40 00 - HVAC Systems": {
                    "Subsection 23 41 00 - Central Heating Systems": [],
                    "Subsection 23 42 00 - Central Cooling Systems": []
                },
                "Section 23 50 00 - Central Heating Equipment": {
                    "Subsection 23 51 00 - Boilers": [],
                    "Subsection 23 52 00 - Furnaces": []
                },
                "Section 23 60 00 - Central Cooling Equipment": {
                    "Subsection 23 61 00 - Chillers": [],
                    "Subsection 23 62 00 - Cooling Towers": []
                },
                "Section 23 70 00 - Central HVAC Equipment": {
                    "Subsection 23 71 00 - Packaged HVAC Equipment": [],
                    "Subsection 23 72 00 - Air Handling Units": []
                },
                "Section 23 80 00 - Decentralized HVAC Equipment": {
                    "Subsection 23 81 00 - Unit Heaters": [],
                    "Subsection 23 82 00 - Fan Coil Units": []
                },
                "Section 23 90 00 - HVAC Controls": {
                    "Subsection 23 91 00 - Building Automation System (BAS)": [],
                    "Subsection 23 92 00 - Energy Management System (EMS)": []
                }
            },
            "Division 24 - (Reserved for Future Expansion)": {
                "Section 24 00 00 - General": {
                    "Subsection 24 00 10 - Placeholder Sub G.1": [],
                    "Subsection 24 00 20 - Placeholder Sub G.2": []
                }
            },
            "Division 25 - Integrated Automation": {
                "Section 25 05 00 - Common Work Results for Integrated Automation": {
                    "Subsection 25 05 10 - Integrated Automation Wiring": [],
                    "Subsection 25 05 20 - Integrated Automation Control Panels": []
                },
                "Section 25 10 00 - Integrated Automation Control Systems": {
                    "Subsection 25 11 00 - Building Automation System (BAS)": [],
                    "Subsection 25 12 00 - Energy Management System (EMS)": [],
                    "Subsection 25 13 00 - Lighting Control System": []
                },
                "Section 25 20 00 - Integrated Automation Network": {
                    "Subsection 25 21 00 - Integrated Automation Network Devices": [],
                    "Subsection 25 22 00 - Integrated Automation Network Software": []
                },
                "Section 25 30 00 - Integrated Automation Equipment": {
                    "Subsection 25 31 00 - Integrated Automation Servers": [],
                    "Subsection 25 32 00 - Integrated Automation Workstations": []
                },
                "Section 25 40 00 - Integrated Automation Applications": {
                    "Subsection 25 41 00 - Integrated Automation Software Applications": [],
                    "Subsection 25 42 00 - Integrated Automation Database Applications": []
                }
            },
            "Division 26 - Electrical": {
                "Section 26 05 00 - Common Work Results for Electrical": {
                    "Subsection 26 05 10 - Electrical Wiring": [],
                    "Subsection 26 05 20 - Electrical Boxes and Fittings": []
                },
                "Section 26 10 00 - Wiring Devices": {
                    "Subsection 26 11 00 - Receptacles": [],
                    "Subsection 26 12 00 - Switches": [],
                    "Subsection 26 13 00 - Wall Plates": []
                },
                "Section 26 20 00 - Low-Voltage Distribution": {
                    "Subsection 26 21 00 - Panelboards": [],
                    "Subsection 26 22 00 - Transformers": [],
                    "Subsection 26 24 00 - Switchboards": []
                },
                "Section 26 30 00 - Power Generation": {
                    "Subsection 26 31 00 - Diesel Generators": [],
                    "Subsection 26 32 00 - Gas Turbine Generators": []
                },
                "Section 26 40 00 - Electrical and Cathodic Protection": {
                    "Subsection 26 41 00 - Grounding and Bonding": [],
                    "Subsection 26 42 00 - Surge Protection": []
                },
                "Section 26 50 00 - Lighting": {
                    "Subsection 26 51 00 - Interior Lighting": [],
                    "Subsection 26 52 00 - Exterior Lighting": [],
                    "Subsection 26 53 00 - Emergency Lighting": []
                }
            },
            "Division 27 - Communications": {
                "Section 27 05 00 - Common Work Results for Communications": {
                    "Subsection 27 05 10 - Communications Wiring": [],
                    "Subsection 27 05 20 - Communications Cabinets and Racks": []
                },
                "Section 27 10 00 - Structured Cabling": {
                    "Subsection 27 11 00 - Copper Cabling": [],
                    "Subsection 27 12 00 - Fiber Optic Cabling": []
                },
                "Section 27 20 00 - Data Communications": {
                    "Subsection 27 21 00 - Network Equipment": [],
                    "Subsection 27 22 00 - Wireless Access Points": []
                },
                "Section 27 30 00 - Voice Communications": {
                    "Subsection 27 31 00 - Telephone Systems": [],
                    "Subsection 27 32 00 - Intercom Systems": []
                },
                "Section 27 40 00 - Audio-Video Communications": {
                    "Subsection 27 41 00 - Audio Systems": [],
                    "Subsection 27 42 00 - Video Systems": [],
                    "Subsection 27 43 00 - Paging Systems": []
                },
                "Section 27 50 00 - Distributed Communications and Monitoring": {
                    "Subsection 27 51 00 - Distributed Antenna Systems (DAS)": [],
                    "Subsection 27 52 00 - Wireless Communication Systems": []
                }
            },
            "Division 28 - Electronic Safety and Security": {
                "Section 28 05 00 - Common Work Results for Electronic Safety and Security": {
                    "Subsection 28 05 10 - Security Wiring": [],
                    "Subsection 28 05 20 - Security Control Panels": []
                },
                "Section 28 10 00 - Access Control": {
                    "Subsection 28 11 00 - Card Access Systems": [],
                    "Subsection 28 12 00 - Biometric Access Systems": []
                },
                "Section 28 20 00 - Electronic Surveillance": {
                    "Subsection 28 21 00 - Video Surveillance Systems (CCTV)": [],
                    "Subsection 28 22 00 - Intrusion Detection Systems": []
                },
                "Section 28 30 00 - Fire Detection and Alarm": {
                    "Subsection 28 31 00 - Fire Alarm Control Panels": [],
                    "Subsection 28 32 00 - Fire Alarm Devices": []
                },
                "Section 28 40 00 - Electronic Safety and Security Specialties": {
                    "Subsection 28 41 00 - Emergency Call Systems": [],
                    "Subsection 28 42 00 - Mass Notification Systems": []
                }
            },
            "Division 29 - (Reserved for Future Expansion)": {
                "Section 29 00 00 - General": {
                    "Subsection 29 00 10 - Placeholder Sub H.1": [],
                    "Subsection 29 00 20 - Placeholder Sub H.2": []
                }
            },
            "Division 30 - (Reserved for Future Expansion)": {
                "Section 30 00 00 - General": {
                    "Subsection 30 00 10 - Placeholder Sub I.1": [],
                    "Subsection 30 00 20 - Placeholder Sub I.2": []
                }
            },
            "Division 31 - Earthwork": {
                "Section 31 05 00 - Common Work Results for Earthwork": {
                    "Subsection 31 05 10 - Geotextiles": [],
                    "Subsection 31 05 20 - Soil Amendments": []
                },
                "Section 31 10 00 - Site Clearing": {
                    "Subsection 31 11 00 - Clearing and Grubbing": [],
                    "Subsection 31 12 00 - Tree Removal": [],
                    "Subsection 31 13 00 - Selective Tree and Shrub Removal": []
                },
                "Section 31 20 00 - Earth Moving": {
                    "Subsection 31 21 00 - Excavation": [],
                    "Subsection 31 22 00 - Grading": [],
                    "Subsection 31 23 00 - Fill": []
                },
                "Section 31 30 00 - Earthwork Methods": {
                    "Subsection 31 31 00 - Soil Treatment": [],
                    "Subsection 31 32 00 - Soil Stabilization": []
                },
                "Section 31 40 00 - Shoring and Underpinning": {
                    "Subsection 31 41 00 - Shoring": [],
                    "Subsection 31 42 00 - Underpinning": []
                },
                "Section 31 50 00 - Excavation Support and Protection": {
                    "Subsection 31 51 00 - Slurry Walls": [],
                    "Subsection 31 52 00 - Soldier Pile and Lagging": []
                },
                "Section 31 60 00 - Special Foundations and Load-Bearing Elements": {
                    "Subsection 31 61 00 - Driven Piles": [],
                    "Subsection 31 62 00 - Bored Piles": []
                }
            },
            "Division 32 - Exterior Improvements": {
                "Section 32 05 00 - Common Work Results for Exterior Improvements": {
                    "Subsection 32 05 10 - Site Preparation": [],
                    "Subsection 32 05 20 - Drainage": []
                },
                "Section 32 10 00 - Bases, Ballasts, and Paving": {
                    "Subsection 32 11 00 - Aggregate Bases": [],
                    "Subsection 32 12 00 - Asphalt Paving": [],
                    "Subsection 32 13 00 - Concrete Paving": []
                },
                "Section 32 20 00 - Landscape Grading": {
                    "Subsection 32 21 00 - Fine Grading": [],
                    "Subsection 32 22 00 - Rough Grading": []
                },
                "Section 32 30 00 - Site Improvements": {
                    "Subsection 32 31 00 - Fences and Gates": [],
                    "Subsection 32 32 00 - Retaining Walls": [],
                    "Subsection 32 33 00 - Site Furnishings": []
                },
                "Section 32 40 00 - Planting": {
                    "Subsection 32 41 00 - Trees": [],
                    "Subsection 32 42 00 - Shrubs": [],
                    "Subsection 32 43 00 - Groundcovers": []
                },
                "Section 32 80 00 - Irrigation": {
                    "Subsection 32 81 00 - Sprinkler Irrigation Systems": [],
                    "Subsection 32 82 00 - Drip Irrigation Systems": []
                },
                "Section 32 90 00 - Planting": {
                    "Subsection 32 91 00 - Planting Preparation": [],
                    "Subsection 32 92 00 - Plants": [],
                    "Subsection 32 93 00 - Lawns and Grasses": []
                }
            },
            "Division 33 - Utilities": {
                "Section 33 05 00 - Common Work Results for Utilities": {
                    "Subsection 33 05 10 - Utility Excavation and Backfill": [],
                    "Subsection 33 05 20 - Utility Piping": []
                },
                "Section 33 10 00 - Water Utilities": {
                    "Subsection 33 11 00 - Water Distribution Piping": [],
                    "Subsection 33 12 00 - Fire Hydrants": [],
                    "Subsection 33 13 00 - Water Storage Tanks": []
                },
                "Section 33 20 00 - Wastewater Utilities": {
                    "Subsection 33 21 00 - Sanitary Sewer Piping": [],
                    "Subsection 33 22 00 - Manholes": []
                },
                "Section 33 30 00 - Sanitary Sewerage Utilities": {
                    "Subsection 33 31 00 - Sanitary Sewer Piping": [],
                    "Subsection 33 32 00 - Manholes": []
                },
                "Section 33 40 00 - Storm Utility Drainage": {
                    "Subsection 33 41 00 - Storm Drain Piping": [],
                    "Subsection 33 42 00 - Catch Basins and Inlets": []
                },
                "Section 33 50 00 - Fuel Distribution Utilities": {
                    "Subsection 33 51 00 - Natural Gas Distribution": [],
                    "Subsection 33 52 00 - Propane Distribution": []
                },
                "Section 33 60 00 - Steam and Condensate Utilities": {
                    "Subsection 33 61 00 - Steam Distribution Piping": [],
                    "Subsection 33 62 00 - Condensate Return Piping": []
                },
                "Section 33 70 00 - Electrical Utilities": {
                    "Subsection 33 71 00 - Electrical Power Transmission": [],
                    "Subsection 33 72 00 - Electrical Power Distribution": []
                },
                "Section 33 80 00 - Communications Utilities": {
                    "Subsection 33 81 00 - Telephone Utilities": [],
                    "Subsection 33 82 00 - Cable Television Utilities": []
                }
            },
            "Division 34 - Transportation": {
                "Section 34 05 00 - Common Work Results for Transportation": {
                    "Subsection 34 05 10 - Roadway Markings": [],
                    "Subsection 34 05 20 - Traffic Signs": []
                },
                "Section 34 10 00 - Roadway Construction": {
                    "Subsection 34 11 00 - Flexible Pavement": [],
                    "Subsection 34 12 00 - Rigid Pavement": []
                },
                "Section 34 30 00 - Airport Construction": {
                    "Subsection 34 31 00 - Runways": [],
                    "Subsection 34 32 00 - Taxiways": []
                },
                "Section 34 40 00 - Traffic Control": {
                    "Subsection 34 41 00 - Traffic Signals": [],
                    "Subsection 34 42 00 - Roadway Striping": []
                },
                "Section 34 50 00 - Bridges": {
                    "Subsection 34 51 00 - Pedestrian Bridges": [],
                    "Subsection 34 52 00 - Vehicular Bridges": []
                },
                "Section 34 60 00 - Tunnels": {
                    "Subsection 34 61 00 - Vehicular Tunnels": [],
                    "Subsection 34 62 00 - Pedestrian Tunnels": []
                },
                "Section 34 70 00 - Bridges": {
                    "Subsection 34 71 00 - Pedestrian Bridges": [],
                    "Subsection 34 72 00 - Vehicular Bridges": []
                },
                "Section 34 80 00 - Marine Transportation": {
                    "Subsection 34 81 00 - Docks": [],
                    "Subsection 34 82 00 - Piers": []
                },
                "Section 34 90 00 - Air Transportation": {
                    "Subsection 34 91 00 - Air Traffic Control Towers": [],
                    "Subsection 34 92 00 - Aircraft Hangars": []
                }
            },
            "Division 35 - Waterway and Marine Construction": {
                "Section 35 05 00 - Common Work Results for Waterway and Marine Construction": {
                    "Subsection 35 05 10 - Marine Coatings": [],
                    "Subsection 35 05 20 - Marine Fenders": []
                },
                "Section 35 10 00 - Marine Facilities": {
                    "Subsection 35 11 00 - Docks and Piers": [],
                    "Subsection 35 12 00 - Bulkheads": []
                },
                "Section 35 20 00 - Dredging and Earthwork": {
                    "Subsection 35 21 00 - Hydraulic Dredging": [],
                    "Subsection 35 22 00 - Mechanical Dredging": []
                },
                "Section 35 30 00 - Dams and Reservoirs": {
                    "Subsection 35 31 00 - Earth Dams": [],
                    "Subsection 35 32 00 - Concrete Dams": []
                },
                "Section 35 40 00 - Navigation Aids": {
                    "Subsection 35 41 00 - Buoys": [],
                    "Subsection 35 42 00 - Lighthouses": []
                },
                "Section 35 50 00 - Marine Structures": {
                    "Subsection 35 51 00 - Breakwaters": [],
                    "Subsection 35 52 00 - Jetties": []
                },
                "Section 35 60 00 - Marine Equipment": {
                    "Subsection 35 61 00 - Cranes for Marine Use": [],
                    "Subsection 35 62 00 - Winches": []
                },
                "Section 35 70 00 - Marine Systems": {
                    "Subsection 35 71 00 - Marine Power Systems": [],
                    "Subsection 35 72 00 - Marine Communication Systems": []
                }
            },
            "Division 36 - (Reserved for Future Expansion)": {
                "Section 36 00 00 - General": {
                    "Subsection 36 00 10 - Placeholder Sub J.1": [],
                    "Subsection 36 00 20 - Placeholder Sub J.2": []
                }
            },
            "Division 37 - (Reserved for Future Expansion)": {
                "Section 37 00 00 - General": {
                    "Subsection 37 00 10 - Placeholder Sub K.1": [],
                    "Subsection 37 00 20 - Placeholder Sub K.2": []
                }
            },
            "Division 38 - (Reserved for Future Expansion)": {
                "Section 38 00 00 - General": {
                    "Subsection 38 00 10 - Placeholder Sub L.1": [],
                    "Subsection 38 00 20 - Placeholder Sub L.2": []
                }
            },
            "Division 39 - (Reserved for Future Expansion)": {
                "Section 39 00 00 - General": {
                    "Subsection 39 00 10 - Placeholder Sub M.1": [],
                    "Subsection 39 00 20 - Placeholder Sub M.2": []
                }
            },
            "Division 40 - Process Interconnections": {
                "Section 40 05 00 - Common Work Results for Process Interconnections": {
                    "Subsection 40 05 10 - Process Control Panels": [],
                    "Subsection 40 05 20 - Process Instrumentation": []
                },
                "Section 40 10 00 - Process Piping": {
                    "Subsection 40 11 00 - Chemical Process Piping": [],
                    "Subsection 40 12 00 - Industrial Gas Piping": [],
                    "Subsection 40 13 00 - Steam and Condensate Piping": []
                },
                "Section 40 20 00 - Process Pumps": {
                    "Subsection 40 21 00 - Centrifugal Process Pumps": [],
                    "Subsection 40 22 00 - Positive Displacement Process Pumps": []
                },
                "Section 40 30 00 - Process Valves": {
                    "Subsection 40 31 00 - Ball Valves": [],
                    "Subsection 40 32 00 - Gate Valves": []
                },
                "Section 40 40 00 - Process Controls": {
                    "Subsection 40 41 00 - Distributed Control Systems (DCS)": [],
                    "Subsection 40 42 00 - Programmable Logic Controllers (PLC)": []
                },
                "Section 40 50 00 - Process Instrumentation": {
                    "Subsection 40 51 00 - Pressure Transmitters": [],
                    "Subsection 40 52 00 - Temperature Sensors": []
                },
                "Section 40 60 00 - Process Storage Tanks": {
                    "Subsection 40 61 00 - Chemical Storage Tanks": [],
                    "Subsection 40 62 00 - Fuel Storage Tanks": []
                }
            },
            "Division 41 - Material Processing and Handling Equipment": {
                "Section 41 05 00 - Common Work Results for Material Processing and Handling Equipment": {
                    "Subsection 41 05 10 - Material Handling Controls": [],
                    "Subsection 41 05 20 - Safety Devices for Material Handling": []
                },
                "Section 41 10 00 - Conveying Equipment": {
                    "Subsection 41 11 00 - Belt Conveyors": [],
                    "Subsection 41 12 00 - Screw Conveyors": [],
                    "Subsection 41 13 00 - Roller Conveyors": []
                },
                "Section 41 20 00 - Hoists and Cranes": {
                    "Subsection 41 21 00 - Overhead Cranes": [],
                    "Subsection 41 22 00 - Gantry Cranes": []
                },
                "Section 41 30 00 - Material Processing Equipment": {
                    "Subsection 41 31 00 - Crushers": [],
                    "Subsection 41 32 00 - Screens": []
                },
                "Section 41 40 00 - Material Handling Equipment": {
                    "Subsection 41 41 00 - Forklifts": [],
                    "Subsection 41 42 00 - Pallet Jacks": []
                },
                "Section 41 50 00 - Storage and Retrieval Systems": {
                    "Subsection 41 51 00 - Automated Storage and Retrieval Systems (ASRS)": [],
                    "Subsection 41 52 00 - Racking Systems": []
                },
                "Section 41 60 00 - Packaging Equipment": {
                    "Subsection 41 61 00 - Wrapping Machines": [],
                    "Subsection 41 62 00 - Labeling Machines": []
                }
            },
            "Division 42 - Process Heating, Cooling, and Drying Equipment": {
                "Section 42 05 00 - Common Work Results for Process Heating, Cooling, and Drying Equipment": {
                    "Subsection 42 05 10 - Temperature Control Systems": [],
                    "Subsection 42 05 20 - Heat Exchangers": []
                },
                "Section 42 10 00 - Heating Equipment": {
                    "Subsection 42 11 00 - Industrial Furnaces": [],
                    "Subsection 42 12 00 - Ovens": []
                },
                "Section 42 20 00 - Cooling Equipment": {
                    "Subsection 42 21 00 - Industrial Chillers": [],
                    "Subsection 42 22 00 - Cooling Towers for Process": []
                },
                "Section 42 30 00 - Drying Equipment": {
                    "Subsection 42 31 00 - Industrial Dryers": [],
                    "Subsection 42 32 00 - Kilns": []
                },
                "Section 42 40 00 - Evaporators and Crystallizers": {
                    "Subsection 42 41 00 - Evaporators": [],
                    "Subsection 42 42 00 - Crystallizers": []
                },
                "Section 42 50 00 - Refrigeration Equipment": {
                    "Subsection 42 51 00 - Industrial Refrigeration Systems": [],
                    "Subsection 42 52 00 - Freezers": []
                }
            },
            "Division 43 - Process Gas and Liquid Handling, Purification, and Storage Equipment": {
                "Section 43 05 00 - Common Work Results for Process Gas and Liquid Handling, Purification, and Storage Equipment": {
                    "Subsection 43 05 10 - Flow Meters": [],
                    "Subsection 43 05 20 - Level Sensors": []
                },
                "Section 43 10 00 - Process Gas Handling Equipment": {
                    "Subsection 43 11 00 - Gas Compressors": [],
                    "Subsection 43 12 00 - Gas Blowers": [],
                    "Subsection 43 13 00 - Gas Fans": []
                },
                "Section 43 20 00 - Process Liquid Handling Equipment": {
                    "Subsection 43 21 00 - Liquid Pumps": [],
                    "Subsection 43 22 00 - Liquid Mixers": []
                },
                "Section 43 30 00 - Process Gas Purification Equipment": {
                    "Subsection 43 31 00 - Gas Scrubbers": [],
                    "Subsection 43 32 00 - Gas Absorbers": []
                },
                "Section 43 40 00 - Process Liquid Purification Equipment": {
                    "Subsection 43 41 00 - Liquid Filters": [],
                    "Subsection 43 42 00 - Liquid Separators": []
                },
                "Section 43 50 00 - Process Gas Storage Equipment": {
                    "Subsection 43 51 00 - Gas Storage Tanks": [],
                    "Subsection 43 52 00 - Gas Cylinders": []
                },
                "Section 43 60 00 - Process Liquid Storage Equipment": {
                    "Subsection 43 61 00 - Liquid Storage Tanks": [],
                    "Subsection 43 62 00 - Liquid Drums": []
                },
                "Section 43 70 00 - Process Pumping Equipment": {
                    "Subsection 43 71 00 - Centrifugal Pumps": [],
                    "Subsection 43 72 00 - Positive Displacement Pumps": []
                },
                "Section 43 80 00 - Process Piping and Valves": {
                    "Subsection 43 81 00 - Process Piping": [],
                    "Subsection 43 82 00 - Process Valves": []
                }
            },
            "Division 44 - Pollution Control Equipment": {
                "Section 44 05 00 - Common Work Results for Pollution Control Equipment": {
                    "Subsection 44 05 10 - Emission Monitoring Systems": [],
                    "Subsection 44 05 20 - Waste Stream Analysis": []
                },
                "Section 44 10 00 - Air Pollution Control Equipment": {
                    "Subsection 44 11 00 - Baghouses": [],
                    "Subsection 44 12 00 - Electrostatic Precipitators": [],
                    "Subsection 44 13 00 - Wet Scrubbers": []
                },
                "Section 44 20 00 - Water Pollution Control Equipment": {
                    "Subsection 44 21 00 - Wastewater Treatment Plants": [],
                    "Subsection 44 22 00 - Oil-Water Separators": [],
                    "Subsection 44 23 00 - Dissolved Air Flotation (DAF) Units": []
                },
                "Section 44 30 00 - Soil Pollution Control Equipment": {
                    "Subsection 44 31 00 - Soil Vapor Extraction Systems": [],
                    "Subsection 44 32 00 - Bioremediation Systems": []
                },
                "Section 44 40 00 - Noise Pollution Control Equipment": {
                    "Subsection 44 41 00 - Acoustic Enclosures": [],
                    "Subsection 44 42 00 - Noise Barriers": []
                },
                "Section 44 50 00 - Waste Pollution Control Equipment": {
                    "Subsection 44 51 00 - Incinerators": [],
                    "Subsection 44 52 00 - Landfill Gas Collection Systems": []
                },
                "Section 44 60 00 - Radiation Pollution Control Equipment": {
                    "Subsection 44 61 00 - Radiation Shielding": [],
                    "Subsection 44 62 00 - Radioactive Waste Storage": []
                },
                "Section 44 70 00 - Hazardous Material Pollution Control Equipment": {
                    "Subsection 44 71 00 - Hazardous Waste Incinerators": [],
                    "Subsection 44 72 00 - Hazardous Waste Storage Facilities": []
                }
            },
            "Division 45 - Industry-Specific Manufacturing Equipment": {
                "Section 45 05 00 - Common Work Results for Industry-Specific Manufacturing Equipment": {
                    "Subsection 45 05 10 - Automated Assembly Systems": [],
                    "Subsection 45 05 20 - Robotics": []
                },
                "Section 45 10 00 - Food and Beverage Processing Equipment": {
                    "Subsection 45 11 00 - Mixing Equipment": [],
                    "Subsection 45 12 00 - Filling Equipment": [],
                    "Subsection 45 13 00 - Packaging Equipment": []
                },
                "Section 45 20 00 - Pharmaceutical Manufacturing Equipment": {
                    "Subsection 45 21 00 - Tablet Presses": [],
                    "Subsection 45 22 00 - Capsule Fillers": []
                },
                "Section 45 30 00 - Chemical Manufacturing Equipment": {
                    "Subsection 45 31 00 - Reactors": [],
                    "Subsection 45 32 00 - Distillation Columns": []
                },
                "Section 45 40 00 - Textile Manufacturing Equipment": {
                    "Subsection 45 41 00 - Looms": [],
                    "Subsection 45 42 00 - Spinning Machines": []
                },
                "Section 45 50 00 - Paper and Pulp Manufacturing Equipment": {
                    "Subsection 45 51 00 - Paper Machines": [],
                    "Subsection 45 52 00 - Pulp Digesters": []
                },
                "Section 45 60 00 - Metal Manufacturing Equipment": {
                    "Subsection 45 61 00 - CNC Machines": [],
                    "Subsection 45 62 00 - Welding Equipment": []
                },
                "Section 45 70 00 - Wood Manufacturing Equipment": {
                    "Subsection 45 71 00 - Saws": [],
                    "Subsection 45 72 00 - Planers": []
                },
                "Section 45 80 00 - Plastic and Rubber Manufacturing Equipment": {
                    "Subsection 45 81 00 - Injection Molding Machines": [],
                    "Subsection 45 82 00 - Extruders": []
                },
                "Section 45 90 00 - Other Industry-Specific Manufacturing Equipment": {
                    "Subsection 45 91 00 - Semiconductor Manufacturing Equipment": [],
                    "Subsection 45 92 00 - Printing Equipment": []
                }
            },
            "Division 46 - Water and Wastewater Equipment": {
                "Section 46 05 00 - Common Work Results for Water and Wastewater Equipment": {
                    "Subsection 46 05 10 - SCADA for Water/Wastewater": [],
                    "Subsection 46 05 20 - Chemical Feed Systems": []
                },
                "Section 46 10 00 - Water Treatment Equipment": {
                    "Subsection 46 11 00 - Filtration Equipment": [],
                    "Subsection 46 12 00 - Disinfection Equipment": [],
                    "Subsection 46 13 00 - Aeration Equipment": []
                },
                "Section 46 20 00 - Wastewater Treatment Equipment": {
                    "Subsection 46 21 00 - Aeration Systems": [],
                    "Subsection 46 22 00 - Sludge Dewatering Equipment": []
                }
            },
            "Division 47 - (Reserved for Future Expansion)": {
                "Section 47 00 00 - General": {
                    "Subsection 47 00 10 - Placeholder Sub N.1": [],
                    "Subsection 47 00 20 - Placeholder Sub N.2": []
                }
            },
            "Division 48 - Electrical Power Generation": {
                "Section 48 05 00 - Common Work Results for Electrical Power Generation": {
                    "Subsection 48 05 10 - Power Control Systems": [],
                    "Subsection 48 05 20 - Electrical Safety Systems": []
                },
                "Section 48 10 00 - Electrical Power Generation Equipment": {
                    "Subsection 48 11 00 - Generators": [],
                    "Subsection 48 12 00 - Turbines": [],
                    "Subsection 48 13 00 - Solar Photovoltaic Equipment": []
                },
                "Section 48 20 00 - Electrical Power Storage": {
                    "Subsection 48 21 00 - Battery Energy Storage Systems": []
                },
                "Section 48 30 00 - Fuel Cells": {
                    "Subsection 48 31 00 - Hydrogen Fuel Cells": []
                }
            },
            "Division 49 - (Reserved for Future Expansion)": {
                "Section 49 00 00 - General": {
                    "Subsection 49 00 10 - Placeholder Sub O.1": [],
                    "Subsection 49 00 20 - Placeholder Sub O.2": []
                }
            }
        };

        // DOM elements
        const loginPage = document.getElementById('login-page');
        const mainApp = document.getElementById('main-app');
        const loginForm = document.getElementById('login-form');
        const roleSelect = document.getElementById('role-select');
        const usernameInput = document.getElementById('username-input');
        const passwordInput = document.getElementById('password-input');
        const loginErrorMessage = document.getElementById('login-error-message');

        const divisionSelect = document.getElementById('division-select');
        const sectionSelect = document.getElementById('section-select');
        const subsectionSelect = document.getElementById('subsection-select'); // Now CSI Section (Detailed)
        const itemSelect = document.getElementById('item-select'); // NEW: CSI Item (Detailed)

        const attributeInputSection = document.getElementById('attribute-input-section');
        const currentItemNameSpan = document.getElementById('current-item-name'); // Updated from currentSubsectionNameSpan
        const attributesContainer = document.getElementById('attributes-container');
        const saveAllAttributesBtn = document.getElementById('save-all-attributes-btn');
        const collectedDataSection = document.getElementById('collected-data-section');
        const collectedDataBody = document.getElementById('collected-data-body');
        const exportExcelBtn = document.getElementById('export-excel-btn');
        const messageBox = document.getElementById('message-box');
        const messageText = document.getElementById('message-text');
        const messageBoxOkBtn = document.getElementById('message-box-ok-btn');

        // Modal elements for modifying sections
        const modifySectionsBtn = document.getElementById('modify-sections-btn');
        const modifySectionsModal = document.getElementById('modify-sections-modal');
        const closeModifySectionsModalBtn = document.getElementById('close-modify-sections-modal');
        const modalDivisionNameSpan = document.getElementById('modal-division-name');
        const modalDivisionCodeInput = document.getElementById('modal-division-code');
        const modalSectionsList = document.getElementById('modal-sections-list');
        const newSectionNumInput = document.getElementById('new-section-num');
        const newSectionDescInput = document.getElementById('new-section-desc');
        const addNewSectionBtn = document.getElementById('add-new-section-btn');
        const saveSectionsModalBtn = document.getElementById('save-sections-modal-btn');
        const cancelSectionsModalBtn = document.getElementById('cancel-sections-modal-btn');

        // Modal elements for modifying subsections (CSI Section (Detailed))
        const modifySubsectionsBtn = document.getElementById('modify-subsections-btn');
        const modifySubsectionsModal = document.getElementById('modify-subsections-modal');
        const closeModifySubsectionsModalBtn = document.getElementById('close-modify-subsections-modal');
        const modalSubsectionDivisionNameSpan = document.getElementById('modal-subsection-division-name');
        const modalSubsectionSectionNameSpan = document.getElementById('modal-subsection-section-name');
        const modalSubsectionDivisionCodeInput = document.getElementById('modal-subsection-division-code');
        const modalSubsectionSectionCodeInput = document.getElementById('modal-subsection-section-code');
        const modalSubsectionsList = document.getElementById('modal-subsections-list');
        const newSubsectionNumInput = document.getElementById('new-subsection-num');
        const newSubsectionDescInput = document.getElementById('new-subsection-desc');
        const addNewSubsectionBtn = document.getElementById('add-new-subsection-btn');
        const saveSubsectionsModalBtn = document.getElementById('save-subsections-modal-btn');
        const cancelSubsectionsModalBtn = document.getElementById('cancel-subsections-modal-btn');

        // Modal elements for modifying items (NEW: CSI Item (Detailed))
        const modifyItemsBtn = document.getElementById('modify-items-btn');
        const modifyItemsModal = document.getElementById('modify-items-modal');
        const closeModifyItemsModalBtn = document.getElementById('close-modify-items-modal');
        const modalItemDivisionNameSpan = document.getElementById('modal-item-division-name');
        const modalItemSectionNameSpan = document.getElementById('modal-item-section-name');
        const modalItemSubsectionNameSpan = document.getElementById('modal-item-subsection-name');
        const modalItemDivisionCodeInput = document.getElementById('modal-item-division-code');
        const modalItemSectionCodeInput = document.getElementById('modal-item-section-code');
        const modalItemSubsectionCodeInput = document.getElementById('modal-item-subsection-code');
        const modalItemsList = document.getElementById('modal-items-list');
        const newItemNumInput = document.getElementById('new-item-num');
        const newItemDescInput = document.getElementById('new-item-desc');
        const addNewItemBtn = document.getElementById('add-new-item-btn');
        const saveItemsModalBtn = document.getElementById('save-items-modal-btn');
        const cancelItemsModalBtn = document.getElementById('cancel-items-modal-btn');


        // Global variable to store collected attributes (session-based)
        let collectedData = [];
        let currentAttributeIdCounter = 0; // To generate unique IDs for new attributes
        let currentUserRole = null; // Stores the role of the logged-in user

        // User credentials (for demonstration purposes only, in a real app this would be server-side)
        const users = {
            developer: { username: 'ziadahmed', password: 'ziad123' },
            editor: { username: 'user1', password: 'user1' }
        };


        // Function to show a message box
        function showMessage(message) {
            messageText.textContent = message;
            messageBox.classList.remove('hidden');
        }

        // Function to populate dropdowns
        function populateDropdown(selectElement, options) {
            // Determine the default option text based on the select element's ID
            let defaultOptionText = '';
            if (selectElement.id === 'division-select') {
                defaultOptionText = 'Select Division';
            } else if (selectElement.id === 'section-select') {
                defaultOptionText = 'Select Section';
            } else if (selectElement.id === 'subsection-select') {
                defaultOptionText = 'Select Detailed Section';
            } else if (selectElement.id === 'item-select') { // NEW
                defaultOptionText = 'Select Item';
            }

            selectElement.innerHTML = `<option value="">${defaultOptionText}</option>`;
            options.forEach(option => {
                const opt = document.createElement('option');
                opt.value = option;
                opt.textContent = option;
                selectElement.appendChild(opt);
            });
        }

        /**
         * Renders the standard value input fields for a given attribute.
         * Ensures the "Add" button is always on the last row and all rows have "Remove" buttons.
         * @param {HTMLElement} standardValuesContainer - The container div for standard values.
         * @param {Array<Object>} valuesArray - An array of {value: string, unit: string} for existing values.
         */
        function renderStandardValueInputs(standardValuesContainer, valuesArray) {
            standardValuesContainer.innerHTML = ''; // Clear existing inputs

            // If no values, add one empty row with Add and Remove
            if (valuesArray.length === 0) {
                const newDiv = document.createElement('div');
                newDiv.className = 'flex gap-2 mt-2';
                newDiv.innerHTML = `
                    <input type="text" class="standard-value-input flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Value">
                    <input type="text" class="standard-unit-input w-24 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Unit">
                    <button type="button" class="add-value-btn px-3 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>
                    <button type="button" class="remove-value-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                standardValuesContainer.appendChild(newDiv);
                return;
            }

            // Render existing values
            valuesArray.forEach((sv, index) => {
                const newDiv = document.createElement('div');
                newDiv.className = 'flex gap-2 mt-2';
                const isLastRow = (index === valuesArray.length - 1);

                newDiv.innerHTML = `
                    <input type="text" class="standard-value-input flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Value" value="${sv.value}">
                    <input type="text" class="standard-unit-input w-24 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Unit" value="${sv.unit}">
                    ${isLastRow ? `<button type="button" class="add-value-btn px-3 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>` : ''}
                    <button type="button" class="remove-value-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                standardValuesContainer.appendChild(newDiv);
            });
        }

        /**
         * Renders an attribute input block, potentially with nested children.
         * @param {HTMLElement} parentContainer - The DOM element where this attribute block should be appended.
         * @param {number} level - The nesting level of this attribute (0 for top-level).
         * @param {Object} [attributeData] - Optional existing attribute data to pre-fill the form.
         */
        function renderAttributeInput(parentContainer, level, attributeData = null) {
            const attributeId = currentAttributeIdCounter++; // Unique ID for this attribute block

            const attributeRowDiv = document.createElement('div');
            attributeRowDiv.className = `attribute-input-row space-y-3 p-4 border border-gray-300 rounded-md bg-white mb-4`;
            attributeRowDiv.setAttribute('data-level', level);
            attributeRowDiv.setAttribute('data-attribute-id', attributeId); // Store unique ID

            // The remove button should ONLY be shown if level > 0 (i.e., it's a child attribute)
            const showRemoveButton = (level > 0);

            attributeRowDiv.innerHTML = `
                <div>
                    <label for="attribute-name-input-${attributeId}" class="block text-sm font-medium text-gray-700">Attribute Name:</label>
                    <input type="text" id="attribute-name-input-${attributeId}" class="attribute-name-input mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="e.g., Material Type" value="${attributeData ? attributeData.attributeName : ''}">
                </div>
                <div class="flex items-center">
                    <input type="checkbox" id="mandatory-checkbox-${attributeId}" class="mandatory-checkbox h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded" ${attributeData && attributeData.isMandatory ? 'checked' : ''}>
                    <label for="mandatory-checkbox-${attributeId}" class="ml-2 block text-sm text-gray-900">Mandatory</label>
                </div>
                <div class="standard-values-section space-y-2" id="standard-values-container-${attributeId}">
                    <!-- Standard values will be rendered here by renderStandardValueInputs -->
                </div>
                <div class="flex justify-end gap-2 mt-4">
                    <button type="button" class="add-child-attribute-btn px-3 py-2 bg-indigo-500 text-white rounded-md hover:bg-indigo-600 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 transition ease-in-out duration-150">
                        Add Child Attribute
                    </button>
                    <!-- New Clear Inputs button -->
                    <button type="button" class="clear-attribute-inputs-btn px-3 py-2 bg-yellow-500 text-white rounded-md hover:bg-yellow-600 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-offset-2 transition ease-in-out duration-150">
                        Clear Inputs
                    </button>
                    ${showRemoveButton ? `
                        <button type="button" class="remove-attribute-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">
                            Remove Child Attribute
                        </button>
                    ` : ''}
                </div>
                <div class="child-attributes-container mt-4 space-y-4">
                    <!-- Child attributes will be rendered here -->
                </div>
            `;
            parentContainer.appendChild(attributeRowDiv);

            const standardValuesSection = attributeRowDiv.querySelector(`#standard-values-container-${attributeId}`);
            const childAttributesContainer = attributeRowDiv.querySelector('.child-attributes-container');

            // Initial render of standard values or hide if children exist
            if (attributeData && attributeData.children && attributeData.children.length > 0) {
                standardValuesSection.classList.add('hidden'); // Hide the standard values section
            } else {
                renderStandardValueInputs(standardValuesSection, attributeData ? attributeData.standardValues : []);
                standardValuesSection.classList.remove('hidden'); // Ensure it's visible if no children
            }

            // Recursively render child attributes if data exists
            if (attributeData && attributeData.children && attributeData.children.length > 0) {
                attributeData.children.forEach(childAttr => {
                    renderAttributeInput(childAttributesContainer, level + 1, childAttr);
                });
            }

            // Event Listeners for the newly created attribute block (using event delegation on the block itself)
            attributeRowDiv.addEventListener('click', (event) => {
                const target = event.target;
                const valuesContainer = attributeRowDiv.querySelector(`#standard-values-container-${attributeId}`); // Correctly scope valuesContainer

                // Add Standard Value
                if (target.classList.contains('add-value-btn')) {
                    // Collect current values from the DOM
                    const currentValues = [];
                    Array.from(valuesContainer.children).forEach(row => {
                        if (row.classList.contains('flex')) { // Check if it's an input row
                            currentValues.push({
                                value: row.querySelector('.standard-value-input').value.trim(),
                                unit: row.querySelector('.standard-unit-input').value.trim()
                            });
                        }
                    });
                    currentValues.push({ value: '', unit: '' }); // Add a new empty value
                    renderStandardValueInputs(valuesContainer, currentValues);
                }
                // Remove Standard Value
                if (target.classList.contains('remove-value-btn')) {
                    const rowToRemove = target.closest('.flex.gap-2.mt-2');
                    // Collect current values from the DOM, excluding the one to be removed
                    const currentValues = [];
                    Array.from(valuesContainer.children).forEach(row => {
                        if (row.classList.contains('flex') && row !== rowToRemove) {
                            currentValues.push({
                                value: row.querySelector('.standard-value-input').value.trim(),
                                unit: row.querySelector('.standard-unit-input').value.trim()
                            });
                        }
                    });
                    renderStandardValueInputs(valuesContainer, currentValues);
                }
                // Add Child Attribute
                if (target.classList.contains('add-child-attribute-btn')) {
                    const childContainer = attributeRowDiv.querySelector('.child-attributes-container');
                    renderAttributeInput(childContainer, level + 1); // Render new child

                    // Hide parent's standard values section if a child is added
                    const parentStandardValuesSection = attributeRowDiv.querySelector('.standard-values-section');
                    if (parentStandardValuesSection) {
                        parentStandardValuesSection.classList.add('hidden');
                    }
                }
                // Remove Attribute (this attribute block)
                if (target.classList.contains('remove-attribute-btn')) {
                    const attributeBlockToRemove = target.closest('.attribute-input-row');
                    if (attributeBlockToRemove) {
                        const parentChildContainer = attributeBlockToRemove.parentNode; // This is the .child-attributes-container
                        const parentAttributeRowDiv = parentChildContainer.closest('.attribute-input-row'); // This is the parent attribute's row

                        attributeBlockToRemove.remove(); // Remove the child attribute

                        // If there's a parent attribute, check if it now has no children
                        if (parentAttributeRowDiv) {
                            const remainingChildren = parentAttributeRowDiv.querySelector('.child-attributes-container').children.length;

                            // If parent now has no children, re-enable parent's standard value inputs
                            if (remainingChildren === 0) {
                                const parentStandardValuesSection = parentAttributeRowDiv.querySelector('.standard-values-section');
                                if (parentStandardValuesSection) {
                                    parentStandardValuesSection.classList.remove('hidden');
                                    renderStandardValueInputs(parentStandardValuesSection, [{value: '', unit: ''}]); // Add one empty value input
                                }
                            }
                        }
                    } else {
                        console.error('Could not find attribute block to remove.');
                    }
                }
                // Clear Inputs for this attribute block
                if (target.classList.contains('clear-attribute-inputs-btn')) {
                    const currentAttributeBlock = target.closest('.attribute-input-row');
                    if (currentAttributeBlock) {
                        currentAttributeBlock.querySelector('.attribute-name-input').value = '';
                        currentAttributeBlock.querySelector('.mandatory-checkbox').checked = false;
                        const standardValuesSection = currentAttributeBlock.querySelector('.standard-values-section');
                        if (standardValuesSection) {
                            standardValuesSection.classList.remove('hidden'); // Ensure it's visible
                            renderStandardValueInputs(standardValuesSection, []); // Render with empty array to clear
                        }
                        // Also clear any child attributes
                        const childContainer = currentAttributeBlock.querySelector('.child-attributes-container');
                        if (childContainer) {
                            childContainer.innerHTML = '';
                        }
                    }
                }
            });
        }


        /**
         * Collects attribute data from the dynamically created input fields.
         * This function is recursive to handle child attributes.
         * @param {HTMLElement} attributeElement - The DOM element representing an attribute row.
         * @returns {Object|null} The collected attribute object or null if invalid.
         */
        function collectAttributeData(attributeElement) {
            const attributeNameInput = attributeElement.querySelector('.attribute-name-input');
            const mandatoryCheckbox = attributeElement.querySelector('.mandatory-checkbox');
            const standardValuesContainer = attributeElement.querySelector('.standard-values-section');
            const childAttributeContainers = attributeElement.querySelector('.child-attributes-container');

            const attributeName = attributeNameInput.value.trim();
            const isMandatory = mandatoryCheckbox.checked;

            if (!attributeName) {
                return null;
            }

            const children = [];
            if (childAttributeContainers) {
                Array.from(childAttributeContainers.children).forEach(childElement => {
                    const childAttr = collectAttributeData(childElement);
                    if (childAttr) {
                        children.push(childAttr);
                    }
                });
            }

            const standardValues = [];
            // Only collect standard values if there are no children for this attribute
            if (children.length === 0 && standardValuesContainer && !standardValuesContainer.classList.contains('hidden')) {
                // Iterate over direct children of standardValuesContainer
                Array.from(standardValuesContainer.children).forEach(row => {
                    // Check if the child is an input row (has the 'flex' class)
                    if (row.classList.contains('flex')) {
                        const valueInput = row.querySelector('.standard-value-input');
                        const unitInput = row.querySelector('.standard-unit-input');
                        if (valueInput && unitInput) { // Ensure elements exist
                            const value = valueInput.value.trim();
                            const unit = unitInput.value.trim();
                            if (value) { // Only add if value is not empty
                                standardValues.push({ value, unit });
                            }
                        }
                    }
                });
            }

            return {
                attributeName: attributeName,
                standardValues: standardValues,
                isMandatory: isMandatory,
                children: children // Will be empty array if no children
            };
        }


        /**
         * Saves all attributes currently displayed in the input section.
         */
        function saveAllAttributes() {
            const currentDivisionName = divisionSelect.options[divisionSelect.selectedIndex].text;
            const currentSectionName = sectionSelect.options[sectionSelect.selectedIndex].text;
            const currentSubsectionName = subsectionSelect.options[subsectionSelect.selectedIndex].text; // CSI Section (Detailed)
            const currentItemName = itemSelect.options[itemSelect.selectedIndex].text; // NEW: CSI Item (Detailed)

            // Validate that a full hierarchy is selected/entered
            if (!currentDivisionName || currentDivisionName === 'Select Division' ||
                !currentSectionName || currentSectionName.includes('Select Section') ||
                !currentSubsectionName || currentSubsectionName.includes('Select Detailed Section') ||
                !currentItemName || currentItemName.includes('Select Item')) { // NEW validation
                showMessage("Please select a complete CSI Division, Section, Detailed Section, and Item before saving attributes.");
                return;
            }

            const attributesFromForm = [];
            Array.from(attributesContainer.children).forEach(attributeElement => {
                const attribute = collectAttributeData(attributeElement);
                if (attribute) {
                    // Add the hierarchy information to the top-level attributes
                    attribute.division = currentDivisionName;
                    attribute.section = currentSectionName;
                    attribute.subsection = currentSubsectionName;
                    attribute.item = currentItemName; // NEW
                    attribute.level = 0; // Ensure top-level attributes have level 0
                    attributesFromForm.push(attribute);
                }
            });

            if (attributesFromForm.length === 0) {
                showMessage("No valid attributes to save. Please enter at least one attribute name.");
                return;
            }

            // Instead of filtering and updating, simply add new entries
            attributesFromForm.forEach(newAttr => {
                collectedData.push(newAttr); // Add as a new entry
            });

            renderCollectedData(); // Re-render the table
            showMessage("All attributes saved successfully!");
            // Optionally, clear the input section after saving
            attributesContainer.innerHTML = '';
            renderAttributeInput(attributesContainer, 0); // Add a new blank top-level attribute
        }


        /**
         * Renders collected data in the table, handling nested attributes.
         * @param {Object} attribute - The attribute object to render (can be top-level or child).
         * @param {HTMLElement} parentBody - The tbody or parent element to append rows to.
         * @param {Object} parentHierarchy - The division, section, subsection, item info inherited from parent.
         * @param {number} level - The current nesting level for display indentation.
         * @param {number[]} path - Array representing the path (indices) to the current attribute for deletion.
         */
        function renderCollectedAttributeRow(attribute, parentBody, parentHierarchy, level, path) {
            const row = document.createElement('tr');
            row.className = 'collected-attribute-row border-b border-gray-100';
            row.setAttribute('data-level', level);

            // Format standard values for display
            const standardValuesDisplay = attribute.standardValues.map(sv => {
                return `${sv.value}${sv.unit ? ` (${sv.unit})` : ''}`;
            }).join(', ');

            // Only show hierarchy for top-level attributes
            const displayDivision = level === 0 ? parentHierarchy.division : '';
            const displaySection = level === 0 ? parentHierarchy.section : '';
            const displaySubsection = level === 0 ? parentHierarchy.subsection : '';
            const displayItem = level === 0 ? parentHierarchy.item : ''; // NEW

            row.innerHTML = `
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${displayDivision}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${displaySection}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${displaySubsection}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${displayItem}</td> <!-- NEW COLUMN -->
                <td class="px-6 py-4 text-sm text-gray-900" style="padding-left: ${1.5 + level * 1.5}rem;">${attribute.attributeName}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${standardValuesDisplay}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${attribute.isMandatory ? 'Yes' : 'No'}</td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <button type="button" class="delete-attribute-btn text-red-600 hover:text-red-900 ml-4" data-path="${JSON.stringify(path)}">Delete</button>
                </td>
            `;
            parentBody.appendChild(row);

            // Recursively render children
            if (attribute.children && attribute.children.length > 0) {
                attribute.children.forEach((childAttr, childIndex) => {
                    renderCollectedAttributeRow(childAttr, parentBody, parentHierarchy, level + 1, [...path, childIndex]);
                });
            }
        }

        // Main function to render all collected data
        function renderCollectedData() {
            collectedDataBody.innerHTML = ''; // Clear existing table rows
            if (collectedData.length === 0) {
                collectedDataSection.classList.add('hidden');
                return;
            } else {
                collectedDataSection.classList.remove('hidden');
            }

            collectedData.forEach((item, index) => {
                const hierarchyInfo = {
                    division: item.division,
                    section: item.section,
                    subsection: item.subsection,
                    item: item.item // NEW
                };
                renderCollectedAttributeRow(item, collectedDataBody, hierarchyInfo, item.level || 0, [index]);
            });
        }

        /**
         * Deletes an attribute based on its path in the collectedData array.
         * @param {number[]} path - An array of indices representing the path to the attribute (e.g., [0, 1] for the second child of the first top-level attribute).
         */
        function deleteAttribute(path) {
            let currentLevel = collectedData;
            let targetAttribute = null;
            let parentArray = null;
            let indexInParent = -1;

            for (let i = 0; i < path.length; i++) {
                const index = path[i];
                if (!currentLevel || index === undefined || index < 0 || index >= currentLevel.length) {
                    console.error("Invalid path for deletion:", path);
                    showMessage("Error: Could not find attribute to delete.");
                    return;
                }

                if (i === path.length - 1) { // Last element in path is the target
                    targetAttribute = currentLevel[index];
                    parentArray = currentLevel;
                    indexInParent = index;
                } else {
                    currentLevel = currentLevel[index].children;
                }
            }

            if (parentArray && indexInParent !== -1) {
                parentArray.splice(indexInParent, 1);
                renderCollectedData();
                showMessage("Attribute deleted successfully!");
            } else {
                showMessage("Error: Could not find attribute to delete.");
            }
        }

        /**
         * Recursively flattens attributes for Excel export, including full hierarchy and parent details.
         * @param {Array} attributes - The array of attributes (current level) to process.
         * @param {Object} hierarchyInfo - Object containing full division, section, subsection, item names.
         * @param {Object|null} parentAttributeDetails - Object with parent's name and standard values, or null if top-level.
         * @param {number} level - The current nesting level.
         * @param {Array<Array>} dataToExport - The array to push flattened rows into.
         */
        function flattenAttributesForExport(attributes, hierarchyInfo, parentAttributeDetails, level, dataToExport) {
            attributes.forEach(attr => {
                const currentStandardValues = attr.standardValues.map(sv => `${sv.value}${sv.unit ? ` (${sv.unit})` : ''}`).join(', ');
                const parentAttributeName = parentAttributeDetails ? parentAttributeDetails.attributeName : '';
                const parentStandardValues = parentAttributeDetails ? parentAttributeDetails.standardValues.map(sv => `${sv.value}${sv.unit ? ` (${sv.unit})` : ''}`).join(', ') : '';

                const rowData = [
                    hierarchyInfo.division,
                    hierarchyInfo.section,
                    hierarchyInfo.subsection,
                    hierarchyInfo.item, // NEW
                    parentAttributeName,
                    parentStandardValues,
                    level, // Attribute Level
                    attr.attributeName,
                    currentStandardValues,
                    attr.isMandatory ? 'Yes' : 'No'
                ];
                dataToExport.push(rowData);

                if (attr.children && attr.children.length > 0) {
                    // For children, the current attribute becomes the parent
                    flattenAttributesForExport(attr.children, hierarchyInfo, {
                        attributeName: attr.attributeName,
                        standardValues: attr.standardValues // Pass full standard values for parent
                    }, level + 1, dataToExport);
                }
            });
        }

        // --- Helper Functions for CSI Code Parsing ---

        /**
         * Extracts the 2-digit division code from a full division name string.
         * E.g., "Division 00 - Procurement..." -> "00"
         * @param {string} divisionName The full division name.
         * @returns {string} The 2-digit division code, or empty string if not found.
         */
        function getDivisionCode(divisionName) {
            const match = divisionName.match(/Division (\d{2})/);
            return match ? match[1] : '';
        }

        /**
         * Extracts the 6-digit section code from a full section name string.
         * E.g., "Section 00 10 00 - Solicitations" -> "00 10 00"
         * @param {string} sectionName The full section name.
         * @returns {string} The 6-digit section code, or empty string if not found.
         */
        function getFullSectionCode(sectionName) {
            const match = sectionName.match(/Section (\d{2} \d{2} \d{2})/);
            return match ? match[1] : '';
        }

        /**
         * Extracts the middle two digits (section number) from a 6-digit CSI section code (XX YY ZZ).
         * E.g., "00 10 00" -> "10"
         * @param {string} sectionCode The 6-digit section code (e.g., "00 10 00").
         * @returns {string} The 2-digit section number (YY part), or empty string if not found.
         */
        function getSectionNumberFromFullCode(sectionCode) {
            const parts = sectionCode.split(' ');
            return parts.length === 3 ? parts[1] : '';
        }

        /**
         * Constructs a full 6-digit CSI section code from a division code and a 2-digit section number.
         * E.g., "00", "10" -> "00 10 00"
         * @param {string} divCode The 2-digit division code (XX part).
         * @param {string} sectionNum The 2-digit section number (YY part).
         * @returns {string} The constructed 6-digit section code (XX YY 00).
         */
        function constructFullSectionCode(divCode, sectionNum) {
            return `${divCode} ${sectionNum} 00`;
        }

        /**
         * Extracts the full 6-digit detailed section code from a full detailed section name string.
         * E.g., "Subsection 01 31 19 - Project Meetings" -> "01 31 19"
         * @param {string} detailedSectionName The full detailed section name.
         * @returns {string} The 6-digit detailed section code, or empty string if not found.
         */
        function getFullDetailedSectionCode(detailedSectionName) {
            const match = detailedSectionName.match(/Subsection (\d{2} \d{2} \d{2})/);
            return match ? match[1] : '';
        }

        /**
         * Extracts the two digits (ZZ part) from a 6-digit CSI detailed section code (XX YY ZZ).
         * E.g., "01 31 19" -> "19"
         * @param {string} detailedSectionCode The 6-digit detailed section code (e.g., "01 31 19").
         * @returns {string} The 2-digit number (ZZ part), or empty string if not found.
         */
        function getDetailedSectionNumberFromFullCode(detailedSectionCode) {
            const parts = detailedSectionCode.split(' ');
            return parts.length === 3 ? parts[2] : ''; // Return the ZZ part for the detailed section
        }

        /**
         * Constructs a full 6-digit CSI detailed section code from a division code, section number, and detailed section number.
         * E.g., "01", "31", "19" -> "01 31 19"
         * @param {string} divCode The 2-digit division code (XX part).
         * @param {string} sectionNum The 2-digit section number (YY part of parent section).
         * @param {string} detailedSectionNum The 2-digit detailed section number (ZZ part).
         * @returns {string} The constructed 6-digit detailed section code (XX YY ZZ).
         */
        function constructFullDetailedSectionCode(divCode, sectionNum, detailedSectionNum) {
            return `${divCode} ${sectionNum} ${detailedSectionNum}`;
        }

        /**
         * Extracts the full ZZ.AA item code from a full item name string.
         * E.g., "Item 01 31 19.13 - Preconstruction Meetings" -> "19.13"
         * @param {string} itemName The full item name.
         * @returns {string} The ZZ.AA item code, or empty string if not found.
         */
        function getItemCodeFromFullItemName(itemName) {
            const match = itemName.match(/Item \d{2} \d{2} (\d{2}\.\d{2}) - (.*)/);
            return match ? match[1] : ''; // Returns "19.13" from "Item 01 31 19.13 - ..."
        }

        /**
         * Constructs a full 8-character CSI item code (XX YY ZZ.AA) from hierarchy codes and the ZZ.AA item part.
         * E.g., "01", "31", "19.13" -> "01 31 19.13"
         * @param {string} divCode The 2-digit division code (XX part).
         * @param {string} sectionNum The 2-digit section number (YY part).
         * @param {string} itemCodePart The ZZ.AA part of the item code (e.g., "19.13").
         * @returns {string} The constructed 8-character item code (XX YY ZZ.AA).
         */
        function constructFullItemCode(divCode, sectionNum, itemCodePart) {
            return `${divCode} ${sectionNum} ${itemCodePart}`;
        }


        /**
         * Downloads the current HTML file with the updated CSI data embedded.
         */
        function downloadUpdatedHtmlFile() {
            // Get the current HTML content of the page
            let htmlContent = document.documentElement.outerHTML;

            // Convert the current csiData object to a JSON string
            // Use JSON.stringify with null, 4 for pretty printing and readability
            const updatedCsiDataString = JSON.stringify(csiData, null, 4);

            // Find the script block containing the csiData definition
            // This regex looks for: let csiData = { ... };
            // It captures everything between `let csiData = ` and the closing `};` and the semicolon.
            // The `[\s\S]*?` matches any character (including newlines) non-greedily.
            const regex = /(let\s+csiData\s*=\s*)([\s\S]*?)(;(?=\s*<\/script>))/;

            // Replace the old csiData definition with the new one
            htmlContent = htmlContent.replace(regex, (match, p1, p2, p3) => {
                // p1 is "let csiData = "
                // p2 is the old JSON data
                // p3 is the closing ";"
                return `${p1}${updatedCsiDataString}${p3}`;
            });

            // Create a Blob from the updated HTML content
            const blob = new Blob([htmlContent], { type: 'text/html' });

            // Create a temporary download link
            const a = document.createElement('a');
            a.href = URL.createObjectURL(blob);
            a.download = 'CSI_Attribute_Portal_Updated.html'; // Name of the downloaded file

            // Programmatically click the link to trigger the download
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);

            showMessage("File downloaded successfully with updated data!");
        }


        // --- Modal Functions for Modifying Sections ---

        /**
         * Renders the editable list of sections in the modal.
         * @param {string} divisionName - The name of the currently selected division.
         */
        function renderSectionsInModal(divisionName) {
            modalSectionsList.innerHTML = '';
            const sections = csiData[divisionName] ? Object.keys(csiData[divisionName]) : [];
            const divCode = getDivisionCode(divisionName);

            sections.forEach((sectionName) => {
                const sectionParts = sectionName.match(/Section (\d{2} \d{2} \d{2}) - (.*)/);
                const fullSectionCode = sectionParts ? sectionParts[1] : ''; // e.g., "00 10 00"
                const sectionNum = getSectionNumberFromFullCode(fullSectionCode); // e.g., "10"
                const sectionDesc = sectionParts ? sectionParts[2] : sectionName; // Fallback to full name

                const sectionDiv = document.createElement('div');
                sectionDiv.className = 'flex gap-2 items-center mb-2';
                sectionDiv.innerHTML = `
                    <input type="text" class="modal-section-num w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${sectionNum}" maxlength="2" pattern="\d{2}" title="Enter 2 digits for the section number">
                    <input type="text" class="modal-section-desc flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${sectionDesc}">
                    <button type="button" class="remove-modal-section-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                modalSectionsList.appendChild(sectionDiv);
            });
        }

        /**
         * Opens the modify sections modal.
         */
        function openModifySectionsModal() {
            // Check role before opening
            if (currentUserRole !== 'developer') {
                showMessage("You do not have permission to modify sections.");
                return;
            }

            const selectedDivision = divisionSelect.value;
            if (!selectedDivision) {
                showMessage("Please select a valid CSI Division to modify its sections.");
                return;
            }
            modalDivisionNameSpan.textContent = selectedDivision;
            modalDivisionCodeInput.value = getDivisionCode(selectedDivision); // Set non-modifiable division code
            renderSectionsInModal(selectedDivision);
            modifySectionsModal.classList.remove('hidden');
        }

        /**
         * Closes the modify sections modal.
         */
        function closeModifySectionsModal() {
            modifySectionsModal.classList.add('hidden');
            newSectionNumInput.value = '';
            newSectionDescInput.value = '';
        }

        /**
         * Adds a new section input field to the modal.
         */
        addNewSectionBtn.addEventListener('click', () => {
            const newNum = newSectionNumInput.value.trim();
            const newDesc = newSectionDescInput.value.trim();
            const divCode = modalDivisionCodeInput.value; // Get the division code from the non-modifiable field

            if (!newNum || !newDesc || !/^\d{2}$/.test(newNum)) {
                showMessage("Please enter a valid 2-digit number and description for the new section.");
                return;
            }

            const fullNewSectionCode = constructFullSectionCode(divCode, newNum);
            const formattedSectionName = `Section ${fullNewSectionCode} - ${newDesc}`;

            // Check for duplicates before adding
            let duplicateFound = false;
            Array.from(modalSectionsList.children).forEach(sectionDiv => {
                const existingNum = sectionDiv.querySelector('.modal-section-num').value.trim();
                const existingDesc = sectionDiv.querySelector('.modal-section-desc').value.trim();
                if (existingNum === newNum && existingDesc === newDesc) {
                    duplicateFound = true;
                }
            });

            if (duplicateFound) {
                showMessage("A section with this number and description already exists in the list.");
                return;
            }

            const sectionDiv = document.createElement('div');
            sectionDiv.className = 'flex gap-2 items-center mb-2';
            sectionDiv.innerHTML = `
                <input type="text" class="modal-section-num w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${newNum}" maxlength="2" pattern="\d{2}" title="Enter 2 digits for the section number">
                <input type="text" class="modal-section-desc flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${newDesc}">
                <button type="button" class="remove-modal-section-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
            `;
            modalSectionsList.appendChild(sectionDiv);

            newSectionNumInput.value = '';
            newSectionDescInput.value = '';
        });

        /**
         * Saves the modified sections from the modal to csiData and triggers file download.
         */
        saveSectionsModalBtn.addEventListener('click', () => {
            const currentDivision = modalDivisionNameSpan.textContent;
            const divCode = modalDivisionCodeInput.value;
            const updatedSections = {};

            Array.from(modalSectionsList.children).forEach(sectionDiv => {
                const sectionNumInput = sectionDiv.querySelector('.modal-section-num');
                const sectionDescInput = sectionDiv.querySelector('.modal-section-desc');

                const num = sectionNumInput.value.trim();
                const desc = sectionDescInput.value.trim();

                if (num && desc && /^\d{2}$/.test(num)) {
                    const fullSectionCode = constructFullSectionCode(divCode, num);
                    const formattedSectionName = `Section ${fullSectionCode} - ${desc}`;

                    // Preserve existing detailed sections (subsections) if the section name (full code + description) exists
                    // Otherwise, create an empty object for new sections.
                    updatedSections[formattedSectionName] = csiData[currentDivision] && csiData[currentDivision][formattedSectionName]
                        ? csiData[currentDivision][formattedSectionName]
                        : {}; // Changed to empty object for new level
                }
            });

            // Update the csiData object for the current division
            csiData[currentDivision] = updatedSections;
            downloadUpdatedHtmlFile(); // Trigger download of the updated HTML file

            // Re-populate the section dropdown to reflect changes
            populateDropdown(sectionSelect, Object.keys(csiData[currentDivision]));
            sectionSelect.disabled = false;

            closeModifySectionsModal();
        });

        // Event listener for removing sections within the modal
        modalSectionsList.addEventListener('click', (event) => {
            if (event.target.classList.contains('remove-modal-section-btn')) {
                event.target.closest('.flex.gap-2.items-center.mb-2').remove();
            }
        });


        // --- Modal Functions for Modifying Detailed Sections (formerly Subsections) ---

        /**
         * Renders the editable list of detailed sections in the modal.
         * @param {string} divisionName - The name of the currently selected division.
         * @param {string} sectionName - The name of the currently selected section.
         */
        function renderDetailedSectionsInModal(divisionName, sectionName) {
            modalSubsectionsList.innerHTML = '';
            const detailedSections = csiData[divisionName] && csiData[divisionName][sectionName]
                               ? Object.keys(csiData[divisionName][sectionName])
                               : [];

            const divCode = getDivisionCode(divisionName);
            const sectionNum = getSectionNumberFromFullCode(getFullSectionCode(sectionName));

            detailedSections.forEach((detailedSectionName) => {
                const detailedSectionParts = detailedSectionName.match(/Subsection (\d{2} \d{2} \d{2}) - (.*)/);
                const fullDetailedSectionCode = detailedSectionParts ? detailedSectionParts[1] : '';
                const detailedSectionNum = getDetailedSectionNumberFromFullCode(fullDetailedSectionCode);
                const detailedSectionDesc = detailedSectionParts ? detailedSectionParts[2] : detailedSectionName;

                const detailedSectionDiv = document.createElement('div');
                detailedSectionDiv.className = 'flex gap-2 items-center mb-2';
                detailedSectionDiv.innerHTML = `
                    <input type="text" class="modal-subsection-num w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${detailedSectionNum}" maxlength="2" pattern="\d{2}" title="Enter 2 digits for the detailed section number">
                    <input type="text" class="modal-subsection-desc flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${detailedSectionDesc}">
                    <button type="button" class="remove-modal-subsection-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                modalSubsectionsList.appendChild(detailedSectionDiv);
            });
        }

        /**
         * Opens the modify detailed sections modal.
         */
        function openModifySubsectionsModal() {
            // Check role before opening
            if (currentUserRole !== 'developer') {
                showMessage("You do not have permission to modify detailed sections.");
                return;
            }

            const selectedDivision = divisionSelect.value;
            const selectedSection = sectionSelect.value;

            if (!selectedDivision || !selectedSection) {
                showMessage("Please select a valid CSI Division and Section to modify its detailed sections.");
                return;
            }

            modalSubsectionDivisionNameSpan.textContent = selectedDivision;
            modalSubsectionSectionNameSpan.textContent = selectedSection;
            modalSubsectionDivisionCodeInput.value = getDivisionCode(selectedDivision);
            modalSubsectionSectionCodeInput.value = getSectionNumberFromFullCode(getFullSectionCode(selectedSection)); // This is the parent section's YY part

            renderDetailedSectionsInModal(selectedDivision, selectedSection);
            modifySubsectionsModal.classList.remove('hidden');
        }

        /**
         * Closes the modify detailed sections modal.
         */
        function closeModifySubsectionsModal() {
            modifySubsectionsModal.classList.add('hidden');
            newSubsectionNumInput.value = '';
            newSubsectionDescInput.value = '';
        }

        /**
         * Adds a new detailed section input field to the modal.
         */
        addNewSubsectionBtn.addEventListener('click', () => {
            const newNum = newSubsectionNumInput.value.trim(); // This is the ZZ part
            const newDesc = newSubsectionDescInput.value.trim();
            const divCode = modalSubsectionDivisionCodeInput.value; // Get the XX part from the division code
            const sectionNum = modalSubsectionSectionCodeInput.value; // Get the YY part from the section code

            if (!newNum || !newDesc || !/^\d{2}$/.test(newNum)) {
                showMessage("Please enter a valid 2-digit number and description for the new detailed section.");
                return;
            }

            // Construct the full 6-digit code for the new detailed section: XX YY ZZ
            const fullNewDetailedSectionCode = constructFullDetailedSectionCode(divCode, sectionNum, newNum);
            const formattedDetailedSectionName = `Subsection ${fullNewDetailedSectionCode} - ${newDesc}`;

            // Check for duplicates before adding
            let duplicateFound = false;
            Array.from(modalSubsectionsList.children).forEach(detailedSectionDiv => {
                const existingNum = detailedSectionDiv.querySelector('.modal-subsection-num').value.trim();
                const existingDesc = detailedSectionDiv.querySelector('.modal-subsection-desc').value.trim();
                if (existingNum === newNum && existingDesc === newDesc) {
                    duplicateFound = true;
                }
            });

            if (duplicateFound) {
                showMessage("A detailed section with this number and description already exists in the list.");
                return;
            }

            const detailedSectionDiv = document.createElement('div');
            detailedSectionDiv.className = 'flex gap-2 items-center mb-2';
            detailedSectionDiv.innerHTML = `
                <input type="text" class="modal-subsection-num w-28 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${newNum}" maxlength="2" pattern="\d{2}" title="Enter 2 digits for the detailed section number">
                <input type="text" class="modal-subsection-desc flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${newDesc}">
                <button type="button" class="remove-modal-subsection-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
            `;
            modalSubsectionsList.appendChild(detailedSectionDiv);

            newSubsectionNumInput.value = '';
            newSubsectionDescInput.value = '';
        });

        /**
         * Saves the modified detailed sections from the modal to csiData and triggers file download.
         */
        saveSubsectionsModalBtn.addEventListener('click', () => {
            const currentDivision = modalSubsectionDivisionNameSpan.textContent;
            const currentSection = modalSubsectionSectionNameSpan.textContent;
            const divCode = modalSubsectionDivisionCodeInput.value;
            const sectionNum = modalSubsectionSectionCodeInput.value;
            const updatedDetailedSections = {}; // Changed to object for next level

            Array.from(modalSubsectionsList.children).forEach(detailedSectionDiv => {
                const detailedSectionNumInput = detailedSectionDiv.querySelector('.modal-subsection-num');
                const detailedSectionDescInput = detailedSectionDiv.querySelector('.modal-subsection-desc');

                const num = detailedSectionNumInput.value.trim();
                const desc = detailedSectionDescInput.value.trim();

                if (num && desc && /^\d{2}$/.test(num)) {
                    const fullDetailedSectionCode = constructFullDetailedSectionCode(divCode, sectionNum, num);
                    const formattedDetailedSectionName = `Subsection ${fullDetailedSectionCode} - ${desc}`;

                    // Preserve existing items if the detailed section name exists, otherwise create empty array
                    updatedDetailedSections[formattedDetailedSectionName] = csiData[currentDivision] &&
                                                                        csiData[currentDivision][currentSection] &&
                                                                        csiData[currentDivision][currentSection][formattedDetailedSectionName]
                        ? csiData[currentDivision][currentSection][formattedDetailedSectionName]
                        : []; // Changed to empty array for new level
                }
            });

            // Update the csiData object for the current section
            if (csiData[currentDivision] && csiData[currentDivision][currentSection]) {
                csiData[currentDivision][currentSection] = updatedDetailedSections;
            }
            downloadUpdatedHtmlFile(); // Trigger download of the updated HTML file

            // Re-populate the detailed section dropdown to reflect changes
            populateDropdown(subsectionSelect, Object.keys(csiData[currentDivision][currentSection]));
            subsectionSelect.disabled = false;

            closeModifySubsectionsModal();
        });

        // Event listener for removing detailed sections within the modal
        modalSubsectionsList.addEventListener('click', (event) => {
            if (event.target.classList.contains('remove-modal-subsection-btn')) {
                event.target.closest('.flex.gap-2.items-center.mb-2').remove();
            }
        });


        // --- Modal Functions for Modifying Items (NEW) ---

        /**
         * Renders the editable list of items in the modal.
         * @param {string} divisionName - The name of the currently selected division.
         * @param {string} sectionName - The name of the currently selected section.
         * @param {string} detailedSectionName - The name of the currently selected detailed section.
         */
        function renderItemsInModal(divisionName, sectionName, detailedSectionName) {
            modalItemsList.innerHTML = '';
            const items = csiData[divisionName] && csiData[divisionName][sectionName] && csiData[divisionName][sectionName][detailedSectionName]
                               ? csiData[divisionName][sectionName][detailedSectionName]
                               : [];

            const divCode = getDivisionCode(divisionName);
            const sectionNum = getSectionNumberFromFullCode(getFullSectionCode(sectionName));
            // No need for detailedSectionNum separately here, as itemNum will contain ZZ.AA

            items.forEach((itemName) => {
                const itemParts = itemName.match(/Item (\d{2} \d{2} (\d{2}\.\d{2})) - (.*)/); // Capture ZZ.AA
                const fullItemCode = itemParts ? itemParts[2] : ''; // This will be "19.13"
                const itemDesc = itemParts ? itemParts[3] : itemName;

                const itemDiv = document.createElement('div');
                itemDiv.className = 'flex gap-2 items-center mb-2';
                itemDiv.innerHTML = `
                    <input type="text" class="modal-item-num w-36 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${fullItemCode}" maxlength="5" pattern="\d{2}\.\d{2}" title="Enter 2 digits, a dot, and 2 digits (e.g., 19.13)">
                    <input type="text" class="modal-item-desc flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${itemDesc}">
                    <button type="button" class="remove-modal-item-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                modalItemsList.appendChild(itemDiv);
            });
        }

        /**
         * Opens the modify items modal.
         */
        function openModifyItemsModal() {
            // Check role before opening
            if (currentUserRole !== 'developer') {
                showMessage("You do not have permission to modify items.");
                return;
            }

            const selectedDivision = divisionSelect.value;
            const selectedSection = sectionSelect.value;
            const selectedSubsection = subsectionSelect.value; // CSI Section (Detailed)

            if (!selectedDivision || !selectedSection || !selectedSubsection) {
                showMessage("Please select a valid CSI Division, Section, and Detailed Section to modify its items.");
                return;
            }

            modalItemDivisionNameSpan.textContent = selectedDivision;
            modalItemSectionNameSpan.textContent = selectedSection;
            modalItemSubsectionNameSpan.textContent = selectedSubsection;
            modalItemDivisionCodeInput.value = getDivisionCode(selectedDivision);
            modalItemSectionCodeInput.value = getSectionNumberFromFullCode(getFullSectionCode(selectedSection));
            modalItemSubsectionCodeInput.value = getDetailedSectionNumberFromFullCode(getFullDetailedSectionCode(selectedSubsection)); // ZZ part

            renderItemsInModal(selectedDivision, selectedSection, selectedSubsection);
            modifyItemsModal.classList.remove('hidden');
        }

        /**
         * Closes the modify items modal.
         */
        function closeModifyItemsModal() {
            modifyItemsModal.classList.add('hidden');
            newItemNumInput.value = '';
            newItemDescInput.value = '';
        }

        /**
         * Adds a new item input field to the modal.
         */
        addNewItemBtn.addEventListener('click', () => {
            const newItemCodePart = newItemNumInput.value.trim(); // This is the ZZ.AA part
            const newItemDesc = newItemDescInput.value.trim();
            const divCode = modalItemDivisionCodeInput.value;
            const sectionNum = modalItemSectionCodeInput.value;
            // detailedSectionNum is implicitly part of newItemCodePart (the ZZ part)

            if (!newItemCodePart || !newItemDesc || !/^\d{2}\.\d{2}$/.test(newItemCodePart)) {
                showMessage("Please enter a valid item code in XX.YY format and a description for the new item.");
                return;
            }

            // Construct the full 8-character code for the new item: XX YY ZZ.AA
            const fullNewItemCode = constructFullItemCode(divCode, sectionNum, newItemCodePart);
            const formattedItemName = `Item ${fullNewItemCode} - ${newItemDesc}`;

            // Check for duplicates before adding
            let duplicateFound = false;
            Array.from(modalItemsList.children).forEach(itemDiv => {
                const existingCode = itemDiv.querySelector('.modal-item-num').value.trim();
                const existingDesc = itemDiv.querySelector('.modal-item-desc').value.trim();
                if (existingCode === newItemCodePart && existingDesc === newItemDesc) {
                    duplicateFound = true;
                }
            });

            if (duplicateFound) {
                showMessage("An item with this code and description already exists in the list.");
                return;
            }

            const itemDiv = document.createElement('div');
            itemDiv.className = 'flex gap-2 items-center mb-2';
            itemDiv.innerHTML = `
                <input type="text" class="modal-item-num w-36 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${newItemCodePart}" maxlength="5" pattern="\d{2}\.\d{2}" title="Enter 2 digits, a dot, and 2 digits (e.g., 19.13)">
                <input type="text" class="modal-item-desc flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm text-gray-700" value="${newItemDesc}">
                <button type="button" class="remove-modal-item-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
            `;
            modalItemsList.appendChild(itemDiv);

            newItemNumInput.value = '';
            newItemDescInput.value = '';
        });

        /**
         * Saves the modified items from the modal to csiData and triggers file download.
         */
        saveItemsModalBtn.addEventListener('click', () => {
            const currentDivision = modalItemDivisionNameSpan.textContent;
            const currentSection = modalItemSectionNameSpan.textContent;
            const currentSubsection = modalItemSubsectionNameSpan.textContent;
            const divCode = modalItemDivisionCodeInput.value;
            const sectionNum = modalItemSectionCodeInput.value;
            const updatedItems = [];

            Array.from(modalItemsList.children).forEach(itemDiv => {
                const itemCodePartInput = itemDiv.querySelector('.modal-item-num');
                const itemDescInput = itemDiv.querySelector('.modal-item-desc');

                const codePart = itemCodePartInput.value.trim(); // This is ZZ.AA
                const desc = itemDescInput.value.trim();

                if (codePart && desc && /^\d{2}\.\d{2}$/.test(codePart)) {
                    const fullItemCode = constructFullItemCode(divCode, sectionNum, codePart);
                    const formattedItemName = `Item ${fullItemCode} - ${desc}`;
                    updatedItems.push(formattedItemName);
                }
            });

            // Update the csiData object for the current detailed section
            if (csiData[currentDivision] && csiData[currentDivision][currentSection] && csiData[currentDivision][currentSection][currentSubsection]) {
                csiData[currentDivision][currentSection][currentSubsection] = updatedItems;
            }
            downloadUpdatedHtmlFile(); // Trigger download of the updated HTML file

            // Re-populate the item dropdown to reflect changes
            populateDropdown(itemSelect, csiData[currentDivision][currentSection][currentSubsection]);
            itemSelect.disabled = false;

            closeModifyItemsModal();
        });

        // Event listener for removing items within the modal
        modalItemsList.addEventListener('click', (event) => {
            if (event.target.classList.contains('remove-modal-item-btn')) {
                event.target.closest('.flex.gap-2.items-center.mb-2').remove();
            }
        });


        // --- Main Application Initialization and Event Listeners ---

        /**
         * Initializes the main CSI application view after successful login.
         * Applies role-based access control to modification buttons.
         */
        function initializeCSIApp() {
            // Hide login, show main app
            loginPage.classList.add('hidden');
            mainApp.classList.remove('hidden');

            // collectedData is session-based and cleared on refresh.
            collectedData = [];
            // Populate Division dropdown using the csiData directly embedded in the HTML
            populateDropdown(divisionSelect, Object.keys(csiData));

            // Apply role-based access control
            // modify buttons are initially disabled in HTML
            if (currentUserRole === 'editor') {
                modifySectionsBtn.disabled = true;
                modifySectionsBtn.classList.add('opacity-50', 'cursor-not-allowed');
                modifySubsectionsBtn.disabled = true;
                modifySubsectionsBtn.classList.add('opacity-50', 'cursor-not-allowed');
                modifyItemsBtn.disabled = true; // NEW
                modifyItemsBtn.classList.add('opacity-50', 'cursor-not-allowed'); // NEW
            } else { // developer role
                // Developers will have these enabled based on selection, so remove disabled class initially
                modifySectionsBtn.classList.remove('opacity-50', 'cursor-not-allowed');
                modifySubsectionsBtn.classList.remove('opacity-50', 'cursor-not-allowed');
                modifyItemsBtn.classList.remove('opacity-50', 'cursor-not-allowed'); // NEW
            }


            // Event listener for Division selection change
            divisionSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                sectionSelect.value = '';
                subsectionSelect.value = '';
                itemSelect.value = ''; // NEW
                sectionSelect.disabled = true;
                subsectionSelect.disabled = true;
                itemSelect.disabled = true; // NEW
                // Only enable modify sections button if user is developer AND a valid division is selected
                modifySectionsBtn.disabled = !(currentUserRole === 'developer' && selectedDivision && selectedDivision !== '');
                modifySubsectionsBtn.disabled = true;
                modifyItemsBtn.disabled = true; // NEW
                attributeInputSection.classList.add('hidden');
                currentItemNameSpan.textContent = ''; // Updated
                attributesContainer.innerHTML = ''; // Clear attribute inputs

                if (selectedDivision) {
                    populateDropdown(sectionSelect, Object.keys(csiData[selectedDivision]));
                    sectionSelect.disabled = false;
                }
                renderCollectedData();
            });

            // Event listener for Section selection change
            sectionSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                const selectedSection = sectionSelect.value;
                subsectionSelect.value = '';
                itemSelect.value = ''; // NEW
                subsectionSelect.disabled = true;
                itemSelect.disabled = true; // NEW
                // Only enable modify detailed sections button if user is developer AND a valid section is selected
                modifySubsectionsBtn.disabled = !(currentUserRole === 'developer' && selectedSection && selectedSection !== '');
                modifyItemsBtn.disabled = true; // NEW

                attributeInputSection.classList.add('hidden');
                currentItemNameSpan.textContent = ''; // Updated
                attributesContainer.innerHTML = '';

                if (selectedSection && csiData[selectedDivision] && csiData[selectedDivision][selectedSection]) {
                    populateDropdown(subsectionSelect, Object.keys(csiData[selectedDivision][selectedSection])); // Changed to Object.keys
                    subsectionSelect.disabled = false;
                }

                attributesContainer.innerHTML = '';
                currentAttributeIdCounter = 0;
                renderAttributeInput(attributesContainer, 0);
                renderCollectedData();
            });

            // Event listener for CSI Section (Detailed) selection change
            subsectionSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                const selectedSection = sectionSelect.value;
                const selectedSubsection = subsectionSelect.value; // CSI Section (Detailed)
                itemSelect.value = ''; // NEW
                itemSelect.disabled = true; // NEW
                // Only enable modify items button if user is developer AND a valid detailed section is selected
                modifyItemsBtn.disabled = !(currentUserRole === 'developer' && selectedSubsection && selectedSubsection !== ''); // NEW

                attributeInputSection.classList.add('hidden');
                currentItemNameSpan.textContent = ''; // Updated
                attributesContainer.innerHTML = '';

                if (selectedSubsection && csiData[selectedDivision] && csiData[selectedDivision][selectedSection] && csiData[selectedDivision][selectedSection][selectedSubsection]) {
                    populateDropdown(itemSelect, csiData[selectedDivision][selectedSection][selectedSubsection]); // NEW
                    itemSelect.disabled = false; // NEW
                }

                attributesContainer.innerHTML = '';
                currentAttributeIdCounter = 0;
                renderAttributeInput(attributesContainer, 0);
                renderCollectedData();
            });

            // Event listener for CSI Item (Detailed) selection change (NEW)
            itemSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                const selectedSection = sectionSelect.value;
                const selectedSubsection = subsectionSelect.value;
                const selectedItem = itemSelect.value; // NEW

                let itemDisplayName = '';

                itemDisplayName = selectedItem;

                if (selectedItem) {
                    attributeInputSection.classList.remove('hidden');
                    currentItemNameSpan.textContent = itemDisplayName;
                    attributesContainer.innerHTML = '';
                    currentAttributeIdCounter = 0;

                    // Filter existing attributes for the selected hierarchy to pre-populate the form
                    const existingAttributesForItem = collectedData.filter(item =>
                        item.division === divisionSelect.options[divisionSelect.selectedIndex].text &&
                        item.section === sectionSelect.options[sectionSelect.selectedIndex].text &&
                        item.subsection === subsectionSelect.options[subsectionSelect.selectedIndex].text &&
                        item.item === selectedItem // NEW filter
                    );

                    if (existingAttributesForItem.length > 0) {
                        existingAttributesForItem.forEach(attr => {
                            renderAttributeInput(attributesContainer, 0, attr);
                        });
                    } else {
                        renderAttributeInput(attributesContainer, 0); // Start with a blank one if none exist
                    }

                } else {
                    attributeInputSection.classList.add('hidden');
                    currentItemNameSpan.textContent = '';
                    attributesContainer.innerHTML = '';
                }
                renderCollectedData();
            });


            // Event listener for "Modify Sections" button
            modifySectionsBtn.addEventListener('click', openModifySectionsModal);
            closeModifySectionsModalBtn.addEventListener('click', closeModifySectionsModal);
            cancelSectionsModalBtn.addEventListener('click', closeModifySectionsModal);

            // Event listener for "Modify Detailed Sections" button
            modifySubsectionsBtn.addEventListener('click', openModifySubsectionsModal);
            closeModifySubsectionsModalBtn.addEventListener('click', closeModifySubsectionsModal);
            cancelSubsectionsModalBtn.addEventListener('click', closeModifySubsectionsModal);

            // Event listener for "Modify Items" button (NEW)
            modifyItemsBtn.addEventListener('click', openModifyItemsModal);
            closeModifyItemsModalBtn.addEventListener('click', closeModifyItemsModal);
            cancelItemsModalBtn.addEventListener('click', closeModifyItemsModal);


            // Event listener for "Save All Attributes" button
            saveAllAttributesBtn.addEventListener('click', saveAllAttributes);

            // Event listener for message box OK button
            messageBoxOkBtn.addEventListener('click', () => {
                messageBox.classList.add('hidden');
            });

            // Event listener for Export to Excel button
            exportExcelBtn.addEventListener('click', () => {
                if (typeof XLSX === 'undefined') {
                    showMessage('XLSX library not loaded. Please try again.');
                    return;
                }

                if (collectedData.length === 0) {
                    showMessage('No data to export.');
                    return;
                }

                const dataToExport = [];
                dataToExport.push([
                    'CSI Division', 'CSI Section', 'CSI Section (Detailed)', 'CSI Item (Detailed)', // NEW COLUMN
                    'Parent Attribute Name', 'Parent Standard Values',
                    'Attribute Level', 'Attribute Name', 'Standard Values', 'Mandatory'
                ]);

                collectedData.forEach(item => {
                    const hierarchyInfo = {
                        division: item.division,
                        section: item.section,
                        subsection: item.subsection,
                        item: item.item // NEW
                    };
                    flattenAttributesForExport([item], hierarchyInfo, null, item.level || 0, dataToExport);
                });

                let fileItemName = ''; // Updated from fileSubsectionName
                if (itemSelect.value) { // Use itemSelect value
                    fileItemName = itemSelect.value;
                } else if (subsectionSelect.value) { // Fallback to subsection if no item selected
                    fileItemName = subsectionSelect.value;
                } else {
                    fileItemName = 'All_Collected_Data';
                }

                const fileName = `${fileItemName.replace(/[^a-zA-Z0-9-_ ]/g, '')}_CSI_Attributes.xlsx`;

                const ws = XLSX.utils.aoa_to_sheet(dataToExport);
                const wb = XLSX.utils.book_new();
                XLSX.utils.book_append_sheet(wb, ws, "CSI Attributes");

                XLSX.writeFile(wb, fileName);
                showMessage("Data exported to Excel successfully!");
            });

            // Event listener for delete buttons on the collected data table (using event delegation)
            collectedDataBody.addEventListener('click', (event) => {
                if (event.target.classList.contains('delete-attribute-btn')) {
                    const pathString = event.target.dataset.path;
                    if (pathString) {
                        const path = JSON.parse(pathString);
                        deleteAttribute(path);
                    }
                }
            });

            // Initial state: Show login page on DOMContentLoaded
            document.addEventListener('DOMContentLoaded', () => {
                loginPage.classList.remove('hidden');
                mainApp.classList.add('hidden');
            });
        }

        // Login form submission handler
        loginForm.addEventListener('submit', (event) => {
            event.preventDefault(); // Prevent default form submission and page reload

            const selectedRole = roleSelect.value;
            const username = usernameInput.value;
            const password = passwordInput.value;

            const user = users[selectedRole];

            if (user && user.username === username && user.password === password) {
                currentUserRole = selectedRole;
                loginErrorMessage.classList.add('hidden'); // Hide error message on success
                initializeCSIApp(); // Initialize the main application
            } else {
                loginErrorMessage.textContent = 'Invalid role, username, or password.';
                loginErrorMessage.classList.remove('hidden');
            }
        });
    </script>
</body>
</html>
