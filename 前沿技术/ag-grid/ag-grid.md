# ag-grid

* 参考：https://www.itxst.com/ag-grid/tutorial.html

## 主要功能

* Properties, Events, Callbacks and APIs

  https://www.ag-grid.com/react-data-grid/grid-interface/



## 概念

* JavaScript Data Grid: Row Models

The grid comes with four row models:

1. Client-Side
2. Server-Side
3. Infinite
4. Viewport

The Client-Side Row Model deals with client-side data. The Server-Side, Infinite and Viewport Row Models deal with server-side data. The following is a summary of each:



## 坑

### 样式

```js
import {AgGridColumn, AgGridReact} from "ag-grid-react";
import 'ag-grid-community/dist/styles/ag-grid.css';
import 'ag-grid-community/dist/styles/ag-theme-alpine.css';

// 注意：外层的容器一定要加样式 className="ag-theme-alpine"
```



## 常用

### sort api

* 参考： https://ag-grid.com/javascript-data-grid/row-sorting/#sorting-api

### filter

* filter 分为以下三种类型

  * text、number、Date

  参看：https://ag-grid.com/javascript-data-grid/filter-provided-simple/

  类型列表：https://www.ag-grid.com/javascript-data-grid/filter-provided-simple/#default-filter-options

* 先看这个：https://ag-grid.com/javascript-data-grid/filter-provided/

#### simple column filters

* 基础的三种类型：text、number、date
* https://www.ag-grid.com/javascript-data-grid/filter-provided-simple/

#### agMultiColumnFilter

filter: agMultiColumnFilter，这个是能兼容所有的filter类型的



### 自定义 filter

```js
// get filter instance
const filterInstance = gridOptions.api.getFilterInstance('athlete'); 

// get filter model
const model = filterInstance.getModel(); 

// set filter model and update
filterInstance.setModel({
    type: 'endsWith',
    filter: 'thing'
});

// refresh rows based on the filter (not automatic to allow for batching multiple filters)
gridOptions.api.onFilterChanged();
```

* 有如下的配置

| `agNumberColumnFilter` | A Number Filter for number comparisons.|
| ---------------------- | ---------------------------------------|
|`agTextColumnFilter`                    | A Text Filter for string comparisons.                        |
|`agDateColumnFilter`                    | A Date Filter for date comparisons.                          |
| `agSetColumnFilter`                     | A Set Filter, influenced by how filters work in Microsoft Excel. This is an AG Grid Enterprise feature. |

* 注意不能喝 hidden 字段配合，hidden true 不起作用

### 默认 列配置

```js
var gridOptions = {
  columnDefs: columnDefs,
  defaultColDef: {
    flex: 1,
    minWidth: 150,
    filter: true,
  },
};
```



### 头部和右侧栏 UI

* 参考这个例子：https://ag-grid.com/javascript-data-grid/tool-panel-columns/#expand--collapse-column-groups

### 自定义cell 

* 支持自定义组件，注册组件，然后挂载到 cell 中

  参考：https://ag-grid.com/javascript-data-grid/grouping-unbalanced-groups/

### group 相关

#### 自定义 group api

* https://ag-grid.com/javascript-data-grid/grouping/#api-reference

#### 自定义打开的 group

* https://ag-grid.com/javascript-data-grid/grouping-opening-groups/

#### 顶部拖动聚合

* https://ag-grid.com/javascript-data-grid/grouping-group-panel/



#### aggFunc

* 自定义计算聚合



### 自定义 column

* api: gridOptions.columnApi.applyColumnState
* 需要修改某个列的配置，都可以用这个方法

```js
gridOptions.columnApi.applyColumnState({
    state: [
      { colId: 'country', rowGroup: true },
      { colId: 'year', rowGroup: true },
    ],
    defaultState: {
      pivot: false,
      rowGroup: false,
    },
  });
```



#### column api 参考

* https://www.ag-grid.com/javascript-data-grid/column-api/#reference-state-applyColumnState



### 跳转相关

lastRow 一共有多少行，服务端返回数据，建议带上能够设置滚动条长度；

gridOptions.api.setFocusedCell(0, '')

gridOptions.api.ensureIndexVisible()



### 总结

* 所有涉及到要修改 column 情况可以参考：https://ag-grid.com/javascript-data-grid/column-api/#reference-rowGroups

  包括自定义 filter group sort 等。

## serverside

### api

* 更新store：refreshServerSideStore

  https://ag-grid.com/javascript-data-grid/server-side-model-refresh/#reference-serverSideRowModel-refreshServerSideStore



## api

### 设置 column

* https://www.ag-grid.com/javascript-data-grid/column-api/#reference-state-applyColumnState

### 设置 grid 相关（sort、filter、group等）

* https://www.ag-grid.com/javascript-data-grid/grid-api/
