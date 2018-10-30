---
title: ngGrid
date: 2017-6-15
categroies: Angular组件
---

虽然ng-grid已经升级到了ui-grid，但是由于我们现在正在使用的是ng-grid，因此在这还是对ng-grid做一个总结吧。

ng-grid的使用很简单，在我看来，只需要按照官方提供的一copy就ok了。

# 基本的列表展示
- html

```html
<html ng-app="myApp">  
    <head lang="en">
        <meta charset="utf-8">
        <title>Getting Started With ngGrid Example</title>  
        <link rel="stylesheet" type="text/css" href="ng-grid.css" />
        <link rel="stylesheet" type="text/css" href="style.css" />
        <script type="text/javascript" src="jquery-1.8.2.js"></script>
        <script type="text/javascript" src="angular.js"></script>
        <script type="text/javascript" src="ng-grid-1.3.2.js"></script>
    </head>
    <body ng-controller="MyCtrl">
        <div class="gridStyle" ng-grid="gridOptions"></div>
    </body>
</html>
```

- js

```js
var app = angular.module('myApp', ['ngGrid']);
app.controller('MyCtrl', function($scope) {
    //myData一般为从后台获取的数据，数组格式
    $scope.myData = [{name: "Moroni", age: 50},
                     {name: "Tiancum", age: 43},
                     {name: "Jacob", age: 27},
                     {name: "Nephi", age: 29},
                     {name: "Enos", age: 34}];
    $scope.gridOptions = { data: 'myData' };
});
```

# 使用列配置

```html
<html ng-app="myApp">  
    <head lang="en">
        <meta charset="utf-8">
        <title>Getting Started With ngGrid Example</title>  
        <link rel="stylesheet" type="text/css" href="ng-grid.css" />
        <link rel="stylesheet" type="text/css" href="style.css" />
        <script type="text/javascript" src="jquery-1.8.2.js"></script>
        <script type="text/javascript" src="angular.js"></script>
        <script type="text/javascript" src="ng-grid-1.3.2.js"></script>
        <script type="text/javascript" src="main.js"></script>
    </head>
    <body ng-controller="MyCtrl">
        <div class="gridStyle" ng-grid="gridOptions"></div>
    </body>
</html>
<script type="text/javascript">
    var app = angular.module('myApp', ['ngGrid']);
    app.controller('MyCtrl', function($scope) {
        $scope.myData = [{name: "Moroni", age: 50},
                         {name: "Tiancum", age: 43},
                         {name: "Jacob", age: 27},
                         {name: "Nephi", age: 29},
                         {name: "Enos", age: 34}];
        $scope.gridOptions = { 
            data: 'myData',
            columnDefs: [{field:'name', displayName:'Name'}, {field:'age', displayName:'Age'}]
        };
    });
</script>>
```

# 分页

```html
<body ng-controller="MyCtrl">
        <div class="gridStyle" ng-grid="gridOptions"></div>
</body>
<script type="text/javascript">
    var app = angular.module('myApp', ['ngGrid']);
app.controller('MyCtrl', function($scope, $http) {
    $scope.filterOptions = {
        filterText: "",
        useExternalFilter: true
    }; 
    $scope.totalServerItems = 0;
    $scope.pagingOptions = {
        pageSizes: [250, 500, 1000],
        pageSize: 250,
        currentPage: 1
    };  
    $scope.setPagingData = function(data, page, pageSize){  
        var pagedData = data.slice((page - 1) * pageSize, page * pageSize);
        $scope.myData = pagedData;
        $scope.totalServerItems = data.length;
        if (!$scope.$$phase) {
            $scope.$apply();
        }
    };
    $scope.getPagedDataAsync = function (pageSize, page, searchText) {
        setTimeout(function () {
            var data;
            if (searchText) {
                var ft = searchText.toLowerCase();
                $http.get('jsonFiles/largeLoad.json').success(function (largeLoad) {        
                    data = largeLoad.filter(function(item) {
                        return JSON.stringify(item).toLowerCase().indexOf(ft) != -1;
                    });
                    $scope.setPagingData(data,page,pageSize);
                });            
            } else {
                $http.get('jsonFiles/largeLoad.json').success(function (largeLoad) {
                    $scope.setPagingData(largeLoad,page,pageSize);
                });
            }
        }, 100);
    };
    
    $scope.getPagedDataAsync($scope.pagingOptions.pageSize, $scope.pagingOptions.currentPage);
    
    $scope.$watch('pagingOptions', function (newVal, oldVal) {
        if (newVal !== oldVal && newVal.currentPage !== oldVal.currentPage) {
          $scope.getPagedDataAsync($scope.pagingOptions.pageSize, $scope.pagingOptions.currentPage, $scope.filterOptions.filterText);
        }
    }, true);
    $scope.$watch('filterOptions', function (newVal, oldVal) {
        if (newVal !== oldVal) {
          $scope.getPagedDataAsync($scope.pagingOptions.pageSize, $scope.pagingOptions.currentPage, $scope.filterOptions.filterText);
        }
    }, true);
    
    $scope.gridOptions = {
        data: 'myData',
        enablePaging: true,
        showFooter: true,
        totalServerItems: 'totalServerItems',
        pagingOptions: $scope.pagingOptions,
        filterOptions: $scope.filterOptions
    };
});
</script>
```

# ng-grid的常用配置API

