# Generate Tasks in Azure DevOps from Plan File

## Introduction
- I want you to create tasks in Azure DevOps based on the "Development Tasks" section from the plan file
- Create tasks using the same structure, names and descriptions as defined in the plan
- Only create tasks that haven't been created yet on Azure DevOps
- Update the plan file to track which tasks have been created

## Prerequisites
- Work item ID : you can find it in the first line of the plan file
- Review the "## 2. Development Tasks" section from the plan file
- Ensure the Azure DevOps work item exists

## Task Creation Process

1. Extract tasks from the "## 2. Development Tasks" section of the plan file

2. For each task in the plan, check if it has a "Created on Azure" field:
   - If "Created on Azure" field exists and has a task ID, skip this task as it's already created
   - If "Created on Azure" field doesn't exist or is empty, proceed with creating the task

3. For each task to be created, prepare a structured task object with:
   - parentId : Work item ID
   - title: Use the exact "Task Name" from the plan (e.g., "Dev FE : Create SalesAgentRegistration module")
   - description: Use the exact "Description" from the plan
   -parentId : Use the Work item ID from the plan
   - projectId: Use the exact "Project Name" from the plan
   - iterationPath: Use the exact "Iteration Path" from the plan
   "fields": {
     - Microsoft.VSTS.Common.Activity: Use "Development"
     - System.State: Use "To Do"
     - Microsoft.VSTS.Scheduling.OriginalEstimate: Use the exact "Effort Estimate" (in hours)
   }

4. Create the tasks in Azure DevOps using the task creation tool

5. After successful creation of each task, update the plan file:
   - Add a "Created on Azure: [Task ID]" field to the task in the plan file
   - Replace the task in the plan with the updated version

## Important Notes

1. Maintain the exact task names and descriptions from the plan file
2. Preserve the task organization (Frontend, Backend, Testing)
3. Only create tasks that don't have a "Created on Azure" field or where this field is empty
4. Update the plan file for each created task by adding the "Created on Azure: [Task ID]" field
5. If the task has dependencies mentioned in the plan, include them in the description
6. If the task has effort estimates mentioned in the plan, include them in the description

## Example

If the plan contains a task like:

```
#### Task 1
- Task Name: "Dev FE : Create SalesAgentRegistration module and basic structure"
- Description: Create the feature module, routing configuration, and basic component structure for the sales agent registration requests view.
- Status: New
- Dependencies: None
- Effort Estimate: 4 hours
- Iteration Path : MOJ_eAuction\Sprint 2025-05
- Project Name : MOJ_eAuction

```

Create the Azure DevOps task with:
 - parentId : 115679
 - title : "Dev FE : Create SalesAgentRegistration module and basic structure"
- Description: Create the feature module, routing configuration, and basic component structure for the sales agent registration requests view.
 - projectId: 115679
   - iterationPath:  MOJ_eAuction\Sprint 2025-05 
   "fields": {
     - Microsoft.VSTS.Common.Activity: Development 
     - System.State: To Do
     - Microsoft.VSTS.Scheduling.OriginalEstimate:4 hours
   }

Then, after creating the task and getting back the task ID (e.g., 12345), update the plan file to:

```