#### grafana 部署
##### 1.0 获取配置
[docker-img](https://hub.docker.com/r/grafana/grafana/)
```bash
mkdir data
mkdir -p data/log
mkdir -p data/lib
```
```bash
docker run -d --name=grafana -p 3000:3000 grafana/grafana
docker cp grafana:/etc/grafana/grafana.ini ./data/
```

##### 1.1 启动docker-compose.yml
```bash
version: "2"
services:
  grafana:
    image: grafana/grafana
    # build:
    #   context: .
    #   dockerfile: Dockerfile
    ports:
     - "3000:3000"
    volumes:
      - ./data/grafana.ini:/etc/grafana/grafana.ini
      - ./data/lib:/var/lib/grafana
      - ./data/log:/var/log/grafana
    environment:
      GF_INSTALL_PLUGINS: "grafana-piechart-panel"
      # FORCE_HOSTNAME: auto
      # INFLUXDB_HOST: influxdb
      # INFLUXDB_PASS: changeme
      # GRAFANA_USER: admin
      # GRAFANA_PASS: AxwayPassword*
```
#### 预览
![image](https://raw.githubusercontent.com/hyhlinux/static/master/img/test_comment.jpg)

#### 当前模版样式
```json
{
  "__inputs": [
    {
      "name": "DS_ES-评论数据源",
      "label": "es-评论数据源",
      "description": "",
      "type": "datasource",
      "pluginId": "elasticsearch",
      "pluginName": "Elasticsearch"
    }
  ],
  "__requires": [
    {
      "type": "datasource",
      "id": "elasticsearch",
      "name": "Elasticsearch",
      "version": "5.0.0"
    },
    {
      "type": "grafana",
      "id": "grafana",
      "name": "Grafana",
      "version": "5.0.4"
    },
    {
      "type": "panel",
      "id": "grafana-piechart-panel",
      "name": "Pie Chart",
      "version": "1.3.0"
    },
    {
      "type": "panel",
      "id": "graph",
      "name": "Graph",
      "version": "5.0.0"
    },
    {
      "type": "panel",
      "id": "singlestat",
      "name": "Singlestat",
      "version": "5.0.0"
    }
  ],
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": "-- Grafana --",
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "description": "HTTP logs  dashboard from Elastic Stack.",
  "editable": true,
  "gnetId": 73,
  "graphTooltip": 0,
  "id": null,
  "links": [],
  "panels": [
    {
      "aliasColors": {
        "410": "#511749"
      },
      "bars": false,
      "dashLength": 10,
      "dashes": false,
      "datasource": "${DS_ES-评论数据源}",
      "editable": true,
      "error": false,
      "fill": 0,
      "grid": {},
      "gridPos": {
        "h": 8,
        "w": 24,
        "x": 0,
        "y": 0
      },
      "id": 2,
      "isNew": true,
      "legend": {
        "alignAsTable": true,
        "avg": false,
        "current": true,
        "max": true,
        "min": false,
        "rightSide": true,
        "show": true,
        "total": false,
        "values": true
      },
      "lines": true,
      "linewidth": 1,
      "links": [],
      "nullPointMode": "connected",
      "percentage": false,
      "pointradius": 5,
      "points": false,
      "renderer": "flot",
      "seriesOverrides": [],
      "spaceLength": 10,
      "stack": false,
      "steppedLine": true,
      "targets": [
        {
          "bucketAggs": [
            {
              "field": "@timestamp",
              "id": "2",
              "settings": {
                "interval": "auto",
                "min_doc_count": 0,
                "trimEdges": 0
              },
              "type": "date_histogram"
            }
          ],
          "dsType": "elasticsearch",
          "groupBy": [
            {
              "params": [
                "$interval"
              ],
              "type": "time"
            },
            {
              "params": [
                "null"
              ],
              "type": "fill"
            }
          ],
          "metrics": [
            {
              "field": "count",
              "id": "4",
              "meta": {},
              "settings": {},
              "type": "count"
            }
          ],
          "policy": "default",
          "query": "",
          "refId": "A",
          "resultFormat": "time_series",
          "select": [
            [
              {
                "params": [
                  "value"
                ],
                "type": "field"
              },
              {
                "params": [],
                "type": "mean"
              }
            ]
          ],
          "tags": [],
          "timeField": "@timestamp"
        }
      ],
      "thresholds": [],
      "timeFrom": null,
      "timeShift": null,
      "title": "评论数量统计",
      "tooltip": {
        "msResolution": false,
        "ordering": "alphabetical",
        "shared": true,
        "sort": 0,
        "value_type": "cumulative"
      },
      "type": "graph",
      "xaxis": {
        "buckets": null,
        "mode": "time",
        "name": null,
        "show": true,
        "values": []
      },
      "yaxes": [
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        },
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        }
      ]
    },
    {
      "aliasColors": {},
      "breakPoint": "50%",
      "cacheTimeout": null,
      "combine": {
        "label": "Others",
        "threshold": 0
      },
      "datasource": "${DS_ES-评论数据源}",
      "fontSize": "80%",
      "format": "short",
      "gridPos": {
        "h": 12,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "id": 15,
      "interval": null,
      "legend": {
        "percentage": true,
        "percentageDecimals": null,
        "show": true,
        "sort": "total",
        "sortDesc": true,
        "values": true
      },
      "legendType": "Under graph",
      "links": [],
      "maxDataPoints": 3,
      "nullPointMode": "connected",
      "pieType": "pie",
      "strokeWidth": 1,
      "targets": [
        {
          "bucketAggs": [
            {
              "fake": true,
              "field": "country.keyword",
              "id": "3",
              "settings": {
                "min_doc_count": 1,
                "order": "desc",
                "orderBy": "_count",
                "size": "10"
              },
              "type": "terms"
            },
            {
              "field": "@timestamp",
              "id": "2",
              "settings": {
                "interval": "auto",
                "min_doc_count": 0,
                "trimEdges": 0
              },
              "type": "date_histogram"
            }
          ],
          "metrics": [
            {
              "field": "user_id",
              "id": "1",
              "meta": {},
              "settings": {},
              "type": "count"
            }
          ],
          "refId": "A",
          "timeField": "@timestamp"
        }
      ],
      "title": "评论地区分布",
      "type": "grafana-piechart-panel",
      "valueName": "total"
    },
    {
      "aliasColors": {},
      "breakPoint": "50%",
      "cacheTimeout": null,
      "combine": {
        "label": "Others",
        "threshold": 0
      },
      "datasource": "${DS_ES-评论数据源}",
      "fontSize": "80%",
      "format": "short",
      "gridPos": {
        "h": 12,
        "w": 12,
        "x": 12,
        "y": 8
      },
      "id": 23,
      "interval": null,
      "legend": {
        "percentage": true,
        "show": true,
        "sort": "total",
        "sortDesc": true,
        "values": true
      },
      "legendType": "Under graph",
      "links": [],
      "maxDataPoints": 3,
      "nullPointMode": "connected",
      "pieType": "pie",
      "strokeWidth": 1,
      "targets": [
        {
          "bucketAggs": [
            {
              "fake": true,
              "field": "language.keyword",
              "id": "3",
              "settings": {
                "min_doc_count": 1,
                "order": "desc",
                "orderBy": "_term",
                "size": "10"
              },
              "type": "terms"
            },
            {
              "field": "@timestamp",
              "id": "2",
              "settings": {
                "interval": "auto",
                "min_doc_count": 0,
                "trimEdges": 0
              },
              "type": "date_histogram"
            }
          ],
          "metrics": [
            {
              "field": "select field",
              "id": "1",
              "type": "count"
            }
          ],
          "refId": "A",
          "timeField": "@timestamp"
        }
      ],
      "title": "评论语言分布",
      "transparent": false,
      "type": "grafana-piechart-panel",
      "valueName": "total"
    },
    {
      "cacheTimeout": null,
      "colorBackground": false,
      "colorValue": false,
      "colors": [
        "#299c46",
        "rgba(237, 129, 40, 0.89)",
        "#d44a3a"
      ],
      "datasource": "${DS_ES-评论数据源}",
      "format": "none",
      "gauge": {
        "maxValue": 100,
        "minValue": 0,
        "show": false,
        "thresholdLabels": false,
        "thresholdMarkers": true
      },
      "gridPos": {
        "h": 12,
        "w": 12,
        "x": 0,
        "y": 20
      },
      "id": 21,
      "interval": null,
      "links": [],
      "mappingType": 1,
      "mappingTypes": [
        {
          "name": "value to text",
          "value": 1
        },
        {
          "name": "range to text",
          "value": 2
        }
      ],
      "maxDataPoints": 100,
      "nullPointMode": "connected",
      "nullText": null,
      "postfix": "",
      "postfixFontSize": "50%",
      "prefix": "",
      "prefixFontSize": "50%",
      "rangeMaps": [
        {
          "from": "null",
          "text": "N/A",
          "to": "null"
        }
      ],
      "sparkline": {
        "fillColor": "rgba(31, 118, 189, 0.18)",
        "full": false,
        "lineColor": "rgb(31, 120, 193)",
        "show": true
      },
      "tableColumn": "",
      "targets": [
        {
          "bucketAggs": [
            {
              "field": "@timestamp",
              "id": "2",
              "settings": {
                "interval": "auto",
                "min_doc_count": 0,
                "trimEdges": 0
              },
              "type": "date_histogram"
            }
          ],
          "metrics": [
            {
              "field": "select field",
              "id": "1",
              "type": "count"
            }
          ],
          "refId": "A",
          "timeField": "@timestamp"
        }
      ],
      "thresholds": "",
      "title": "评论总数",
      "type": "singlestat",
      "valueFontSize": "80%",
      "valueMaps": [
        {
          "op": "=",
          "text": "N/A",
          "value": "null"
        }
      ],
      "valueName": "total"
    }
  ],
  "refresh": false,
  "schemaVersion": 16,
  "style": "dark",
  "tags": [
    "http",
    "lgfront"
  ],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-5m",
    "to": "now"
  },
  "timepicker": {
    "refresh_intervals": [
      "5s",
      "10s",
      "30s",
      "1m",
      "5m",
      "15m",
      "30m",
      "1h",
      "2h",
      "1d"
    ],
    "time_options": [
      "5m",
      "15m",
      "1h",
      "6h",
      "12h",
      "24h",
      "2d",
      "7d",
      "30d"
    ]
  },
  "timezone": "browser",
  "title": "test-评论报表",
  "uid": "0MRsjKWmz",
  "version": 18
}
```
