Tags: #Javascript 

---

### Create & Download a excel(.xlsx) file using javascript.

1. Prepare your data in an array.

```javascript
// Some dummy data for user details.
let data = [
    [ 'Id', 'FirstName', 'LastName', 'Mobile', 'Address' ], // This is your header.
    [ 1, 'Richard', 'Roe', '9874563210', 'Address' ],
    [ 2, 'Joe', 'Doakes', '7896541230', 'Address' ],
    [ 3, 'John', 'Smith', '8745632109', 'Address' ],
    [ 4, 'Joe', 'Sixpack', '9875647890', 'Address' ],
    [ 5, 'Richard', 'Thomson', '8632547890', 'Address' ]
];
```

2. Prepare data for excel.

```javascript
let excelData = '';

// Prepare data for excel.You can also use html tag for create table for excel.
data.forEach(( rowItem, rowIndex ) => {   
    
    if (0 === rowIndex) {
        // This is for header.
    rowItem.forEach((colItem, colIndex) => {
         excelData += colItem + ',';
    });
    excelData += "\r\n";

     } else {
        // This is data.
        rowItem.forEach((colItem, colIndex) => {
       excelData += colItem + ',';   
    })
       excelData += "\r\n";       
     }
});
```

3. We have completed the preparing data. Now move towards creating a blob URL and download file.

```javascript
// Create the blob url for the file. 
excelData = "data:text/xlsx," + encodeURI(excelData);

// Download the xlsx file.
let a = document.createElement("A");
a.setAttribute("href", excelData);
a.setAttribute("download", "filename.xlsx");
document.body.appendChild(a);
a.click();
```

### Create & Download a html(.html) file using javascript.

1. Prepare data for HTML

```javascript
// I am creating table but you can create any design as per your need.

let htmlData = `<table align="center" border="1">`;
    
data.forEach(( rowItem, rowIndex ) => {
   if (0 === rowIndex) {
       htmlData += `<tr>`;
       rowItem.forEach(( colItem, colIndex ) => {
          htmlData += `<th>${colItem}</th>`;
       });
       htmlData += `</tr>`;
   } else {
       htmlData += `<tr>`;
       rowItem.forEach(( colItem, colIndex ) => {
            htmlData += `<td>${colItem}</td>`;
       });
       htmlData += `</tr>`;
    }
});
```

2. We have completed the preparing data for HTML. Now move towards creating a blob URL and download the file.

```javascript
// Create the blob url for the file.
htmlData = "data:text/html," + encodeURI(htmlData);

// Download the html file.
let a = document.createElement("A");
a.setAttribute("href", htmlData);
a.setAttribute("download", "filename.html");
document.body.appendChild(a);
a.click();
```

### Create & Download a text(.txt) file using javascript.

```javascript
let text = 'You text goes here';

// Create the blob url for the file.
text = "data:text/plain," + encodeURI(text);

// Download the txt file.
let a = document.createElement("A");
a.setAttribute("href", text);
a.setAttribute("download", "filename.txt");
document.body.appendChild(a);
a.click();
```