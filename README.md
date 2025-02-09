# Report_Generator
## Optimized Archiving Process to Enhance Case Management Efficiency

During my internship as a Data Governance Intern, one of my primary responsibilities was managing archived cases and freeing up storage space on our main hard drive. The goal was to ensure that attorneys and paralegals could efficiently manage their active cases without delays, improving the overall workflow.

Faced with the challenge of archiving files, we identified an opportunity to streamline the process by focusing on archiving the largest files instead of randomly selected ones. This change not only helped us optimize storage but also enhanced the accessibility of important case files, enabling quicker access to active cases.

Due to strict confidentiality policies, I was unable to provide direct details on the defendant cases handled, but I utilized personal desktop files as an analogy for the files we managed in the archive. The main difference was the volume—our team managed over 3,000 cases in the archive instance, requiring meticulous organization.

A key part of my initiative involved automating the process of identifying the heaviest files and generating a PDF report that listed the cases in descending order by size, with details on the size (MB), number of files, and last modified date. This approach significantly improved the efficiency and accuracy of archiving.

By leveraging Python, I developed a script that streamlined this process, making it not only faster but also easier to implement and replicate. The automation allowed us to make data-driven decisions on file prioritization, ultimately contributing to a more organized and efficient data governance system.

## Libraries
```r
import os
import pandas as pd
from fpdf import FPDF
from datetime import datetime
```
## Defining the Function
```r
def get_folder_info(folder_path):
    """Calculate the total size, file count, and last modified date of a folder."""
    total_size = 0
    file_count = 0
    last_modified = 0
    
    for dirpath, _, filenames in os.walk(folder_path):
        file_count += len(filenames)
        for f in filenames:
            fp = os.path.join(dirpath, f)
            if os.path.exists(fp):
                total_size += os.path.getsize(fp)
        # Update the last modified date of the folder (most recent file modification)
        last_modified = max(last_modified, os.path.getmtime(dirpath))
    
    # Convert to MB
    total_size_MB = total_size / (1024 * 1024)  # Convert bytes to MB
    
    # Convert last modified time to a readable format
    last_modified_date = datetime.fromtimestamp(last_modified).strftime('%Y-%m-%d %H:%M:%S')
    
    return total_size_MB, file_count, last_modified_date
```
## Defining location of files, information and order
```r
# Define the drive path
drive_path = "C:/Frank"

# Get folder info
folder_info = []
for folder in os.listdir(drive_path):
    folder_path = os.path.join(drive_path, folder)
    if os.path.isdir(folder_path):
        size, file_count, last_modified = get_folder_info(folder_path)
        folder_info.append((folder, size, file_count, last_modified))

# Sort folders by size in descending order
folder_info.sort(key=lambda x: x[1], reverse=True)

# Create a DataFrame
df = pd.DataFrame(folder_info, columns=["Folder Name", "Size (MB)", "File Count", "Last Modified"])
```
## Display the DataFrame
```r
df
```
As we can see, we now have an initial view of the results in the report.

| Folder Name        | Size (MB)  | File Count | Last Modified          |
|--------------------|------------|------------|------------------------|
| Mercy_University   | 9981.57    | 18         | 2025-02-02 01:18:20    |
| Video Editor       | 3806.08    | 26         | 2025-02-05 22:29:10    |
| Anaconda_Install   | 963.60     | 6          | 2025-02-05 22:01:57    |
| Google             | 861.66     | 36         | 2025-02-05 01:39:46    |
| Books              | 851.55     | 148        | 2025-02-05 22:28:08    |
| Nitro              | 385.30     | 1          | 2025-02-04 01:16:35    |
| JoB                | 167.13     | 139        | 2025-02-08 18:21:54    |
| Waikitech          | 125.05     | 131        | 2025-02-05 22:29:30    |
| fotos              | 99.93      | 1384       | 2025-02-05 22:28:24    |
| VSCODE             | 98.69      | 2          | 2025-02-05 10:29:50    |
| Certificates       | 88.83      | 54         | 2025-02-05 13:22:25    |
| Project            | 43.16      | 54         | 2025-02-05 22:28:48    |
| C+                 | 22.10      | 9          | 2025-02-05 22:27:49    |
| Portfolio          | 18.60      | 120        | 2025-02-05 10:32:38    |
| Housing            | 4.77       | 12         | 2025-02-05 22:28:32    |
| NYSHM              | 2.08       | 5          | 2025-02-05 22:28:40    |
| CAP                | 2.06       | 2          | 2025-02-05 22:28:16    |
| TAX                | 0.32       | 1          | 2025-02-05 22:28:56    |

## Create Pdf File

Finally, we aligned the PDF design format with the results obtained.
```r
# Create PDF
pdf = FPDF()
pdf.set_auto_page_break(auto=True, margin=15)
pdf.add_page()
pdf.set_font("Arial", "B", 12)
pdf.cell(200, 10, "Folder Size Report - X Drive", ln=True, align="C")
pdf.ln(10)

# Table Headers
pdf.set_font("Arial", "B", 10)
pdf.cell(50, 10, "Folder Name", border=1)
pdf.cell(40, 10, "Size (MB)", border=1)
pdf.cell(40, 10, "File Count", border=1)
pdf.cell(50, 10, "Last Modified", border=1, ln=True)

# Table Content
pdf.set_font("Arial", "", 10)
for folder, size, file_count, last_modified in folder_info:
    pdf.cell(50, 10, folder, border=1)
    pdf.cell(40, 10, f"{size:.2f} MB", border=1)
    pdf.cell(40, 10, str(file_count), border=1)
    pdf.cell(50, 10, last_modified, border=1, ln=True)

# Save PDF
pdf.output("Folder_Size_Report.pdf")

print("PDF report generated: Folder_Size_Report.pdf")
```
As a result, we successfully generated a list that meets the objective of sorting files accurately, allowing for more efficient and streamlined archiving. 
![Alt text](https://github.com/FrankCoRa/Report_Generator/blob/main/Files_Report.png)

I hope this script helps automate the process of organizing files on your hard drive or within your file explorer, making report generation smoother and more efficient.



