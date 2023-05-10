程式碼
```js
function getMainList() {
    if ($.fn.dataTable.isDataTable('#mainTable')) { // $.fn.dataTable.tables() 獲取該頁面上所有的DataTables物件
        $('#mainTable').DataTable().destroy();
        $('#mainTable').empty();
    }
    mainTable = $('#mainTable').on('processing.dt', function(e, settings, processing) {
        if (processing) { // 自訂處理中遮罩
            showLoading();
        } else {
            $.unblockUI();
        }
    }).DataTable({
        dom: 'lBfrtip', // 布局
        searching: false, // 快篩搜尋
        processing: false, // 處理中指示
        serverSide: true, // 後端分頁
        destroy: true, // 已存在時，銷毀舊的
        retrieve: true, // 重載
        deferRender: true, // 分批渲染
        fixedHeader: { // 凍結表頭
            headerOffset: 50
        },
        lengthMenu: [10, 25, 50, 100], // 每頁幾筆
        language: { // 語系
            url: "/scripts/DataTables-1.12.1/zh-HANT.json",
            infoFiltered: "",
        },
        responsive: { // RWD寬度不足時改成點擊展開modal詳情
            details: {
                display: $.fn.dataTable.Responsive.display.modal({
                    header: function(row) {
                        var data = row.data();
                        return '<spring:message code="jsp.dr0201.6" />'; // 詳細資訊
                    }
                }),
                renderer: $.fn.dataTable.Responsive.renderer.tableAll({
                    tableClass: 'table'
                })
            }
        },
        buttons: [{
            extend: 'excel',
            text: '<i class="fa-solid fa-file-export fa-xl"></i> 匯出此頁',
            exportOptions: {
                modifier: {
                    page: 'current'
                }
            }
        }, {
            text: '<i class="fa-solid fa-magnifying-glass fa-xl"></i> 篩選',
            action: function(e, dt, node, config) {
                $('#rwdSearchFilter').slideToggle('fast');
            }
        }],
        order: [
            //[0, 'asc']
        ],
        orderMulti: false, // 是否按住shift點表頭時可以多重排序
        ajax: {
            type: "post",
            url: ems.parseUrl("file") + "/getMainList",
            contentType: "application/json",
            dataType: "json",
            data: function(data) {
                let orderBy = "";
                let ascend = "";
                // 獲取當前的排序
                if (data.order[0]){
                    orderBy = data.columns[data.order[0].column].data;
                    ascend = data.order[0].dir == "asc";
                }
                var d = {
                    "orderNumber": $("#sf-orderNumber").val().trim(),
                    "orderType": "0",
                    "hostId" : $("#sf-hostId").val(),
                    "createTimeFrom": $("#sf-createTime-min").val(),
                    "createTimeTo" : $("#sf-createTime-max").val(),
                    "departureTimeFrom" : $("#sf-departureTime-min").val(),
                    "departureTimeTo" : $("#sf-departureTime-max").val(),
                    "bindOrderNumber": $("#sf-bindOrderNumber").val().trim(),
                    "manufacturer": $("#sf-manufacturer").val(),
                    "isFinish": $("#sf-isFinish").val() == '' ? null : $("#sf-isFinish").val(),
                    // "state": $("#sf-state").val(),
                    "currentPage": (data.start / data.length) + 1,
                    "pageSize": data.length,
                    "orderBy": orderBy, // 排序的欄位
                    "ascend": ascend
                }

                return JSON.stringify(d);
            },
            error: function(jqXHR, ajaxOptions, thrownError) {
                console.log("獲取清單失敗");
            },
            dataSrc: function(json) {
                // console.log(json)
                if (json.status == "SUCCESS") {
                    json.recordsFiltered = json.result.count; // 指定記錄數
                    json.recordsTotal = json.result.totalPage; // 指定頁數
                    return json.result.list;
                }
                return "";
            },
        },
        columns: [
            // 思納捷單
            {
                width: "2%",
                data: null,
                defaultContent: '',
                orderable: false,
                render: function(data, type, full, meta) {
                    return meta.row + 1 + meta.settings._iDisplayStart;
                }
            },
            {
                width: "2%",
                data: "createTime",
                render: function(data) {
                    return data ? dayjs(data).format('YYYY-MM-DD') : "";
                }
            },
            {
                data: "orderNumber",
                render: function(data, type, row) {
                    return '<span class="link" '
                        + ' onclick="gotoDetailPage(' + row.id + ')">' + data + '</span>';
                }
            },
            {data: "creator"},
            {data: "projectName"},
            {
                data: "totalCount",
                orderable: false,
            },
            {
                data: "fixCount",
                orderable: false,
            },
            {
                data: "noFixCount",
                orderable: false,
            },
            {
                data: "departureTime",
                render: function(data) {
                    return data ? dayjs(data).format('YYYY-MM-DD') : "";
                }
            },
            // 原廠單
            {
                data: "bindOrderNumber",
                render: function(data, type, row) {
                    if (data == null) return "";
                    return '<span class="link" '
                        + ' onclick="gotoDetailPage(' + row.bindOrderId + ')">' + data + '</span>';
                }
            },
            {data: "manufacturer"},
            {data: "contact"},
            {data: "contactNumber"},
            {
                data: "manuTotalCount",
                orderable: false
            },
            {
                data: "manuFixCount",
                orderable: false,
            },
            {
                data: "manuNoFixCount",
                orderable: false,
            },
            {
                data: "manuAbnormalCount",
                orderable: false,
            },
            {
                data: "receiveTime",
                orderable: false,
                render: function(data) {
                    return data ? dayjs(data).format('YYYY-MM-DD') : "";
                }
            },
            // 檢驗
            {
                data: "testTotalCount",
                orderable: false,
            },
            {
                data: "isFinish",
                orderable: false,
                render: function(data) {
                    return data == "0" ? "未結單": "結單";
                }
            },
            {data: "note"}
        ],
        initComplete: function(settings, json) {},
        drawCallback: function(setting) {   // 每次繪製圖表結束時，會call
            // 會出現兩次的原因是: 第一次在no data且顯示Loading訊息的時候，第二次是在加載data後再call一次。

            // 跳轉到輸入框
            var _this = $(this);
            var tableId = _this.attr('id');
            var pageDiv = $('#' + tableId + '_paginate');
            let str = '<input id="' + tableId + '_gotoPage" type="text"/>' +
                '<a class="paginate_button" aria-controls="' + tableId + '" tabindex="0" id="' + tableId +
                '_goto">Go</a>';
            pageDiv.append(str);
            $('#' + tableId + '_goto').click(function(obj) {
                var page = $('#' + tableId + '_gotoPage').val();
                var thisDataTable = $('#' + tableId).DataTable();
                var pageInfo = thisDataTable.page.info();
                if (isNaN(page)) {
                    $('#' + tableId + '_gotoPage').val('');
                    return;
                } else {
                    var maxPage = pageInfo.pages;
                    var page = Number(page) - 1;
                    if (page < 0) {
                        page = 0;
                    } else if (page >= maxPage) {
                        page = maxPage - 1;
                    }
                    $('#' + tableId + '_gotoPage').val(page + 1);
                    thisDataTable.page(page).draw('page');
                }
            })
            // 將最上層header設為響應式
            headerAdjust();
        },
    });
}
```

