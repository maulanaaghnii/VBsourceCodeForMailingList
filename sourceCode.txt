Option Explicit

Const FOLDER_SAVED As String = "D:\BEM KM\KABINET TUNAS EMAS\Surat Keaktifan\Pemira\Output\20210610 - "
Const SOURCE_FILE_PATH As String = "D:\BEM KM\KABINET TUNAS EMAS\Surat Keaktifan\Pemira\DataCapres.xlsx"


Sub MailMergeToIndPDF()
Dim MainDoc As Document, TargetDoc As Document
Dim dbPath As String
Dim recordNumber As Long, totalRecord As Long

Set MainDoc = ActiveDocument
With MainDoc.MailMerge
    
        '// if you want to specify your data, insert a WHERE clause in the SQL statement
        .OpenDataSource Name:=SOURCE_FILE_PATH, sqlstatement:="SELECT * FROM [keaktifan$]"
            
        totalRecord = .DataSource.RecordCount

        For recordNumber = 1 To totalRecord
        
            With .DataSource
                .ActiveRecord = recordNumber
                .FirstRecord = recordNumber
                .LastRecord = recordNumber
            End With
            
            .Destination = wdSendToNewDocument
            .Execute False
            
            Set TargetDoc = ActiveDocument

            TargetDoc.SaveAs2 FOLDER_SAVED & "0" & .DataSource.DataFields("no_surat").Value & " " & .DataSource.DataFields("nama").Value & " - Pemira" & ".docx", wdFormatDocumentDefault
            TargetDoc.ExportAsFixedFormat FOLDER_SAVED & "0" & .DataSource.DataFields("no_surat").Value & " " & .DataSource.DataFields("nama").Value & " - Pemira" & ".pdf", exportformat:=wdExportFormatPDF
            TargetDoc.Close False
            Set TargetDoc = Nothing
        Next recordNumber
End With
    On Error Resume Next
    Kill FOLDER_SAVED & "*.docx"
    On Error GoTo 0
Set MainDoc = Nothing
End Sub
'adopted from https://learndataanalysis.org/automate-mail-merge-to-save-each-record-individually-with-word-vba/
'with additional delete docx file


