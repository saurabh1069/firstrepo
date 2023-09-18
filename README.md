# Load the data from a CSV file
$filePath = "C:\Users\t692938\OneDrive - UBS\Rosterdata.csv"
$df = Import-Csv -Path $filePath

# Drop specified rows
$rowsToDrop = 0, 1, 2, 3, 8, 9, 10, 11, 16, 17
$df = $df | Where-Object { $_.'YourColumnName' -notin $rowsToDrop }

# Reset the index
$df = $df | Select-Object -Property *, @{Name='index'; Expression={$_.Index -as [int]}}

# Rename a column
$df = $df | ForEach-Object { $_.'YourColumnName' = $_.'NewColumnName'; $_ }

# Select a subset of rows and columns
$df = $df | Select-Object -First 12 | Select-Object -Property Column1, Column2, ...  # Specify the column names you need

# Save the modified DataFrame to a new CSV file
$exportPath = "C:\Users\t692938\OneDrive - UBS\UBS_New_File.csv"
$df | Export-Csv -Path $exportPath -NoTypeInformation
