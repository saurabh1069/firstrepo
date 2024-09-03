using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq; // Make sure to install Newtonsoft.Json via NuGet

public class Talk2Nucleus
{
    private static readonly HttpClient client = new HttpClient();

    public static async Task<string> GetResponseAsync(string query)
    {
        // Define the API URL
        string url = "https://openai-nucleus-dev.azpriv-cloud.ubs.net/api/v1/openai-sandbox/chat";

        // Define the API key (change this to your actual API key)
        string apiKey = "API-full";

        // Define the headers
        client.DefaultRequestHeaders.Clear();
        client.DefaultRequestHeaders.Add("api-key", apiKey);

        // Define the body content
        var body = new
        {
            messages = new[]
            {
                new { role = "system", content = "Assistant is a large language model trained by OpenAI." },
                new { role = "user", content = query }
            }
        };

        // Convert the body to JSON format
        string jsonBody = Newtonsoft.Json.JsonConvert.SerializeObject(body);

        // Set up the content for the POST request
        var content = new StringContent(jsonBody, Encoding.UTF8, "application/json");

        try
        {
            // Make the POST request