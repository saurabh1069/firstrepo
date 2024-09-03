# Define the URL
$url = "https://openai-nucleus-dev.azpriv-cloud.ubs.net/api/v1/openai-sandbox/chat"

# Define the headers with the API key
$headers = @{
    "api-key" = "API-full"
}

# Define the query
$query = "Can you introduce yourself?"

# Define the body with messages
$body = @{
    "messages" = @(
        @{
            "role" = "system"
            "content" = "Assistant is a large language model trained by OpenAI."
        },
        @{
            "role" = "user"
            "content" = $query
        }
    )
}

# Convert the body to JSON format
$bodyJson = $body | ConvertTo-Json

# Make the POST request
$response = Invoke-WebRequest -Uri $url -Headers $headers -Method Post -Body $bodyJson -ContentType "application/json" -UseBasicParsing

# Parse the JSON response
$responseJson = $response.Content | ConvertFrom-Json

# Extract the answer from the response
$answer = $responseJson.result.content

# Output the answer
Write-Output $answer