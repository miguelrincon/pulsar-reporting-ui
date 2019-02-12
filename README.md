Pulsar Reporting UI
===================

Pulsar Reporting UI is a complete solution to visualize reports that renders visualizations of your events and traffic in real-time.

Pulsar Reporting UI is built on AngularJS, allows developers to create Pulsar visualizations in different ways.

- Time bound and Real-time visualizations
- Dimension vs. Metric visualizations
- Display of widgets which display fetched data
- Creation of dashboards by grouping widgets programatically
- Creation of dashboards from the UI for non-experts users
- Administrative capabilities to manage users and access

Pulsar Reporting UI depends on the [Pulsar Reporting API](https://github.com/pulsarIO/pulsar-reporting-api).

See the original whitepaper [Pulsar – Real-time Analytics at Scale (PDF)](https://github.com/pulsarIO/realtime-analytics/wiki/documents/Whitepaper_Pulsar_Real-timeAnalyticsatScale.pdf)

# Index

> 0. [Installation](#installation)
> 0. [Usage](#usage)
> 0. [Angular Components](#angular-components)
> 0. [Create a report with `ui-router`](#create-a-report-with-ui-router)
> 0. [Admin](#admin)

# Installation
If you haven't, please download and install the latest [node](https://nodejs.org/download/) which includes npm, a JavaScript package manager.

## 0. Install bower and grunt:
```
$ npm install -g bower
$ npm install -g grunt-cli
```

## 1. Add Pulsar Reporting UI as a bower dependency

```
$ bower install pulsar-reporting-ui --save
```

Optionally, you may add `pulsar-reporting-ui` to your `bower.json` dependencies 
```
"dependencies": {
  ...
  "pulsar-reporting-ui": "~0.2.0"
  ...
}
```

## 2. Include depdendencies in html

Pulsar Reporting UI has a big list of dependencies. Add the libraries to your `index.html` file, ensure all the files are added:

```html

<!-- ========== jQuery, lodash and Bootstrap ============ -->

<script src="bower_components/jquery/dist/jquery.js"></script>
<script src="bower_components/jquery-ui/jquery-ui.js"></script>
<script src="bower_components/lodash/lodash.min.js"></script>
<script src="bower_components/bootstrap/dist/js/bootstrap.js"></script>

<!-- ========== Libraries =============================== -->

<script src="bower_components/d3/d3.js"></script>
<script src="bower_components/nvd3/build/nv.d3.js"></script>
<script src="bower_components/moment/moment.js"></script>
<script src="bower_components/moment-timezone/builds/moment-timezone-with-data.js"></script>
<script src="bower_components/knex/browser/knex.js"></script>
<script src="bower_components/bootstrap-daterangepicker/daterangepicker.js"></script>
<script src="bower_components/countto/jquery.countTo.js"></script>

<!-- ========== Angular Modules ================= -->

<script src="bower_components/angular/angular.js"></script>

<script src="bower_components/angular-resource/angular-resource.js"></script>
<script src="bower_components/angular-sanitize/angular-sanitize.js"></script>
<script src="bower_components/angular-ui-router/release/angular-ui-router.js"></script>
<script src="bower_components/angular-breadcrumb/dist/angular-breadcrumb.js"></script>
<script src="bower_components/angular-ui-router-menus/dist/angular-ui-router-menus.js"></script>
<script src="bower_components/angular-ui-grid/ui-grid.js"></script>
<script src="bower_components/angular-ui-select/dist/select.js"></script>
<script src="bower_components/angular-ui-sortable/sortable.js"></script>
<script src="bower_components/angular-nvd3/dist/angular-nvd3.js"></script>
<script src="bower_components/angular-moment/angular-moment.js"></script>
<script src="bower_components/angular-bootstrap/ui-bootstrap-tpls.js"></script>
<script src="bower_components/angular-load/angular-load.js"></script>
<script src="bower_components/angular-loading-bar/build/loading-bar.min.js"></script>
<script src="bower_components/angular-animate/angular-animate.min.js"></script>

<!-- Must be downloaded manually -->
<script src="vendor/plugin/jvectormap/jquery-jvectormap-2.0.4.min.js"></script>

```

**Note**: At the moment jvectormap is not provided as a bower component, please download it manually from their [download page](http://jvectormap.com/download/).

## 3. Adding dependencies to your AngularJS app

Pulsar Reporting UI is split in different components. To start quickly we recommend including the `pr.dashboard.widgets.all`, which includes all the widgets to quickly create a report.

```js
angular.module('myModule', [
  'pr.tpls',
  'pr.dashboard.widgets.all'
]);
```
# Usage

## 1.  Configuration of your project

Set up the correct URL to the back-end of your application, by default the same as the URL of the front-end.

```js
.config( 
    function(prApiProvider) {

      prApiProvider.url('http://wwww.example.com/prapi/v2');

      // Optionally, configure the timezone of your back-end. `MST` by default
      prApiProvider.timezone('MST');

    });
```

## 2. Test if a widget works

In your `html` template:
```html
  <pr-pie-widget params="myParams" options="myOptions" filters="myFilters">
  </pr-pie-widget>
```

In your controller code:
```js
$scope.myParams = {
  dataSourceName: 'trackingdruid',
  table: 'event',
  metrics: [{
    name: 'guid_hll',
    type: 'unique',
    alias: 'activeusers'
  }],
  dimensions: [{
    name: 'osfamily'
  }],
  granularity: 'all',
  maxResults: 10
};

$scope.myOptions = {}

$scope.myFilters = {
  intervals: '2015-10-11 00:00:00/2015-10-17 23:59:59',
  where: {}
}
```

Check the page, you may need to setup a server for your html file. You can follow step 6 for this.

## 3. (Optional) Start a node server on your localhost

You may use any server technology. A quick setup can be done using node.js. Install `connect` and `serve-static`

```
$ npm install connect
$ npm install serve-static
```

Add a directory and JS file, named server/server.js in the root of your project, 

```js
var connect = require('connect');
var serveStatic = require('serve-static');

var path = '../app'; // The path to the directory of your index.html
var port = 9090; // Can be any available port

connect()
  .use(serveStatic(path, {index: ['index.html', 'index.htm']}))
  .listen(port);
console.info('Server started at http://localhost:' + port);
```

Go to [http://localhost:9090](http://localhost:9090)

# Angular Components

Following is a guide with the details of how to create charts and dashboard from the data sources. Pulsar Reporting UI is pure AngularJS framework. It includes many services and components to build your reports easily.
We also include two sample dashboards in our build and you can use them as a template to develop your own applications. 


## Directives

### Widget directives

Widgets display data in a self contined space, each of them run a single query to fetch data. They always follow the same interface. The `params` object, the `options` object and the `filters` object and the optional `transform-data` function:

#### `params`

Params is an object that defines a single query.

*`dataSourceName`*: The name of you dataSource.
```js
dataSourceName: 'trackingdruid'
```
*`table`*: The name of the table within your dataSource.
```js
table: 'sessions'
```
*`metrics`*: An array of objects, the metrics to be aggregated. Each metric must define a `name`, a `type` of aggregation and an `alias` to be displayed. For example:
```js
metrics: [{
  name: 'clicks', // Name of the metric in the dataSource
  type: 'count', // Type of aggregation used
  alias: 'Click Count' // Alias which can be used on display
}]
```
*`dimensions`*: An array of objects, the dimensions which will be used as groupings. Each dimension must define a `name`.
```js
dimensions: [{
  name: 'trafficsource', // Name of the dimension
}]
```
*`granularity`*: A string which define another kind of grouping, in time. The value can be `all`, `week`, `day` or `hour`.
```js
granularity: 'hour'
```
*`maxResults`*: A number which is the maximum number of results in the query.
```js
maxResults: 20
```

Useful widgets directives for developer to build widgets.

#### `options`

Additional options which will affect the display of the widget.

* `title`: Title of the widget (only used in widget directive)
* `disabled`: Set to `true` to programatically disable the widgets
* `chart`: *(Only for nvd3 chart widgets)* Object which will be passed down to the nvd3 options. Check [nvd3](https://github.com/novus/nvd3) for more details.
* `grid`: *(Only in `pr-grid-widget`)* Object which will be passed down to the ui-grid object.

#### `filters`

Additional options which will affect the display of the widget.

*`intervals`*: The window of time to be queried, 2 dates separated by a slash. The data format is `YYYY-MM-DD hh:mm:ss`.
```js
intervals: '2015-10-16 00:00:00/2015-10-22 23:59:59'
```
*`where`*: Object which will be used to create a `where` filter in the SQL query. The key is the name of dimension or metric and the value, the accepted values.
```js
where: {
  site: 1,
  devicefamily: ['Mobile', 'Tablet']
}
// creates the following: WHERE site = 1 AND [devicefamily = 'Mobile' OR devicefamily = 'Tablet']
```
*`whereRaw`*: (optional) If there is a need to send the raw conditions in SQL format.
```js
whereRaw: 'trafficsource IS NOT null'
```

#### `transform-data`

Transform data allows flexibility by modifying the array data values before they are displayed. The data structure is different depending on the tpye of widget.
```js
$scope.transformData = function(data) {
  data[0].x = 'Total';
  return data;
}
```

### Widget directives list

#### `prBarWidget`
![](blob/master/app/wikiPic/bar.JPG?raw=true)
```
<pr-bar-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-bar-widget>
```

#### `prGridWidget`

![](blob/master/app/wikiPic/table.JPG?raw=true)
```
<pr-grid-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-grid-widget>
```
#### `prGroupedBarWidget`

![](blob/master/app/wikiPic/groupbar.png?raw=true)
```
<pr-grouped-bar-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-grouped-bar-widget>
```
#### `prLineWithFocusWidget`

![](blob/master/app/wikiPic/lineWithfocus.png?raw=true)
```
<pr-line-with-focus-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-line-with-focus-widget>
```
#### `prMetricWidget`

![](blob/master/app/wikiPic/metric.JPG?raw=true {width=400px height=200px})
```
<pr-metric-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-metric-widget>
```
#### `prPieWidget`

![](blob/master/app/wikiPic/pie.JPG?raw=true)
```
<pr-pie-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-pie-widget>
```
#### `prStackWidget`
```
<pr-stack-widget params="params" options="options" filters="filters" transform-data="transformData"></pr-stack-widget>
```
#### `prTimelineWidget`

![](blob/master/app/wikiPic/timeline.JPG?raw=true)

![](blob/master/app/wikiPic/area.JPG?raw=true)
```
<pr-timeline-widget options="widget.options" params="widget.params" filters="filters" transform-data="transformData"></pr-timeline-widget>
```

### Other directives

#### `prCountTo`
To count up (or down) to a target number at a specified speed in angularJS way, which base on [jQuery countTo](https://github.com/mhuggins/jquery-countTo).
```
<pr-count-to value="totalNumber">
```

#### `prDatepicker`
Users can select an interval (start time and end time) from a popup calendar, which base on [bootstrap-daterangepicker](https://github.com/dangrossman/bootstrap-daterangepicker) and momentjs(http://momentjs.com).

![](blob/master/app/wikiPic/daterangepicker.png?raw=true)
```
<pr-datepicker start-date="$stateParams.start" end-date="$stateParams.end" callback="changeDateRange" opens="left" class="pull-right"></pr-datepicker>
```

#### `prGridExtensions`
Enhance [ui-grid](http://ui-grid.info) directive.
* prGridHeight: Dynamically adjust the height of ui grid.
* prGridLimitation: Set a maximum selected items for ui grid.
```
<div ui-grid="grid.gridOptions" ui-grid-selection ui-grid-pagination ui-grid-auto-resize pr-grid-height  pr-grid-limitation="5" class="grid"></div>
```

#### `prDashboard`
Creates an interactive dahboard report from a model.
```
<pr-dashboard model="dashboard" layout="dashboard.config.layout" edit-mode="editMode" filters="dashboard.config.filters"></pr-dashboard>
```

#### `prDynamicFilter`
Displays a list of filters for user to drilldown in the data.

![](blob/master/app/wikiPic/dynamicFilter.png?raw=true)
```
<pr-dynamic-filter datasource="dataSourceName" table="table" dimensions="dimensions"></pr-dynamic-filter>
```

## Services

### `prDatasourceSqlService`
The prDatasourceSqlService handles queries to the dataSource backend, it wraps around a configurable $resource object. It provides several functions, such as getDimensions, getMetrics, getDataSources, getDataset etc.

### `prUIOptionService`
Provide the default widgets configuration to help developer create widget convenient.

## Filters

Several useful filters used for numbers or dates: `duration`, `float`, `intervalDate`, `percentage`, `range`.

# Create a report with `ui-router`

`ui-router` is a popular Angular components which provides a more powerful routing than Angular's core router.

Altough not mandatory, Pulsar Reporting UI uses `ui-router` in most of its examples to quickly setup the demos and applications. Many tutorials can be found online for `ui-router` we recommend understanding the main concepts of `ui-router` before following the next step. The wiki offers good documentation. 

## 1. Create a `ui-router` state for your report

First we add a state in the config step of your AngularJS module.

```js
angular.module('pr.demo.traffic', [
  'ui.router',
  'pr.date',
  'pr.dashboard.widgets.stack',
])
.config(
    function($stateProvider) {
      $stateProvider.state('home.demo.traffic', {

        url: '/traffic?start&end',

        views: {
          '': {
            templateUrl: 'src/demo/traffic/traffic.html',
            controller: 'TrafficController'
          }
          'trend@home.demo.traffic': {
            templateUrl: 'src/demo/traffic/trafficTrend.html',
            controller: 'TrafficTrendController'
          }
        }
      });
    })
```

In this example we will have 2 controllers, which are nested by the HTML templates.

## 2. Add a report, its controller and html template

```js

angular.module('pr.demo.traffic')
.controller('TrafficController',
    function($state, $stateParams, $scope, $filter) {

      $scope.$state = $state;
      $scope.$stateParams = $stateParams;

      /**
       * Navigates to a new date range, used by pr-datepicker directive
       * as a callback for date changes.
       * @param  {moment object} start Start Date
       * @param  {moment object} end   End Date
       */
      $scope.changeDateRange = function(start, end) {
        $scope.$state.go('.', {
          start: start,
          end: end
        });
      };

      // Filters are global to the report.
      $scope.filters = {
        intervals: $filter('intervalDate')($state.params.start) + '/' + $filter('intervalDate')($state.params.end),
      };

    })
```

```html
<section class="content-header">
  <h1>
    Traffic Tutorial Page
  </h1>
</section>

<section class="content">
  <div class="row clearfix">
    <section class="pull-right col-md-3">
      <div class="text-right margin-bottom clearfix">
        <pr-datepicker start-date="$stateParams.start" end-date="$stateParams.end" callback="changeDateRange" opens="left" class="pull-right"></pr-datepicker>
      </div> 
    </section>
  </div>

  <div class="row">
    <div class="col-sm-12" ui-view="trend"></div> <!-- TrafficTrendController is setup here -->
  </div>
</section>
```

## 3. Define your widget controller

```js
angular.module('pr.demo.traffic')
.controller('TrafficTrendController',
    function($scope, prUIOptionService, $filter, $location) {
      $scope.options = {
        disabled: false,
        chart: {
          showLegend: true,
        }
      };

      // Reset data to fit legend
      $scope.transformData = function(data) {
        // ... 
        return data;
      };

      $scope.params = {
        dataSourceName: 'trackingdruid',
        table: 'pulsar_session',
        granularity: 'day',
        dimensions: [],
        metrics: [
          {
            name: 'count',
            type: 'sum',
            alias: 'total'
          }
        ],
        maxResults: 100
      };

    })
```

Notice the filters were defined in the parent controller

```html
<div class="box box-default">
  <div class="box-header with-border">
    <h3 class="box-title">
      <i class="fa fa-line-chart"></i> Trend of Sessions
    </h3>
  </div>
  <div class="box-body">
    <pr-stack-widget params="params" options="options" filters="filters" transform-data="transformData"></pr-stack-widget>
  </div>
  <div ng-show="wait" class="overlay">
    <i class="fa fa-refresh fa-spin"></i>
  </div>
</div>
```

# Admin

## What is Admin for Pulsar Reporting UI?

Admin provides a functions to enable different kinds of permission control using the RESTful API provided by Pulsar.

With this component, you can create **DataSources** you own or **Dashboards** and you can also create some specific **Groups** to grant users your own rights. Every user can pass all their rights to other users without need of being an admin.
  
## Which resources does Admin include?

Resources in Admin are: **DataSource**, **Dashboard**, **User** and **Group**. **Group** is a resource which defines a set of permissions. Since a **Group** may contain other **Groups**, **Group** is a very powerful kind of reosurce.
   
   * DataSource: DataSource is an abstraction of data storage. You could create a kind of data storage like druid, kylin and pulsar and then based on this type you specify your set-up endpoint to point to the right place of the server. The last thing comes to finish a DataSource's creation is to give it a display name then you can find it relatively easily. But please be noticed of the fact that displayname is not unique. After you post your input data to create a new DataSource, you'd better also take care of the name of the just created DataSource to make sure you could choose the right one you need for use because name is unique. For your created DataSource you own manage level permission of it. It means you can grant other users manage or view level permission of it.
   If you only own view level permission of a DataSource, then you can only pass its view level permission to others. In conclusion, the basic rule about passing manage and view level permission of DataSource is No Beyond Than.
   
   * Dashboard: Dashboard is a customized collection of widgets mixed with layout configuration. It is created in front-end user interface. It is manageable under a group.Its displayname and name rule is same as description for DataSource and so is the rule to pass its manage and view level permission.
   
   * Group: Group is directly related with granting permissions operation. In other words, if you want to grant new permissions or revoke permissions or change permission, you must operate a Group. In DataBase model, a group corresponds to a set of users and four types of permissions. Any user in the group means the user owning all permissions under the group. The four types of permissions are DataSource, Dashboard, Group and System Right.
   In DataBase layer, the former three types of permissions both has two kinds of permission:_Manage_ And _View_. Please take care the 'And' but not 'Or'.
   It means for a specified right typed of DataSource or Dashboard or Group may exist two records. When you are ready to update a group,if you have manage level permission of the resource, you can keep two records or only manage level or only view level permission, it just depends on your will.
  But if you only have view level permission of the resource.You couldn't do anything for the manage level permission of the resource no matter if the manage level permission exists or not. You are just capable of seeing it there.But for view level permission you can do anything you want.
  If you want to pass manage or view level permission of your manageable group, say A. What should you do? The actions you need to take is you need to add corresponding users into a new created group or an existing one, say B and then at last add the manage or view level permission of A to group B.
  Group also has displayname and name, its rule keeps same as DataSource.
  
## How to see a group currently owned permissions or already selected permissions?
After all groups and its users is loaded over, you can click edit button to pop up group edit dialog. Without any touch all permissions' checked control buttons, when you click selected, the items listed in the table means permission under current group. If you already touched permissions' checked control buttons, the list means the collection you want pick up to update the group's permissions.
