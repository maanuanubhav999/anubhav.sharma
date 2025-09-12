---
title: "How I Automated Email Monitoring and Notification Alerts with a Simple Script"
date: 2025-09-12 10:00:00 +0530
categories: [automation, productivity]
tags: [Google Appscript, email, automation, scripts]
---

Recently, I wanted to order protein but it was mostly out of stock, website had email notifications. I didn't want to keep checking my email every few minutes, so I wrote a simple script to monitor my inbox and send me a Notification whenever I received an email from the store.

## The Problem

I was trying to buy protein powder from Amul’s website, which is always out of stock. They do send “back in stock” emails, but I kept missing them and losing the chance to order.

So I built a small script using Google Apps Script that checks my inbox every minute for new emails from the sender. If it finds one, it sends a notification to my phone using pushover.net.

It’s super simple and runs without any cost—I just set it once, and now I never miss a restock! A tiny automation, but it’s been a game-changer for me 📦📲


## The Script

Here's a simplified version of the script:

```javascript
function checkForNewEmails() {
  // Configuration - UPDATE THESE VALUES
  const SPECIFIC_SENDER = "estore@amul.coop"; // Replace with the sender's email
  const LABEL_NAME = "ProcessedEmails"; // Label to track processed emails
  
  try {
    // Get or create a label to track processed emails
    let label = GmailApp.getUserLabelByName(LABEL_NAME);
    if (!label) {
      label = GmailApp.createLabel(LABEL_NAME);
    }
    
    // Search for emails from the specific sender that haven't been processed
    const searchQuery = `from:${SPECIFIC_SENDER} -label:${LABEL_NAME}`;
    const threads = GmailApp.search(searchQuery, 0, 10); // Check last 10 emails
    
    console.log(`Found ${threads.length} unprocessed emails from ${SPECIFIC_SENDER}`);
    
    // Process each thread
    threads.forEach(thread => {
      const messages = thread.getMessages();
      
      messages.forEach(message => {
        // Check if this specific message is from our sender and unprocessed
        if (message.getFrom().includes(SPECIFIC_SENDER) && !message.isInTrash()) {
          
          // Get email details
          const subject = message.getSubject();
          const body = message.getPlainBody();
          const date = message.getDate();
          const sender = message.getFrom();
          
          console.log(`Processing email: ${subject}`);
          
          // CUSTOM ACTIONS - Add your logic here
          performCustomAction(subject, body, sender, date);
          
          // Mark as processed by adding label
          thread.addLabel(label);
        }
      });
    });
    
  } catch (error) {
    console.error('Error in checkForNewEmails:', error);
    
    // Optional: Send error notification email
    GmailApp.sendEmail(
      Session.getActiveUser().getEmail(),
      'Gmail Monitor Script Error',
      `Error occurred: ${error.toString()}`
    );
  }
}
```

> **Note:** Have to provide permission so that apps script can read / write to your email.

## Results

- I now get instant notifications for "back in stock" emails.
- The script runs every 1 minutes free of cost.

---

