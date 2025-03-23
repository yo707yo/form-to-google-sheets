# Leaders Daily Reporting Form

A customized HTML form that submits daily reports directly to a Google Sheets spreadsheet. This form is specifically designed to work with the [Leaders Daily Reporting spreadsheet](https://docs.google.com/spreadsheets/d/e/2PACX-1vRjk7xhJz5C4ThbwxokScyO9QTxbPrYDuXx7WA10PAn5qcFMQQeG-VergRjNFMBXNjqxp4OTG_Wki8b/pubhtml).

## Form Features

- Region selection dropdown with options for all regions
- Dynamic name dropdown that updates based on the selected region
- Subject dropdown with predefined options
- Tasks/Actions Completed textarea
- Status dropdown with color-coded options
- Time Spent text entry field
- Start and End Date fields with popup calendars
- Additional Suggestions and Notes textarea
- Loading indicator during submission
- Success and error messages
- Responsive design that works on all devices, including mobile phones

## Region-Specific Names

The form includes a dynamic dropdown for names that changes based on the selected region:

- **Founders**: Danny, Ivan
- **App**: Danny
- **LA/OC**: Kevin N, Bernadette L, Drew W
- **Bay Area**: Malek, Patrick, Chase
- **SDDU**: Luis A, Lee S, Livrado R
- **Sacramento**: (No specific names, shows N/A option)
- **Fresno**: Eustace, Asad, Jose
- **Redding**: Kevin S, Frank H, Kat P
- **Palm Springs**: Kerwin Q, Greg K, Judy P

## Subject Options

The form includes a dropdown for subjects with the following options:

- General
- Calls
- Emails
- SMS
- Document Approval
- Order Supplies
- App Ideas
- To Do
- General Suggestions
- GoHighLevel Maintenance

## Status Options

The form includes a dropdown for status with the following options:

- Not Started
- In Progress (displayed in green and italic)
- Skipped
- Done

## Date Fields

The form includes two date fields with popup calendars:

- Start Date: Defaults to today's date as the minimum selectable date
- End Date: Automatically sets its minimum date to match the selected Start Date

## Mobile Optimization

The form is fully optimized for mobile devices:

- Responsive layout that adapts to screen size
- Properly sized input fields for touch interaction
- Date fields that work with mobile calendar pickers
- Optimized spacing and typography for small screens

## Setup Instructions

Follow these steps to connect the form to your Google Spreadsheet:

### 1. Set up Google Apps Script

1. Open your [Leaders Daily Reporting spreadsheet](https://docs.google.com/spreadsheets/d/e/2PACX-1vRjk7xhJz5C4ThbwxokScyO9QTxbPrYDuXx7WA10PAn5qcFMQQeG-VergRjNFMBXNjqxp4OTG_Wki8b/pubhtml) in Google Sheets (not the published HTML view)
2. Click on `Extensions > Apps Script` which will open a new tab
3. Delete any code in the editor and paste the following:

```javascript
var sheetName = 'Sheet1' // Change this if your data is on a different sheet
var scriptProp = PropertiesService.getScriptProperties()

function intialSetup () {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet()
  scriptProp.setProperty('key', activeSpreadsheet.getId())
}

function doPost (e) {
  var lock = LockService.getScriptLock()
  lock.tryLock(10000)

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'))
    var sheet = doc.getSheetByName(sheetName)

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0]
    var nextRow = sheet.getLastRow() + 1

    var newRow = headers.map(function(header) {
      return header === 'Timestamp' ? new Date() : e.parameter[header]
    })

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow])

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON)
  }

  catch (e) {
    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'error', 'error': e }))
      .setMimeType(ContentService.MimeType.JSON)
  }

  finally {
    lock.releaseLock()
  }
}
```

4. Click `File > Save` and give your script a name (e.g., "Leaders Daily Reporting Form")

### 2. Run the setup function

1. In the Apps Script editor, select the `intialSetup` function from the dropdown menu next to the "Run" button
2. Click the "Run" button
3. When prompted, click "Review Permissions" and then "Allow" to grant the necessary permissions

### 3. Deploy as web app

1. Click on `Deploy > New deployment`
2. Click the gear icon next to "Select type" and choose "Web app"
3. Fill in the following:
   - Description: "Leaders Daily Reporting Form"
   - Execute as: "Me"
   - Who has access: "Anyone"
4. Click "Deploy"
5. Copy the Web app URL that appears in the deployment confirmation

### 4. Update the form with your script URL

1. Open the `index.html` file in a text editor
2. Find the line that says `const scriptURL = '<SCRIPT URL>'` (around line 290)
3. Replace `<SCRIPT URL>` with the Web app URL you copied in the previous step
4. Save the file

### 5. Update your Google Sheet headers

Make sure your Google Sheet has the following column headers (in any order):

- Timestamp
- Your local Co-op?
- Select Name
- Subject
- Tasks/Actions Completed
- Additional Suggestions and Notes
- Status
- Time Spent
- Start Date
- End Date

## Using the Form

1. Open the `index.html` file in a web browser
2. Select a region from the dropdown
3. Select a name from the dynamically populated dropdown
4. Select a subject from the dropdown
5. Fill in the tasks/actions completed
6. Enter any additional suggestions and notes
7. Select a status (notice how "In Progress" appears in green and italic)
8. Enter the time spent
9. Select start and end dates using the calendar pickers
10. Click "Submit Report"
11. You should see a success message if the submission was successful
12. Check your Google Spreadsheet to verify the data was added

## Troubleshooting

- If you see an error message when submitting the form, check that your script URL is correct
- Make sure the column headers in your spreadsheet exactly match the `name` attributes in the form
- If you're still having issues, check the browser console for more detailed error messages

## Customizing the Form

You can customize the form by editing the `index.html` file:

- Change the form title and description
- Modify the region options or name lists in the JavaScript section
- Update the styling by editing the CSS variables at the top of the file
- Add or remove form fields as needed
