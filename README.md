Sub UpdatePivotSource()
    Dim wsSource As Worksheet
    Dim wsPivot1 As Worksheet
    Dim wsPivot2 As Worksheet
    Dim newFile As String
    Dim newBook As Workbook
    Dim lastRow As Long
    Dim lastCol As Long
    Dim dataRange As Range
    Dim pt As PivotTable

    ' Set worksheet references
    Set wsPivot1 = ThisWorkbook.Sheets("RPS Calculations")
    Set wsPivot2 = ThisWorkbook.Sheets("RPS Calculations Manual")

    ' Prompt user to select a new source file
    With Application.FileDialog(msoFileDialogFilePicker)
        .Title = "Select New Source File"
        .Filters.Clear
        .Filters.Add "Excel Files", "*.xlsx;*.xlsm;*.xls"
        .AllowMultiSelect = False
        If .Show = -1 Then
            newFile = .SelectedItems(1)
        Else
            Exit Sub ' If user cancels, exit macro
        End If
    End With

    ' Open the new workbook
    Application.ScreenUpdating = False
    Set newBook = Workbooks.Open(newFile)

    ' Set the new source sheet
    On Error Resume Next
    Set wsSource = newBook.Sheets("RPSOutput_YTD_Report_FID_Detail")
    If wsSource Is Nothing Then
        MsgBox "Sheet 'RPSOutput_YTD_Report_FID_Detail' not found in the selected file.", vbExclamation, "Error"
        newBook.Close False
        Exit Sub
    End If
    On Error GoTo 0

    ' Find the last row and column in the new source file
    lastRow = wsSource.Cells(Rows.Count, 1).End(xlUp).Row
    lastCol = wsSource.Cells(1, Columns.Count).End(xlToLeft).Column

    ' Define the data range
    Set dataRange = wsSource.Range(wsSource.Cells(1, 1), wsSource.Cells(lastRow, lastCol))

    ' Set the new data source for PivotTables in both sheets
    For Each pt In wsPivot1.PivotTables
        pt.ChangePivotCache ThisWorkbook.PivotCaches.Create(SourceType:=xlDatabase, SourceData:=dataRange)
        pt.RefreshTable
    Next pt

    For Each pt In wsPivot2.PivotTables
        pt.ChangePivotCache ThisWorkbook.PivotCaches.Create(SourceType:=xlDatabase, SourceData:=dataRange)
        pt.RefreshTable
    Next pt

    ' Close the new source file
    newBook.Close False

    ' Turn screen updating back on
    Application.ScreenUpdating = True

    MsgBox "Pivot tables updated successfully!", vbInformation, "Success"

End Sub