|          属性名          |              默认值               |                    描述                    |
| :-------------------: | :----------------------------: | :--------------------------------------: |
|         data          |               []               |                用于展示表格的数据                 |
|    enableCellEdit     |             false              |                 单元格是否可编辑                 |
|     jqueryUITheme     |             false              |             是否使用juqeryUI的主题              |
|      columnDefs       |           undefined            |            对列进行自定义，不设置则按默认的展示            |
|  enableColumnResize   |             false              |                是否可调整列的大小                 |
|     nableSorting      |              true              |                  是否支持排序                  |
|    headerRowHeight    |               32               |              标题行的高度，以像素为单位               |
|   headerRowTemplate   |           undefined            |                  标题行的模板                  |
|         init          | `function(grid, $scope) { } `  |               表格初始化后即调用的函数               |
|       rowHeight       |               30               |                   行高度                    |
|      rowTemplate      |         rowTemplate见下          |                   行模板                    |
|    showColumnMenu     |             false              | 启用菜单以选择要显示和分组的列。 如果showColumnMenu和showFilter都为false，则菜单按钮将不显示。 |
|       sortInfo        | `{ fields: [], directions: [] } `|                 自定义排序对象                  |
|      multiSelect      |              true              |                是否可同时选中多行                 |
| enableCellEditOnFocus |                                |                 单击单元格聚焦                  |
- rowTemplate

```html
<div ng-style="{ 'cursor': row.cursor }" ng-repeat="col in renderedColumns" ng-class="col.colIndex()" class="ngCell {{col.cellClass}}"><div class="ngVerticalBar" ng-style="{height: rowHeight}" ng-class="{ ngVerticalBarVisible: !$last }">&nbsp;</div><div ng-cell></div></div>
```

**以下配置用于分页**

|      属性名      |                   默认值                    |                    描述                    |
| :-----------: | :--------------------------------------: | :--------------------------------------: |
| enablePaging  |                  false                   |                   是否分页                   |
| pagingOptions | `{ pageSizes: [250, 500, 1000], pageSize: 250, totalServerItems: 0, currentPage: 1 }` | 配置分页相关属性 pageSizes: 可选的每页展示数 <br> pageSize:当前显示的展示数量 <br> totalServerItems：数据的总条数 <br> currentPage: 当前的页数 |
| filterOptions | `{ filterText: '', useExternalFilter: false } ` |                    过滤                    |

**自定义列的配置**

|         属性名          |                   默认值                    |                    描述                    |
| :------------------: | :--------------------------------------: | :--------------------------------------: |
|        field         |                    `""`                    |            与myData数据中的属性进行关联             |
|     displayName      |                  field                   |           要显示的名称，默认是field设置的属性           |
|    enableCellEdit    |                  false                   |                 该列是否可编辑                  |
|      cellClass       |                    `''`                    |                 给列添加样式类                  |
|     cellTemplate     | cellTemplate见下 |   该列的模板    |
|        width         |                    `''`                    |                  设置列宽度                   |
|       maxWidth       |                   9000                   |                 设置列的最大宽度                 |
|       minWidth       |                    50                    |                 设置列的最小宽度                 |
| editableCellTemplate | `<input ng-class="'colt' + col.index" ng-input="COL_FIELD" /> ` | 设置列的编辑状态的模板，当用户使用enableFocusedCellEdit和单元格被聚焦时显示。 |
|  headerCellTemplate  |             headerTemplate见下             |               设置列标题单元格的模板                |
|       pinnable       |                   true                   |                列是否可以固定在左边                |
|      groupable       |                   true                   |               是否允许用户对列进行分组               |
|      resizable       |                   true                   |                列的大小是否可调整                 |
|       sortable       |                   true                   |                  列是否可排序                  |

- headerTemplate

```html
<div class="ngHeaderSortColumn {{col.headerClass}}" ng-style="{'cursor': col.cursor}" ng-class="{ 'ngSorted': !noSortVisible }"><div ng-click="col.sort($event)" ng-class="'colt' + col.index" class="ngHeaderText">{{col.displayName}}</div><div class="ngSortButtonDown" ng-show="col.showSortButtonDown()"></div><div class="ngSortButtonUp" ng-show="col.showSortButtonUp()"></div><div class="ngSortPriority">{{col.sortPriority}}</div><div ng-class="{ ngPinnedIcon: col.pinned, ngUnPinnedIcon: !col.pinned }" ng-click="togglePin(col)" ng-show="col.pinnable"></div></div><div ng-show="col.resizable" class="ngHeaderGrip" ng-click="col.gripClick($event)" ng-mousedown="col.gripOnMouseDown($event)"></div>
```

- cellTemplate

```html
<div class="ngCellText" ng-class="col.colIndex()"><span ng-cell-text>{{COL_FIELD CUSTOM_FILTERS}}</span></div>
```

**使用**

```js
$scope.gridOptions = {
	data: 'myData',
	enableColumnResize:true,
    showColumnMenu:true,
	columnDefs: 
		[{field: 'name',width:"35%",displayName: '属	性名',enableCellEdit: true}, 
		{field:'value',width:"35%", displayName:'属性值',enableCellEdit: true},
		{field:'applyToGateway', displayName:'下发',width:'15%', enableCellEdit: false,cellTemplate:'<input type="checkbox"  ng-model=" row.entity.applyToGateway" style=" width: 24px;height: 24px;margin-top:3px;">'},
		{displayName:'操作',width:'15%',cellTemplate:$scope.revise,enableCellEdit: false,cellTemplate:'<a><span class="glyphicon glyphicon-remove" style="margin-left:3%;margin-top:6px;color:#d9534f " ng-click="Remove(row)"></span></a> '}]
};
```

