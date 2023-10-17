# Load the Outlook COM object
Add-Type -AssemblyName 'Microsoft.Office.Interop.Outlook'
$Outlook = New-Object -ComObject Outlook.Application

# Create a new MailItem
Function Create-MailItem {
    $mailItem = $Outlook.CreateItem(0)  # 0 corresponds to olMailItem, which is a MailItem
    return $mailItem
}

# Function to send a reminder email
Function Send-Reminder($originalEmail, $reminderNumber) {
    # Get the conversation ID of the original email
    $conversationID = $originalEmail.ConversationID
    
    # Get all emails in the conversation
    $conversationEmails = $folderTesting.Items | Where-Object { $_.ConversationID -eq $conversationID }
    
    # Create a new email item for the reminder
    $reminderEmail = Create-MailItem
    
    # Set the properties of the reminder email
    $reminderEmail.Subject = "Service Desk Reminder: $reminderNumber - $($originalEmail.Subject)"
    
    # Combine the entire conversation history in the email body
    $conversationHistory = $conversationEmails | ForEach-Object { "`r`n$($_.ReceivedTime.ToString()) - $($_.SenderName): $($_.Body)" }
    $reminderEmail.Body = "This is a reminder for your Service Desk request: $($originalEmail.Subject). Conversation history:$conversationHistory"
    
    # Use the ReplyAll method to reply to all participants
    $reminderEmail.ReplyAll()
    
    # Send the reminder email
    $reminderEmail.Send()
}

# Function to send a final email
Function Send-Final-Email($originalEmail) {
    # Get the conversation ID of the original email
    $conversationID = $originalEmail.ConversationID
    
    # Get all emails in the conversation
    $conversationEmails = $folderTesting.Items | Where-Object { $_.ConversationID -eq $conversationID }
    
    # Create a new email item for the final email
    $finalEmail = Create-MailItem
    
    # Set the properties of the final email
    $finalEmail.Subject = "Service Desk Final Reminder - $($originalEmail.Subject)"
    
    # Combine the entire conversation history in the email body
    $conversationHistory = $conversationEmails | ForEach-Object { "`r`n$($_.ReceivedTime.ToString()) - $($_.SenderName): $($_.Body)" }
    $finalEmail.Body = "This is the final reminder for your Service Desk request: $($originalEmail.Subject). Conversation history:$conversationHistory"
    
    # Use the ReplyAll method to reply to all participants
    $finalEmail.ReplyAll()
    
    # Send the final email
    $finalEmail.Send()
}