<br/>

<br/>

## 處理 Multiple Header 響應式顯示問題
若 datatable 的 column 過長，會導致第一層 header 在顯示上會有不能隱藏的問題

```html
<table id="mainTable">
    <thead>
        <tr id="firstHeader">
            <th rowspan="2">序號</th>
            <th rowspan="2">產品名稱</th>
            <th colspan="3">0-9歲</th>
            <th colspan="3">10-19歲</th>
            <th colspan="3">20-29歲</th>
            <th colspan="3">30-39歲</th>
            <th colspan="3">40-49歲</th>
            <th colspan="3">50-59歲</th>
            <th colspan="3">60-69歲</th>
            <th colspan="3">70歲以上</th>
            <th rowspan="2">銷售總數</th>
        </tr>
        <tr id="secondHeader">
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
            <th>男</th><th>女</th><th>小計</th>
        </tr>
    </thead>
</table>
```




js 程式碼，`markHeader()` DOM載入完成後就可以呼叫；`headerAdjust()`可以在 datatable.drawCallback() 中呼叫。
```html
<script type="text/javascript">
/**
 * 標記 header
 */
function markHeader() {
    $('#firstHeader > th[colspan=3]').addClass('first-header'); // 標記第一層header
    $('#secondHeader > th').addClass('second-header');    // 標記第二層header
}

/**
 * header加上resize事件
 */
function headerAdjust() {
    // 載入後判斷一次
    $(document).ready(function() {
        firstHeaderAdjust();
    })

    // 綁定resize事件
    $(window).resize(function() {
        firstHeaderAdjust();
    })
}

/**
 * 修正第一層header顯示問題
 */
function firstHeaderAdjust() {
    var firstHeaderNum = $('.first-header').length; // 8
    var secondHeaderVisibleNum = $('.second-header:visible').length; // 顯示的第二層header數量
    var firstHeadershowNum = Math.ceil(secondHeaderVisibleNum / 3); // 第一層header該顯示幾欄

    $('.first-header').show();

    for (var i = firstHeadershowNum; i < firstHeaderNum; i++) {     // 第一層header共8欄，從0~7
        $('#firstHeader > th:nth-child(' + (i+3) + ')' ).hide();    // +3表示第二層header從第3欄開始
    }
}
</script>
```