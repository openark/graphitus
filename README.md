Introduction
============
A simple, client-side, JSON-based [Graphite](http://graphite.wikidot.com/) dashboard system build with [bootstrap](http://twitter.github.com/bootstrap/) and [underscore.js](http://underscorejs.org/)

Installation
============
Graphitus is pure client side, all you have to do to run it is put it under a web-server that can serve http requests.

Configuration
=============

Graphitus dashboards are defined using JSON notation. These configuration files can be stored in a document database like [couchdb](http://couchdb.apache.org/) or [mongo](http://www.mongodb.org/) and can also be placed on the file system where dashboards are served.

Graphitus Configuration
-----------------------

Below is an example of global configuration (a file named ```config.json```) using static local JSON files (the dashboards ids are the file names with a ```.json``` extension):

    	{
    		"graphiteUrl": "http://graphite.mysite.com",
    		"dashboardListUrl": "dashboard-index.json",
    		"dashboardUrlTemplate": "${dashboardId}.json"
    	}

Below is an example of global configuration (a file named ```config.json```) using couch db:

    	{
    		"graphiteUrl": "http://graphite.mysite.com",
    		"dashboardListUrl": "http://couch.mysite.com:5984/graphitus-dashboards/_all_docs", <-- must return a JSON with a "rows" element containing an array of rows with dashboard id ("id" attribute)
    		"dashboardUrlTemplate": "http://couch.mysite.com:5984/graphitus-dashboards/${dashboardId}"
    	}


Dashboard Configuration
-----------------------

Below is an example dashboard configuration:

    	{
    		"title": "MySQL Production Cluster", <-- give a title to page	
    		"columns": 2, <-- the number of charts in a row side by side, mostly 2 or 4
    		"user": "erezmazor", <-- owner	 
    		"timeBack": 12h, <-- time range back from current time (can be expressed in minutes/hours/days/weeks e.g., 30m/12h/1d/2w)	 
    		"theme": "cerulean",	<-- themeing and colors based on Bootswatch themes		
    		"from": "", <-- start date for the date range, prefer timeBack as any date you choose will become stale	 
    		"until": "", <-- end date for the date range, prefer timeBack as any date you choose will become stale	 
    		"width": 700, <-- width of each chart image, should correlate with # columns defined
    		"height": 450,<-- height of each chart image
    		"legend": true, <-- show the legend in chart
    		"refresh": true, <-- auto refresh
    		"refreshIntervalSeconds": 90, <-- auto refresh interval in seconds
    		"averageSeries": false, <-- for targets that aggregate a lot of metrics prefer setting this to true as it will average many lines into one
    		"defaultLineWidth": 2, <-- line width for chart lines
    		"data": [ <-- actual data for chart image
    			{
    				"title": "Slow Queries", <-- a title for the chart image
    				"target": "groupByNode(machines.${dc}dc1.mysql*.Slow_queries,2,\"nonNegativeDerivative\")", <-- the graphite target/function which defines the chart content
                    "params": "areaMode=stacked&lineMode=staircase&colorList=blue,red,green" <-- specify additional parameters for this target
    			},{
    				"title": "Seconds Behind Master",
    				"target": "groupByNode(machines.${dc}dc1.mysql*.Seconds_Behind_Master,2,\"averageSeries\")"
    			},{
    				"title": "Queries Per Second",
    				"target": [ <-- you can specify mutliple targets for a chart as a JSON array
                        "groupByNode(machines.${dc}dc1.mysql*.Qps,2,\"averageSeries\")",
                        "groupByNode(machines.${dc}dc1.mysql*.Qps,2,\"averageSeries\")"
                    ]
    			}
    			],
    			"parameters": { <-- parameters to tokens expressed in targets with ${paramName} format	
    				"datacetner" : { <-- label for a select box in the UI
    				"All": {	 <-- display name for a select box in the UI
    					"dc": "*" <-- the token name (dc) as specified in the target name and the actual token value (*)			
    				},
    				"New York": {		 
    					"dc": "ny" 
    				},
    				"LA": {
    					"dc": "la"
    				},
    				"Chicago": {
    					"dc": "chi"
    				}
    			}
    		}

* This will generate the screen below (actual graph images are mocks):

![Screenshot](https://raw.github.com/erezmazor/graphitus/master/doc/screenshot.png)

* Override configuration with URL parameters

You can specify configuration properties in the dashboard URL to override default settings:

        dashboard.html?id=grp1.dash1&defaultLineWidth=25&timeBack=20m&width=350&height=400&columns=4&legend=false
        
You can also specify parameter values in the URL:

        dashboard.html?id=grp1.dash1&datacenter=LA
        
Configuration attributes
------------------------

Parameter              | Required?       | Description
---------------------- | --------------- | ---------------------------------
title                   | Yes             | The title of the dashboard chart
columns                 | Yes             | The number of images in a row
user                    | No              | Owner
timeBack                | No              | Specify timeframe back from current time to display (specify this or ```from``` and ```until```)
from                    | No              | From date/time in ```yyyy-MM-dd HH:MM``` (specify this and ```until``` or ```timeBack```)
until                   | No              | To date/time in ```yyyy-MM-dd HH:MM``` (specify this and ```from``` or ```timeBack```)
theme                   | No              | Bootswatch theme from BootstrapCDN to use
width                   | Yes             | Width of the chart from graphite (see ```columns```)
height                  | Yes             | Height of the chart from graphite
legend                  | No              | Show/Hide the legend in the chart (omitting leaves it up to graphite)
refresh                 | No              | Auto-refresh the page (see ```refreshIntervalSeconds```)
refreshIntervalSeconds  | No              | When ```refresh``` is true this will determine the refresh interval
defaultLineWidth        | No              | The line width for the generated chart

        
* Themes

[Bootswatch](http://bootswatch.com/) themes are provided via [BootstrapCDN](http://www.bootstrapcdn.com/) 