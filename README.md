# Etsy-Chrome-Extension
Chrome Extension Developer to adapt our current plugin for Etsy. The plugin currently automates product listing field population on Facebook Marketplace. Your task will be to modify the existing codebase to achieve similar functionality for Etsy, even without direct API support.

Responsibilities:
- Review and understand the current plugin’s functionality.
- Adjust the codebase to work with Etsy’s interface.
- Automate field population (title, description, price, images, etc.) for Etsy listings.
- Ensure smooth operation and compliance with Etsy guidelines.
- Test, debug, and optimize the extension.
- Provide clear documentation for the Etsy integration.

Requirements:
- Experience building and maintaining Chrome extensions.
- Proficient in JavaScript, HTML, and CSS.
- Knowledge of browser APIs, DOM manipulation, and web scraping.
- Ability to adapt existing code for new features/platforms.
- Understanding of e-commerce platforms (Etsy experience is a plus).
- Strong problem-solving and debugging skills.

Nice to Have:
- Experience with marketplace integrations.
- Familiarity with e-commerce automation tools.
- Knowledge of Etsy’s platform and listing workflows.
-----------------------------
To adapt your existing Chrome extension for Etsy, we need to modify your plugin to automate product listing on Etsy, similar to how it works with Facebook Marketplace. Given that Etsy does not provide an official API for product listing automation, this solution will rely on DOM manipulation and form population.
Step-by-Step Process:
1. Manifest File (manifest.json)

We will need to update the manifest.json to allow the extension to run on Etsy's website.

{
  "manifest_version": 3,
  "name": "Etsy Product Listing Automation",
  "description": "Automates product listings for Etsy.",
  "version": "1.0",
  "permissions": ["activeTab", "storage"],
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["https://www.etsy.com/*"],
      "js": ["content.js"]
    }
  ],
  "action": {
    "default_popup": "popup.html"
  },
  "host_permissions": [
    "https://www.etsy.com/*"
  ]
}

2. Popup UI to Manage and Set Listings (popup.html)

We need a simple interface to allow users to input or select listing data.

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Etsy Product Listing Automation</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 10px; }
    input, textarea { width: 100%; padding: 5px; margin: 5px 0; }
    button { padding: 5px 10px; margin: 5px; }
  </style>
</head>
<body>
  <h2>Configure Your Etsy Listing</h2>
  <input type="text" id="productTitle" placeholder="Product Title" />
  <textarea id="productDescription" placeholder="Product Description"></textarea>
  <input type="number" id="productPrice" placeholder="Price" />
  <input type="file" id="productImages" multiple />
  <button id="populateListing">Populate Listing</button>
  
  <script src="popup.js"></script>
</body>
</html>

3. Popup JavaScript for Handling Form Submission (popup.js)

This script will handle collecting the data input by the user and storing it in chrome.storage.

document.getElementById('populateListing').addEventListener('click', () => {
  const title = document.getElementById('productTitle').value;
  const description = document.getElementById('productDescription').value;
  const price = document.getElementById('productPrice').value;
  const images = document.getElementById('productImages').files;

  // Store the data in chrome storage
  chrome.storage.local.set({ productTitle: title, productDescription: description, productPrice: price, productImages: images }, () => {
    alert('Listing data saved!');
  });
});

4. Content Script for Automating Listing Population on Etsy (content.js)

This script will be injected into Etsy's product listing page to auto-populate the form fields based on the data stored in chrome.storage.

// Wait for Etsy's page to load and check if it's the listing page
window.addEventListener('load', () => {
  const currentUrl = window.location.href;
  if (currentUrl.includes('/listing/create')) {
    chrome.storage.local.get(["productTitle", "productDescription", "productPrice", "productImages"], (data) => {
      if (data.productTitle) {
        // Populate the fields on Etsy's listing page
        document.querySelector('input[name="title"]').value = data.productTitle;
        document.querySelector('textarea[name="description"]').value = data.productDescription;
        document.querySelector('input[name="price"]').value = data.productPrice;

        // Handle image uploads (if any)
        if (data.productImages && data.productImages.length > 0) {
          const fileInput = document.querySelector('input[type="file"]');
          const dataTransfer = new DataTransfer();

          for (let i = 0; i < data.productImages.length; i++) {
            dataTransfer.items.add(data.productImages[i]);
          }

          fileInput.files = dataTransfer.files;
        }
      }
    });
  }
});

5. Background Script (background.js)

We can leave this file empty for now or use it to handle any background processes that may be needed in the future.

// This can be extended to handle background functionality if required

Key Considerations:

    DOM Structure on Etsy: Etsy's product listing page may change, so it's crucial to adapt the DOM element selectors accordingly. You might need to use the browser's developer tools to inspect the page and ensure the form fields are correctly targeted.

    Image Upload Handling: Etsy's form expects image files to be uploaded in a specific way. The code above uses DataTransfer to simulate selecting multiple files. Ensure this method is compatible with Etsy’s image upload field. If Etsy implements image uploads in a more complicated way (e.g., via a third-party widget), additional work might be needed to simulate the file upload.

    Compliance with Etsy’s Terms of Service: When creating automation tools, always ensure that the tool does not violate the platform's terms of service. Etsy may not support automated listing submissions, so it is important to test your extension thoroughly to avoid issues with your Etsy account.

    Error Handling and Debugging: Be sure to add error handling and debugging tools to ensure that the extension works correctly on all devices. Etsy’s site structure might also change over time, which could break the extension’s functionality.

Testing and Debugging:

    Testing on Etsy’s Listing Page: After installing the extension, go to Etsy's product listing page (https://www.etsy.com/your/shops/{shop-name}/listings/create). Ensure that the extension populates the fields properly.

    Verify Image Uploads: Test with different image files to ensure they are being properly uploaded via the extension.

    Use chrome.storage to Test Data Persistence: Verify that the saved data (title, description, price, images) persists across different sessions.

Conclusion:

This extension provides the functionality to automate the population of product listings on Etsy by injecting preset data into Etsy's form fields. It enables users to save their data and quickly populate listings without manually entering each field. If you need to make more advanced integrations or enhancements, you can expand upon this base code by adding additional features like price adjustments, category selection, etc.
