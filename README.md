# Function to send a reminder email with the entire conversation history
Function Send-Reminder($originalEmail, $reminderNumber) {
    # Get the conversation ID of the original email
    $conversationID = $originalEmail.ConversationID
    
    # Get all emails in the conversation
    $conversationEmails = $folderTesting.Items | Where-Object { $_.ConversationID -eq $conversationID }
    
    # Create a new email item for the reminder
    $reminderEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the reminder email
    $reminderEmail.Subject = "Service Desk Reminder: $reminderNumber - $($originalEmail.Subject)"
    
    # Combine the entire conversation history in the email body
    $conversationHistory = $conversationEmails | ForEach-Object { "`r`n$($_.ReceivedTime.ToString()) - $($_.SenderName): $($_.Body)" }
# Function to send a final email with the entire conversation history
Function Send-Final-Email($originalEmail) {
    # Get the conversation ID of the original email
    $conversationID = $originalEmail.ConversationID
    
    # Get all emails in the conversation
    $conversationEmails = $folderTesting.Items | Where-Object { $_.ConversationID -eq $conversationID }
    
    # Create a new email item for the final email
    $finalEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the final email
    $finalEmail.Subject = "Service Desk Final Reminder - $($originalEmail.Subject)"
    
    # Combine the entire conversation history in the email body
    $conversationHistory = $conversationEmails | ForEach-Object { "`r`n$($_.ReceivedTime.ToString()) - $($_.SenderName): $($_.Body)" }
    $finalEmail.Body = "This is the final reminder for your Service Desk request: $($originalEmail.Subject). Conversation history:$conversationHistory"
    
    # Add recipients from the conversation
    foreach ($email in $conversationEmails) {
        $finalEmail.Recipients.Add($email.SenderEmailAddress)
    }
    
    # Send the final email
    $finalEmail.Send()
}
    $reminderEmail.Body = "This is a reminder for your Service Desk request: $($originalEmail.Subject). Conversation history:$conversationHistory"
    
    # Add recipients from the conversation
    foreach ($email in $conversationEmails) {
        $reminderEmail.Recipients.Add($email.SenderEmailAddress)
    }
    
    # Send the reminder email
    $reminderEmail.Send()
}
