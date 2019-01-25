---
layout: default
---

# Dashboard

## Partners

<table id="partners-table">
  <tr>
    <th>
      Organisation
    </th>
    <th>
      Easy access to information
    </th>
    <th>
      Meaningful communication with other partners
    </th>
    <th>
      Know what to do
    </th>
    <th>
      Making progress
    </th>
    <th>
      Creating value
    </th>
    <th>
      Updated
    </th>
  </tr>
</table>

## Working groups
For an overview of the active working groups, please see [Overview of active working groups](https://docs.google.com/spreadsheets/d/1moOu1tkTcBKBrCvEdgtAMQ9EAZpOS0aAfN2g9Qkxvls/edit?usp=sharing)


## Deliverables

<table id="deliverables-table">
  <tr>
    <th>
      Partner
    </th>
    <th>
      Code
    </th>
    <th>
      Deliverable
    </th>
    <th>
      Status
    </th>
    <th>
      Last updated
    </th>
    <th>
      Deadline
    </th>

  </tr>
</table>

<style>
  #partners-table td[data-value="yes"] {
    background-color: green;
  }
  #partners-table td[data-value="no"] {
    background-color: orange;
  }

  td[data-header="timestamp"] {
    opacity: 0.5;
  }
</style>

<script language="javascript">
  // The URL to the shared Google Sheet that generates a CSV
  var CSVURL = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vSzI-71Vpm9GyNXHmqwWsPi8Ps3h4d-EgBLiZ-H3IMtSM3LIZqSIKZQldUlp0GNEB3nVNJMLOypweyR/pub?gid=1227236263&single=true&output=csv'

  // Function to retrieve the CSV from the server
  function getCSV(url, callback) {
    var xhr = new XMLHttpRequest()
    var allText;

    xhr.open("GET", url, false);
    xhr.onreadystatechange = function () {
        if(xhr.readyState === 4)
            if(xhr.status === 200 || xhr.status == 0)
                callback(xhr.responseText)
    }

    xhr.send()
  }

  // Parse the CSV into an object
  function parseCSV(csv, callback) {
    var rows = []
    
    // Trim unnecessary quotation marks
    csv.replace('"', '')
    
    // Split the CSV at every newline
    var csvArray = csv.split('\n')

    // Take the first string in the array, make it into a new array by splitting it at the commas 
    var headers = csvArray[0].split(',')

    // Remove extra padding or spaces from the headers
    headers.forEach(function (header, index) {
      headers[index] = header.trim()
    })

    // Remove the headers from the CSV array
    csvArray = csvArray.splice(1)

    csvArray.forEach(function (string) {
      // Split every row at the commas
      var row = string.split(',')
      
      // Create an Object for each row
      var obj = {}
      
      // Add each existing value to the object
      row.forEach(function (item, index) {
        if (item != "" && item != undefined) {
          obj[headers[index]] = item
        }
      })

      // Add the Object to the rows array
      rows.push(obj)
    })

    callback(rows)
  }

  function getDeliverableStatuses (rows) {
    var el = document.getElementById('deliverables-table')

    rows.forEach(function (currentRow) {

      for (key in currentRow) {
        var value = currentRow[key]
        startsWithD = (/^D[1-9]/.test(key))

        if (value && startsWithD) {
          var rowEl = document.createElement("tr")

          var keyArray = key.split('	')
          var deliverable = {
            key: key,
            status: value,
            name: keyArray[1],
            code: keyArray[0],
            timestamp: currentRow['Timestamp'],
            organisation: currentRow['Your organisation']
          }
          var rowData = [deliverable.organisation, deliverable.code, deliverable.name, deliverable.status, deliverable.timestamp]

          rowData.forEach(function (currentData) {
            var cellEl = document.createElement("td")
            var contents = currentData.toString().trim()
            cellEl.setAttribute('data-value', contents.replace(/ /g, '-').toLowerCase())
            cellEl.innerText = contents
            rowEl.appendChild(cellEl)
          })

          el.appendChild(rowEl)

        }
      }
    })
  }

  // Generates the partner statusses table
  function getPartnerStatuses (rows) {
    var headers = ['Your organisation', 'We have easy access to the information we need', 'We have meaningful communication with other partners', 'We know what to do', 'We are making progress', 'We are creating value', 'Timestamp']

    var el = document.getElementById('partners-table')

    rows.forEach(function (currentRow) {
      var rowEl = document.createElement("tr")

      headers.forEach(function(currentHeader) {
        var cellEl = document.createElement("td")
        var contents = currentRow[currentHeader] || ""
        contents = contents.toString().trim()
        cellEl.innerText = contents
        cellEl.setAttribute('data-value', contents.replace(/ /g, '-').toLowerCase())
        cellEl.setAttribute('data-header', currentHeader.replace(/ /g, '-').toLowerCase())
        rowEl.appendChild(cellEl)
      })

      el.appendChild(rowEl)
    })

  }

  getCSV(
    CSVURL,
    function (response) { 
      parseCSV(
        response, 
        function (rows) {
          getPartnerStatuses(rows)
          getDeliverableStatuses(rows)
          window.v = rows
        }
      ) 
    })
</script>
