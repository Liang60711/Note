## Export excel 需要引入幾個 js 檔案
下載 DataTable 套件包時，需要包含 `Buttons` 組件。
```html
<!-- datatable with DOM buttons -->
<script type="text/javascript" charset="utf8" src="../../datatables.min.js"></script>
<!-- excel -->
<script type="text/javascript" charset="utf8" src="../../dataTables.buttons.min.js"></script>
<script type="text/javascript" charset="utf8" src="../../jszip.min.js"></script>
```


程式碼，若遇到datatable中的欄位資料是`select:option`時。
```js
function initDataTable() {
	table =  $('#dataListTable').DataTable({
		searching: false,
		dom: '<l<"pageDetails"i>>Btp',
		fnDrawCallback: function(oSettings) {   // 舊版的drawCallback，兩者相同
			if (oSettings.fnRecordsDisplay() <= 0) {
				$('#dataListTable_paginate').hide();
			}else{
				$('#dataListTable_paginate').show();
			}
		},
		buttons: [
			{
				extend: 'excel',
				text: '匯出Excel',
				exportOptions: {
					columns: [1,2,3,4,5,6,7],   // 需要保留的欄位，從0開始
					modifier: {
						page: 'current'         // 只輸出此頁
					},
					format: {   // 修改資料
						body: function (data, row, column, node) {
							if (column == 6) {	// 這裡的column是計算已經篩選過的，即原第7欄 (上面columns: [1,2,3,4,5,6,7])
								return $(data).find("option:selected").text();
							} else {
								return data.replace('&amp;','&');
							}
						}
					}
				}
			}
		],
		columnDefs: [
			{
				'targets': 0,
				'render': function (data, type, full, meta){    // 可以額外加上欄位定義
					return '<input type="checkbox" class="checkDataTableIds" name="checkDataTableIds" value="' + data + '" />';
				}
			},
			{
				'searchable': false,
				'orderable': false,
				'targets': [0, 7, 8],
			},
			{
				'type': 'natural',  // type可以設定為html, string, num等等
				'targets': 2
			}
		],
		order: [[6, 'desc' ]],	// 依照發生時間DESC排序
		initComplete: function(settings, json) {},
		drawCallback: function(settings) {}
	});
}

```

columns.type 文件
> https://datatables.net/reference/option/columns.type