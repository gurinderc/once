# Once More Blog
```
param (
    [string]$SourceFolder,
    [string]$OutputFileName,
    [string]$Filter
)

# Ensure the SourceFolder exists
if (-Not (Test-Path -Path $SourceFolder)) {
    Write-Error "SourceFolder does not exist: $SourceFolder"
    exit 1
}

# Get all CSV files from the SourceFolder using the Filter
$csvFiles = Get-ChildItem -Path $SourceFolder -Filter $Filter

if ($csvFiles.Count -eq 0) {
    Write-Error "No files found matching the filter: $Filter"
    exit 1
}

# Initialize a flag to track the header
$headerWritten = $false

# Initialize an array to store merged content
$mergedContent = @()

foreach ($csvFile in $csvFiles) {
    $csvContent = Import-Csv -Path $csvFile.FullName

    if (-Not $headerWritten) {
        # Write the header only once
        $mergedContent += $csvContent[0].psobject.Properties.Name -join ","
        $headerWritten = $true
    }

    # Add the CSV content excluding the header
    $mergedContent += $csvContent | Select-Object -Skip 1
}

# Write the merged content to the OutputFileName
$mergedContent | Out-File -FilePath $OutputFileName -Encoding utf8

Write-Output "Merged CSV files written to $OutputFileName"
```
