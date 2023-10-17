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

# Get today's date
$today = Get-Date

# Hashtable to store the latest emails in "Testing" conversations
$latestEmails = @{}

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

# Process emails in "Testing"
$emailsTesting = $folderTesting.Items | Where-Object { $_.ReceivedTime -ge (Get-Date).AddDays(-7) }

foreach ($email in $emailsTesting) {
    if ($email.ConversationID) {
        $conversationID = $email.ConversationID
        $originalEmail = $latestEmails[$conversationID]

        if (-not $originalEmail) {
            # New conversation in "Testing," store the current email as the latest
            $latestEmails[$conversationID] = $email
        } else {
            # Existing conversation, compare received times
            $originalReceivedDate = $originalEmail.ReceivedTime
            $timeSinceReceived = ($today - $originalReceivedDate).TotalDays

            # Check if the email in "Testing" has not been replied to in "Testing1"
            $correspondingEmailInTesting1 = $folderTesting1.Items | Where-Object { $_.ConversationID -eq $conversationID }
            if ($timeSinceReceived -ge $firstReminderDays -and -not $correspondingEmailInTesting1) {
                Send-Reminder $originalEmail "Reminder 1"
            } elseif ($timeSinceReceived -ge ($firstReminderDays + $secondReminderDays) -and -not $correspondingEmailInTesting1) {
                Send-Reminder $originalEmail "Reminder 2"
            } elseif ($timeSinceReceived -ge ($firstReminderDays + $secondReminderDays + $thirdReminderDays) -and -not $correspondingEmailInTesting1) {
                Send-Reminder $originalEmail "Reminder 3"
                Send-Final-Email $originalEmail
            }
        }
    }
}

# Release COM objects
[System.Runtime.Interopservices.Marshal]::ReleaseComObject($Outlook) | Out-Null
