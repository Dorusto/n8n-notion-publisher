# 🤖 AI-Powered Content Publishing Workflow (n8n)

## Overview
This n8n workflow completely automates the content publishing process, from draft to distribution. It monitors a dedicated **Notion** database, uses an **OpenAI (GPT)** model for comprehensive SEO optimization and text refinement, automatically publishes the final article to **WordPress**, handles image management (DALL-E or URL import), and shares the post on social media (**Facebook**).

## ✨ Key Features

* **Notion Integration:** Triggers the workflow upon status change (`Ready for AI` or `Ready to Publish`) in the specified Notion database.
* **AI Optimization & Refinement:** Utilizes the OpenAI model to act as a copywriter and SEO expert:
    * Refines the content, maintaining the author's voice while shifting the tone to be constructive and solution-oriented.
    * Generates essential SEO components: **SEO Title**, **Meta Description**, **Focus Keyphrase**, **Keywords**, and a compelling **Social Hook** text.
* **Content Synchronization:** Updates the Notion page properties with all the AI-generated SEO data.
* **WordPress Publisher:**
    * Creates a new post on your WordPress site.
    * Manages the featured image: uploads an existing URL image from Notion or generates a new one via DALL-E (if routed to that branch).
    * Applies SEO metadata (Focus Keyphrase, Title, Description) using a direct HTTP request workaround for Yoast/Rank Math.
* **Social Distribution:** Automatically posts the Social Hook and article link to **Facebook**.
* **Review Option:** Sends the final, optimized article content via **Gmail** for email review.

## 🛠️ Prerequisites

To run this workflow, you need an **n8n instance** and valid credentials for the following services:

| Service | Credential Name in Workflow | Purpose |
| :--- | :--- | :--- |
| **Notion** | `Notion to Blogsport` | Trigger, read content, update SEO properties. |
| **OpenAI** | `OpenAi account` | Content optimization and DALL-E image generation. |
| **WordPress** | `Wordpress account` | Publishing posts, uploading media, and setting metadata. |
| **Facebook Graph API**| `Facebook Graph account` | Posting the social hook. |
| **Gmail** | `Gmail account` | Sending the content review email. |

## 🚀 Setup and Installation

1.  **Download:** Save the provided JSON file as `workflow.json`.
2.  **Import:** Import the `workflow.json` file into your n8n instance.
3.  **Configure Credentials:** In n8n, update all referenced credentials (listed in the table above) with your actual API keys and accounts.
4.  **Update Parameters:**
    * In the **Notion Trigger** node, replace the placeholder `databaseId` with your target Notion database ID.
    * In the WordPress-related HTTP Request nodes (e.g., *Set Featured Image*, *Add Metadata*), update the base URL `https://www.dorustoica.ro/` to your own website domain.
    * Customize the DALL-E prompt in the **Generate an image** node if desired.

## ⚙️ Key Technical Notes (Code Nodes)

The workflow relies on several custom Code Nodes (JavaScript) to seamlessly connect services:

* **Extract Content from block to MD:** Recursively extracts text from complex Notion block structures (paragraphs, headings, lists, children) and formats the entire article into a single clean **Markdown** string ready for the AI.
* **Parse text String to JSON:** Cleans the text output from the AI node (which typically contains Markdown wrappers like ` ```json...``` `) and ensures a clean JSON object is passed downstream.
* **Optimization for Notion:** Takes the AI's final `optimized_text` (in Markdown) and converts it back into an array of structured blocks (Paragraph, Heading 1/2/3, Bulleted/Numbered List Items) compatible with the Notion API for full page content updates.
