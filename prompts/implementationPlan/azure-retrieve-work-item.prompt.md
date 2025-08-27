---
description: 'Retrieves an Azure DevOps work item and saves it to a file without performing any analysis or modification.'
---

# Work Item Retrieval
- I will provide you with a work item ID in the prompt
- Please perform the retrieval process described below
- Save the work item details to a markdown file in the 'prompts/plans' folder
- Do NOT refetch work items in a loop - retrieve the work item once and work with the retrieved data

## Retrieval Process

1. Retrieve Work Item Content
Retrieve Azure DevOps work item by its ID (e.g., #{id}) ONCE ONLY and gather all available information, including:
- Title
- Description (make sure to retrieve the COMPLETE description text and convert any HTML to equivalent Markdown)
- Area Path
- Iteration Path
- Acceptance Criteria
- Any other fields available in the response

2. Save the Work Item Details
- Create a markdown file in the `prompts/plans` folder
- Name the file using the format: `{id}-{work-item-title-in-kebab-case}-workitem-content.md`
- Structure the file with the following sections:
  - **Work Item Details**: Basic metadata (ID, title, state, etc.)
  - **Description**: The full description content converted to Markdown
  - **Acceptance Criteria**: The full acceptance criteria converted to Markdown (if available)
  - **Attachments**: List of any attached files
  - **Related Links**: Any related work items or external links
- Follow these conversion rules:
  - Convert HTML tables to Markdown tables
  - Convert HTML lists to Markdown lists
  - Convert `<br>` tags to line breaks
  - Convert `<p>` tags to paragraphs with blank lines
  - Convert HTML text formatting (bold, italic, etc.) to Markdown syntax
  - Convert HTML links to Markdown links
  - Preserve text direction using Markdown RTL/LTR markers when needed
  - Preserve special characters
- Maintain the original language (whether Arabic, English, or any other language)
- Convert ALL HTML formatting to equivalent Markdown syntax
- Do not translate any content
- Do not analyze or modify the actual content meaning

**IMPORTANT**: Convert the format to proper Markdown while preserving all original content, structure, and meaning. The goal is to produce a clean, readable Markdown file that contains all the original information.
