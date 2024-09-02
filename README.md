using System;
using System.Data.OleDb; // Add this namespace for Access database connectivity
using System.Windows;
using System.Windows.Media.Animation;
using Microsoft.Web.WebView2.Core;

namespace MyCultureHub
{
    public partial class AnimatedPromptWindow : Window
    {
        private string connectionString = @"Provider=Microsoft.ACE.OLEDB.12.0;Data Source=your_database_path.accdb"; // Update the path to your Access database

        public AnimatedPromptWindow()
        {
            InitializeComponent();
        }

        private async void Window_Loaded(object sender, RoutedEventArgs e)
        {
            await webView2Control.EnsureCoreWebView2Async();
            LoadContentFromDatabase("Newsletter"); // Load default content on window load
        }

        private void LoadContentFromDatabase(string type)
        {
            using (OleDbConnection connection = new OleDbConnection(connectionString))
            {
                string query = "SELECT Content FROM HtmlContent WHERE Type = ?";
                OleDbCommand command = new OleDbCommand(query, connection);
                command.Parameters.AddWithValue("@Type", type);

                connection.Open();
                string htmlContent = command.ExecuteScalar()?.ToString();
                connection.Close();

                if (!string.IsNullOrEmpty(htmlContent))
                {
                    webView2Control.NavigateToString(htmlContent);
                }
                else
                {
                    MessageBox.Show("No content found for the specified type.", "Error", MessageBoxButton.OK, MessageBoxImage.Error);
                }
            }
        }

        // Event handlers for button clicks
        private void Button1_Click(object sender, RoutedEventArgs e)
        {
            LoadContentFromDatabase("Newsletter"); // Update with your desired type
        }

        private void Button2_Click(object sender, RoutedEventArgs e)
        {
            LoadContentFromDatabase("Update"); // Update with your desired type
        }

        private void Button3_Click(object sender, RoutedEventArgs e)
        {
            LoadContentFromDatabase("Promotion"); // Update with your desired type
        }

        private void Button4_Click(object sender, RoutedEventArgs e)
        {
            LoadContentFromDatabase("Alert"); // Update with your desired type
        }

        private void CloseButton_Click(object sender, RoutedEventArgs e)
        {
            this.Close();
        }

        private void MinimizeButton_Click(object sender, RoutedEventArgs e)
        {
            this.WindowState = WindowState.Minimized;
        }

        private void MaximizeButton_Click(object sender, RoutedEventArgs e)
        {
            if (this.WindowState == WindowState.Maximized)
                this.WindowState = WindowState.Normal;
            else
                this.WindowState = WindowState.Maximized;
        }
    }
}
