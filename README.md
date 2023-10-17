# Load the Outlook COM object
Add-Type -AssemblyName 'Microsoft.Office.Interop.Outlook'
$Outlook = New-Object -ComObject Outlook.Application
$Namespace = $Outlook.GetNamespace('MAPI')

# Specify the folder paths for "Testing" and "Testing1"
$folderPathTesting = "Testing"
$folderPathTesting1 = "Testing1"

# Get the folders by path
$folderTesting = $Namespace.GetDefaultFolder('olFolderInbox').Folders.Item($folderPathTesting)
$folderTesting1 = $Namespace.GetDefaultFolder('olFolderInbox').Folders.Item($folderPathTesting1)

# Define time intervals for reminders
$firstReminderDays = 5
$secondReminderDays = 2
$thirdReminderDays = 2

# Function to send a reminder email
Function Send-Reminder($originalEmail, $reminderNumber) {
    # Create a new email item
    $reminderEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the reminder email
    $reminderEmail.Subject = "Service Desk Reminder: $reminderNumber - $($originalEmail.Subject)"
    $reminderEmail.Body = "This is a reminder for your Service Desk request: $($originalEmail.Subject)."
    $reminderEmail.Recipients.Add($originalEmail.SenderEmailAddress)
    
    # Send the reminder email
    $reminderEmail.Send()
}

# Function to send a final email
Function Send-Final-Email($originalEmail) {
    # Create a new email item
    $finalEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the final email
    $finalEmail.Subject = "Service Desk Final Reminder - $($originalEmail.Subject)"
    $finalEmail.Body = "This is the final reminder for your Service Desk request: $($originalEmail.Subject)."
    $finalEmail.Recipients.Add($originalEmail.SenderEmailAddress)
    
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

# Process emails in "Testing"
$emailsTesting = $folderTesting.Items

foreach ($email in $emailsTesting) {
    # Check if the email is within the time frame for reminders
    $timeSinceReceived = (Get-Date) - $email.ReceivedTime
    
    if ($timeSinceReceived.TotalDays -ge $firstReminderDays) {
        if (-not (EmailHasReplies $email $folderTesting1)) {
            Send-Reminder $email "Reminder 1"
        }
    } elseif ($timeSinceReceived.TotalDays -ge ($firstReminderDays + $secondReminderDays)) {
        if (-not (EmailHasReplies $email $folderTesting1)) {
            Send-Reminder $email "Reminder 2"
        }
    } elseif ($timeSinceReceived.TotalDays -ge ($firstReminderDays + $secondReminderDays + $thirdReminderDays)) {
        if (-not (EmailHasReplies $email $folderTesting1)) {
            Send-Reminder $email "Reminder 3"
            Send-Final-Email $email
        }
    }
}

# Release COM objects
[System.Runtime.Interopservices.Marshal]::ReleaseComObject($Outlook) | Out-Null
