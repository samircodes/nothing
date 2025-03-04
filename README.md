Sub UpdatePivotFromCSV()
    Dim wsSource As Worksheet
    Dim wsPivot1 As Worksheet
    Dim wsPivot2 As Worksheet
    Dim csvFile As String
    Dim lastRow As Long
    Dim lastCol As Long
    Dim dataRange As Range
    Dim qt As QueryTable
    Dim pt As PivotTable
    
    ' Set worksheet references
    Set wsSource = ThisWorkbook.Sheets("RPSOutput_YTD_Report_FID_Detail")
    Set wsPivot1 = ThisWorkbook.Sheets("RPS Calculations")
    Set wsPivot2 = ThisWorkbook.Sheets("RPS Calculations Manual")

    ' Prompt user to select a CSV file
    With Application.FileDialog(msoFileDialogFilePicker)
        .Title = "Select New CSV File"
        .Filters.Clear
        .Filters.Add "CSV Files", "*.csv"
        .AllowMultiSelect = False
        If .Show = -1 Then
            csvFile = .SelectedItems(1)
        Else
            Exit Sub ' If user cancels, exit macro
        End If
    End With

    ' Clear existing data in the source sheet
    wsSource.Cells.Clear

    ' Import CSV data into the source sheet
    Set qt = wsSource.QueryTables.Add(Connection:="TEXT;" & csvFile, Destination:=wsSource.Range("A1"))
    With qt
        .TextFileConsecutiveDelimiter = False
        .TextFileTabDelimiter = False
        .TextFileSemicolonDelimiter = False
        .TextFileCommaDelimiter = True
        .TextFileColumnDataTypes = Array(1) ' Treat all columns as text
        .Refresh
        .Delete ' Remove QueryTable after import
    End With

    ' Find the last row and column in the new data
    lastRow = wsSource.Cells(Rows.Count, 1).End(xlUp).Row
    lastCol = wsSource.Cells(1, Columns.Count).End(xlToLeft).Column

    ' Define the new data range
    Set dataRange = wsSource.Range(wsSource.Cells(1, 1), wsSource.Cells(lastRow, lastCol))

    ' Update PivotTables in "RPS Calculations"
    For Each pt In wsPivot1.PivotTables
        pt.ChangePivotCache ThisWorkbook.PivotCaches.Create(SourceType:=xlDatabase, SourceData:=dataRange)
        pt.RefreshTable
    Next pt

    ' Update PivotTables in "RPS Calculations Manual"
    For Each pt In wsPivot2.PivotTables
        pt.ChangePivotCache ThisWorkbook.PivotCaches.Create(SourceType:=xlDatabase, SourceData:=dataRange)
        pt.RefreshTable
    Next pt

    ' Notify user
    MsgBox "Pivot tables updated successfully!", vbInformation, "Success"

End Sub


import pandas as pd
from openpyxl import load_workbook

# File paths
file_path = "your_file.xlsx"
new_file_path = "new_file.xlsx"  # Temporary file
sheet_name = "YTD report"

# Your DataFrame (replace with actual data)
df = pd.DataFrame({...})  

# Step 1: Write the new "YTD report" sheet quickly using XlsxWriter
df.to_excel(new_file_path, sheet_name=sheet_name, index=False, engine="xlsxwriter")

# Step 2: Load both workbooks
wb_old = load_workbook(file_path)
wb_new = load_workbook(new_file_path)

# Copy all sheets from the old file except "YTD report"
for sheet in wb_old.sheetnames:
    if sheet != sheet_name:  # Skip the "YTD report" sheet
        ws_old = wb_old[sheet]
        ws_new.create_sheet(sheet)
        ws_new_ws = wb_new[sheet]
        
        for row in ws_old.iter_rows(values_only=True):
            ws_new_ws.append(row)

# Step 3: Save the final file (overwrite the original)
wb_new.save(file_path)

print(f"Sheet '{sheet_name}' updated successfully while keeping other sheets.")

