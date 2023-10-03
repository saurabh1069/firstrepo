To automate the assignment of 10 ServiceNow tickets to 3 resources in a team using a logical workflow, you can use a combination of if statements and for loops in Automation Anywhere. Here's a step-by-step logical workflow:

1. **Launch ServiceNow**: Use Automation Anywhere to launch the ServiceNow application.

2. **Log In (if required)**: If ServiceNow requires authentication, create automation to log in using appropriate credentials.

3. **Navigate to Ticket Assignment**: Automate the navigation to the ticket assignment section within ServiceNow.

4. **Retrieve Ticket Information**: Use GUI automation to extract information about the 10 available tickets, including ticket IDs and any other relevant details.

5. **Calculate Ticket Distribution**:
   - Calculate how many tickets each resource should handle: `tickets_per_resource = total_tickets // num_resources`
   - Calculate the remaining tickets: `remaining_tickets = total_tickets % num_resources`

6. **Assign Tickets Using For Loop**:
   - Use a for loop that iterates through the 3 resources (assuming `num_resources = 3`):
     - Within the loop:
       - Check if it's the last iteration (`if current_resource == num_resources`).
         - If it is the last iteration, assign the remaining tickets to this resource: `assignments[current_resource] = assignments[current_resource] + remaining_tickets`
         - If not the last iteration, assign an equal number of tickets to this resource: `assignments[current_resource] = tickets_per_resource`
       - Automate the assignment of tickets to the current resource:
         - Click on a ticket.
         - Select the current resource from a dropdown or assign the ticket in the way your ServiceNow instance allows.
         - Move to the next ticket and repeat the assignment until the required number of tickets is assigned to the current resource.

7. **Verify and Log Assignments**: After the for loop completes, verify that each resource has been assigned the correct number of tickets. You can use Automation Anywhere's features to check ticket assignments and log any discrepancies.

8. **Log Out and Close ServiceNow**: If necessary, create automation to log out of ServiceNow and close the application.

9. **Error Handling**: Implement error handling to deal with unexpected situations, such as errors in ServiceNow or disruptions in the automation process.

10. **Schedule and Run**: Schedule the automation to run at the desired times or trigger it manually as needed.

This logical workflow will distribute the tickets evenly among the 3 resources in your team using a for loop and appropriate calculations. Ensure that you adapt the GUI automation steps to match the specific interface and functionality of your ServiceNow instance. Testing and validation are crucial to ensure the automation works reliably.