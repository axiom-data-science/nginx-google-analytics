# nginx-google-analytics

This project demonstrates an approach to track non-HTML web traffic in
[Google Analytics](https://analytics.google.com) using nginx's
`post_action` directive.

## Why is this needed?

Data is usually submitted to Google Analytics via javascript when a page
is loaded or an event occurs. However, it is often useful to track web
traffic other than HTML page loads (e.g. binary data downloads).

## How does this work?

This approach uses the web server ([nginx](https://www.nginx.com/)) to send data
to Google Analytics after the requested resource (image, data file, etc)
has been served to the client. This is accomplished using the `post_action` directive
to send a formatted request to Google's
[Measurement Protocol API](https://developers.google.com/analytics/devguides/collection/protocol/v1/).

## CAVEAT

The nginx `post_action` directive used here is intentionally undocumented
by the nginx developers, and has been referred to as a ["dirty hack"](https://forum.nginx.org/read.php?2,213627,213722).
However, that disparaging comment was made in 2011 and `post_action` appears
to be somewhat commonly used.

__Be warned that `post_action` could possibly be removed in future versions of nginx__.

## Google Analytics Setup

In addition to configuring nginx using the example files in this repository, you
must also configure your Google Analytics properties if you want to track download
bytes. To do so:

1. Go to https://analytics.google.com and select your target property.
1. Open the `Admin` menu
1. Under the `Property` column, expand `Custom Definitions` and click `Custom Metrics`
1. Click `NEW CUSTOM METRIC`
1. Create a new custom metric called `Bytes` with the `Hit` scope, `Integer` formatting type, and Minimum Value `0` and click `Save`
1. Note the Index of the created `Bytes` metric.

__The index of the Bytes metric should be prefixed with `cm` and used in the `proxy_pass` url submitted to
Google Analytics at the bottom of the `nginx_google_analytics` file (example: `cm1`).

You must also create a custom report to visualize the custom Bytes metric (note: there is a
lag between setting up the metric and seeing data in reports.

1. Under `Customization`, click `Custom Reports`
1. Click `New Custom Report`
1. Choose the desired report type (graph, table, etc) and add `Bytes` as a metric (More Dimensions > Custom Metrics > Bytes)
1. Add the desired dimensional drilldown (e.g. Time > Hour of Day)
1. Click Save and view the report
