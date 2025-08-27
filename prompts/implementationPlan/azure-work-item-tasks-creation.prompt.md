# Work Item Implementation Guide
- i will provide you with work item details file in the prompt, use this file to build the implementation plan
- i want you to create the implementation plan file
- only create plan file, don't make any other changes or implementations
- Plan file Name: - Plan file Name: `<work-item-id>-task-creation-<work-item-title-in-kebab-case>`
- Save the implementation plan file in the `Implementation Plans/{work-item-id}` folder
- respond in english 

## Tasks Template
   **Task Name**:
     - Backend tasks: "Dev BE : [context]"
     - Frontend UI design tasks: "Dev FE : [context]"
     - Frontend integration tasks: "Dev FE : [context]"
   **Description**: [Clear, actionable description]
   **Status**: To Do
   **Project Id**: MOJ_eAuction
   **Area Path**: MOJ_eAuction
   **Iteration Path**:MOJ_eAuction\Sprint 2025-06
   **Dependencies**: None
   **Parent Work Item**:[work-item-id]
   **Additional Fields**: {
     **Activity**: Development
     **Original Estimate**: [Time estimate in hours]
   }

## Tasks List

1. create development tasks 
   - use the task template section for tasks template
   - IMPORTANT: Review all previous workitem implementation plans in `Implementation Plans/{**-workitem-implementaion-plan.md}` folder and exclude any tasks that have already been planned in previous work items
   - Only include new tasks that are specific to this work item and haven't been implemented before
   - tasks type are :
      - Frontend 
         - Development : implement UI design (html and css only)
         - Integration : type script and integration with backend apis
      - backend : apis development (exclude creating view models or DTOs as separate tasks)
      - Database changes
      - Over-all workitem test (end to end test)(don't create unit test tasks)
