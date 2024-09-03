using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace HttpClientExample
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Define the API endpoint
            string url = "https://cirruspl-myhub-svcs-dev-neu-hackathon.azurewebsites.net/api/token/impersonate/43707788";

            // Create an instance of HttpClient
            using (HttpClient client = new HttpClient())
            {
                // Define the headers
                client.DefaultRequestHeaders.Accept.Add(new System.Net.Http.Headers.MediaTypeWithQualityHeaderValue("text/plain"));

                // Define the JSON payload
                var jsonData = new
                {
                    clientKey = "B6F2584E-28E1-4AB1-A403-D54E5A1EA8AF"
                };

                // Serialize the payload to a JSON string
                string jsonString = System.Text.Json.JsonSerializer.Serialize(jsonData);

                // Create the StringContent from the JSON string
                StringContent content = new StringContent(jsonString, Encoding.UTF8, "application/json");

                // Make the POST request
                HttpResponseMessage response = await client.PostAsync(url, content);

                // Ensure the response is successful
                if (response.IsSuccessStatusCode)
                {
                    // Read the response content
                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Response received: " + responseContent);
                }
                else
                {
                    // Handle error response
                    Console.WriteLine("Error: " + response.StatusCode);
                }
            }
        }
    }
}