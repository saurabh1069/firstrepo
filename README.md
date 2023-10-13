# Load the Outlook COM object
Add-Type -AssemblyName 'Microsoft.Office.Interop.Outlook'
$Outlook = New-Object -ComObject Outlook.Application
$Namespace = $Outlook.GetNamespace('MAPI')
$Inbox = $Namespace.GetDefaultFolder('olFolderInbox')
$SentItems = $Namespace.GetDefaultFolder('olFolderSentMail')

# Define time intervals for reminders
$firstReminderDays = 5
$secondReminderDays = 2
$thirdReminderDays = 2

# Get today's date
$today = Get-Date

# Function to send a reminder email
Function Send-Reminder($originalEmail, $reminderNumber) {
    # Create a new email item
    $reminderEmail = $Outlook.CreateItem(0)
    
    # Set the properties of the reminder email
    $reminderEmail.Subject = "Service Desk Reminder: $reminderNumber - $originalEmail.Subject"
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
    $finalEmail.Subject = "Service Desk Final Reminder - $originalEmail.Subject"
    $finalEmail.Body = "This is the final reminder for your Service Desk request: $($originalEmail.Subject)."
    $finalEmail.Recipients.Add($originalEmail.SenderEmailAddress)
    
    # Send the final email
    $finalEmail.Send()
}

# Define a function for processing emails in parallel
Function Process-Email($email) {
    $originalEmail = $null

    if ($email.ConversationID) {
        $originalEmail = $SentItems.Items | Where-Object { $_.ConversationID -eq $email.ConversationID } | Sort-Object ReceivedTime | Select-Object -Last 1
    }

    if ($originalEmail) {
        $originalReceivedDate = $originalEmail.ReceivedTime
        $timeSinceReceived = ($today - $originalReceivedDate).TotalDays

        if ($timeSinceReceived -ge $firstReminderDays) {
            Send-Reminder $originalEmail "Reminder 1"
        } elseif ($timeSinceReceived -ge ($firstReminderDays + $secondReminderDays)) {
            Send-Reminder $originalEmail "Reminder 2"
        } elseif ($timeSinceReceived -ge ($firstReminderDays + $secondReminderDays + $thirdReminderDays)) {
            Send-Reminder $originalEmail "Reminder 3"
            Send-Final-Email $originalEmail
        }
    }
}

# Create a runspace pool for parallel processing
$runspacePool = [runspacefactory]::CreateRunspacePool(1, [Environment]::ProcessorCount)
$runspacePool.Open()

# Create runspaces and assign email processing to them
$runspaces = @()
foreach ($email in $Inbox.Items) {
    $runspace = [powershell]::Create()
    $runspace.RunspacePool = $runspacePool

    $runspace.AddScript({
        param($email)
        Process-Email $email
    })
    $runspace.AddArgument($email)

    $runspaces += [PSCustomObject]@{
        Pipe = $runspace
        Status = $runspace.BeginInvoke()
    }
}

# Wait for all runspaces to complete
$runspaces | ForEach-Object {
    $_.Pipe.EndInvoke($_.Status)
    $_.Pipe.Dispose()
}

# Release COM objects
[System.Runtime.Interopservices.Marshal]::ReleaseComObject($Outlook) | Out-Null
