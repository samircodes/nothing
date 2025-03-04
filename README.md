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

