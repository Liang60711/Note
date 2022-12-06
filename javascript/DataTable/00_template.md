程式碼
```js
function getMainList() {
    if ($.fn.dataTable.isDataTable('#mainTable')) {
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
        drawCallback: function(setting) {
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