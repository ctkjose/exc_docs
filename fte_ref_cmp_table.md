[Related]() {:menu}
[Components](./fte_ref_components.md) {:menu}
[Documentation](./doc_index.md) {:menu}
[Front End]() {:menu}
[Documentation](./doc_index.md) {:menu}


**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Component Table #

```HTML
<w type="table" name = "table1" issearchable=1 enumerateRows=1>
	{
		"columns": [
			{"name": "fname", "caption":"First Name"},
			{"name": "lname", "caption":"Last Name"},
			{"name": "email", "caption":"email", "editable":true},
			{"name": "salary", "caption":"Salary", "format":"2", "sortable": true, "tally":true, "lookupCSS":[{"className":"high-pay", "when":"%salary%>=3500"},{"className":"low-pay", "when":"%salary%<3500"}] },
			{"name": "type", "caption":"Type","lookup":{"P":"Permanent","T":"Temporary","C":"Contract"} },
			{"name": "active", "caption":"Active" },
			{"name": "hiredate", "caption":"Hired" }
		],
		"rowActions": [
			{"caption":"View...", "help":"View record...", "condition":"true", "type":"publish", "msg":"table1RowView", "params":{}},
			{"caption":"Remove", "condition":"true", "type":"publish", "msg":"table1RowRemove", "params":{}},
			{"caption":"Go To", "condition":"true", "help":"Navigate to page", "type":"navigate", "url":"index.html", "params":{"a":"doThis","n":"%fname%"}}
		],
		"actions":[
			{"caption":"Add new record...", "help":"New record record...", "condition":"true", "type":"publish", "msg":"table1AddRow", "params":{}}
		],
		"widgets":{
			"active": {"type": "toggle", "name":"active"},
			"hiredate": {"type": "date", "name":"hiredate", "classes":["style-embedded", "style-with-border"]}
		}
	}
</w>
```

## Properties ## {.class2 #properties .class1}

Properties for the `<w></w>` tag.

| Property | Description |
| -- | -- |
| type | **Required** Value is "table" |
| name | **Required** A name for this widget. |
| enumerateRows | Adds a column with row numbers. Value: 1 or 0 |
| issearchable | Makes a table searchable and adds a search box to the table header. Value: 1 or 0 |


## Column definitions ## {#columns}

The table takes a JSON object on the body of the `<w></w>` with information to define the columns.

A column is declared in the `"columns"` key of the JSON object. A column has the following keys:

| Key | Description |
| -- | -- |
| name | **Required** A string with a "variable" friendly name for the column. It is used for data binding and other operations. |
| caption | **Required** A string with the column header. |
| editable | Makes a column cell editable. Only applies for basic text/numeric columns without formatting. |
| format | Sets a numeric format parameters. The value is a pipe `"&vert;"` delimited string with the amount of decimal places, the character used for decimal point, and the character for thousands separator. Examples: `"&#124;2&#124;.&#124;,"` or `"2"`. |
| sortable | A boolean indicating if this column is a sort key. |
| tally | A boolean indicating that a tally of the column values will be added and show as a footer. |
| hideonsize | An array of strings. Each entry with the code of a layout size that will hide this column.  For example: `["S","M"]`. |
| lookupCSS | An array of plain objects with conditional rules to apply a css class to a column cell.<br><br>An entry in this array has a `"className"` with the css class to be applied and a `"when"` key with an expression to evaluate.<br><br>For example:<br> `"%salary%>=3500"` or `"%salary%<3500"`.<br><br>The expressions use the column names as variables for example `%salary%` is the value of the column "salary". |
| lookup | A plain object with key-value pairs of captions for a given code (key). When the value of a cell is matched to a key in this object the caption of the key is displayed. For example<br> `{"P":"Permanent","T":"Temporary","C":"Contract"}` |

A column may be editable by using widgets. The `"widgets"` key defines a widget for a given column. This widgets key is plain object made up of key value pairs. The key of an entry is the column name. The entry value is a plain object that describes the widget to use.



```js
"active": {"type": "toggle", "name":"active"},
```


### Methods ###

This component inherits methods from `generic`.

| Function | Syntax | Description |
| -- | -- | -- |
| getData | `getData()` | Returns the table associated data. |
| rowSetDataForIndex | `rowSetDataForIndex(rowIdx, data)` | The parameter data is a plain object. |
| refreshRow | `refreshRow(rowIdx)` |  |
| rowAdd | `rowAdd(data)` | The parameter data is a plain object. |
| sortColumn | `sortColumn(thNode)` |  |
| updateTallies | `updateTallies()` |  |




```js
myTable = $.component("table1");

myTable.rowAdd({"fname":"Jose","lname":"Cuevas-1","email":"jcuevas@mac.com", "salary":1250, "type":"P", "active":0,"hiredate":"2/15/2019"});
```

```js
myRecord = {"fname":"Joe","lname":"Cuevas-3","email":"info@expworks.io", "salary":3250.2, "type":"T","active":1,"hiredate":"2/18/2019"};
myRecordUID = myTable.rowAdd(myRecord);
```


```js
myTable.rowSetDataForIndex( 1, {"email":"jcuevas50@ma.com", "active": 1, "type": "T"});
```
