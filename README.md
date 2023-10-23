$ReplyEmail = $Email.ReplyAll()

        # Include the original email content with formatting
        $OriginalEmail = $Email.Forward()
        $OriginalEmail.Recipients.Add($ReplyEmail.SenderEmailAddress) | Out-Null
        $OriginalEmail.HTMLBody = $Email.HTMLBody
        $OriginalEmail.Send()
