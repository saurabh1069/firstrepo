# Load the Outlook COM object
Add-Type -TypeDefinition @"
using System.Runtime.InteropServices;
using Outlook = Microsoft.Office.Interop.Outlook;
"@

$Outlook = New-Object -ComObject Outlook.Application
$Namespace = $Outlook.GetNamespace('MAPI')

# Function to reply to an email and include original text
Function ReplyWithOriginalText($emailItem) {
    $actions = $emailItem.Actions
    $action = $actions["Reply"]
    [System.Runtime.InteropServices.Marshal]::ReleaseComObject($actions)
    $action.ReplyStyle = [Outlook.OlActionReplyStyle]::olIncludeOriginalText
    $response = $action.Execute() -as [Outlook.MailItem]
    [System.Runtime.InteropServices.Marshal]::ReleaseComObject($action)
    $response.Display()
    [System.Runtime.InteropServices.Marshal]::ReleaseComObject($response)
}

# Function to send a reminder email
Function Send-Reminder($originalEmail, $reminderNumber) {
    # Create a new email item for the reminder
    $reminderEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the reminder email
    $reminderEmail.Subject = "Service Desk Reminder: $reminderNumber - $($originalEmail.Subject)"
    $reminderEmail.Body = "This is a reminder for your Service Desk request: $($originalEmail.Subject)."
    $reminderEmail.Recipients.Add($originalEmail.SenderEmailAddress)
    
    # Use the ReplyWithOriginalText function to reply with the reminder
    ReplyWithOriginalText $reminderEmail
    
    # Send the reminder email
    $reminderEmail.Send()
}

# Function to send a final email
Function Send-Final-Email($originalEmail) {
    # Create a new email item for the final email
    $finalEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the final email
    $finalEmail.Subject = "Service Desk Final Reminder - $($originalEmail.Subject)"
    $finalEmail.Body = "This is the final reminder for your Service Desk request: $($originalEmail.Subject)."
    $finalEmail.Recipients.Add($originalEmail.SenderEmailAddress)
    
    # Use the ReplyWithOriginalText function to reply with the final reminder
    ReplyWithOriginalText $finalEmail
    
    # Send the final email
    $finalEmail.Send()
}

# Function to check if an email has replies in a specific folder
Function EmailHasReplies($email, $folder) {
    # Get the conversation ID of the email
    $conversationID = $email.ConversationID
    
    # Check if any email in the folder has the same conversation ID (a reply)
    $replies = $folder.Items | Where-Object { $_.ConversationID -eq $conversationID }
    
    # Return whether there are replies
    return $replies.Count -gt 1
}

# Rest of your code
