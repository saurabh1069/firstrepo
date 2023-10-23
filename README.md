$OriginalEmailHTMLFile = [System.IO.Path]::GetTempFileName() + ".html"
        $Email.SaveAs($OriginalEmailHTMLFile, 5) # 5 represents olHTML format

        # Create a reply email
        $ReplyEmail = $Email.Reply()

        # Load the original email's HTML content into the reply
        $OriginalEmailHTML = Get-Content -Path $OriginalEmailHTMLFile -Raw
        $ReplyEmail.HTMLBody = $OriginalEmailHTML + "<br><br>Your reply message here"

        # Send the reply
        $ReplyEmail.Send()
