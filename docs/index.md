---
hide:
  - footer
---

# Welcome to FINC50

[FINC50](/) is a finance course before even your Finance 101.

*[Finance 101]: "101" represents a basic beginning course.

Objectives of this site are:

- to introduce modern finance topics covered in undergraduate and postgraduate courses
- to experiment interactive web-based technologies in learning and teaching  

## Finance topics explained

We will start from the basics and then walk our way through the fancy world of finance.

For example, we invest $100 today at an interest rate of 5% per year. What would be the value of our little investment some time in the future? The plot below shows the annual balance.

```vegalite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container",
  "description": "Annual balance of investing $100 at 5% per year for 30 years",
  "title": "Annual balance of investing $100 at 5% per year for 30 years",
  "data": {
    "sequence": {
      "start": 0,
      "stop": 31,
      "step": 1,
      "as": "date"
    }
  },
  "transform": [
    {
      "calculate": "100*pow(1.05, datum.date)",
      "as": "payoff"
    }
  ],
  "mark": {"type": "bar", "size": 5},
  "encoding": {
    "x": {"field": "date", "type": "nominal", "axis": {"title": "Year", "labelAngle": 0}},
    "y": {"field": "payoff", "type": "quantitative", "axis": {"title": "Balance", "labelAngle": 0, "domain": false}},
    "tooltip": [
      {"field": "payoff", "type": "quantitative"}
    ]
  }
}
```

??? note

    If you like some math, then we can denote by

    - \(CF_0\) the initial $100 today,
    - \(CF_t\) the value of the investment in year \(t\), and
    - \(r\) the interest rate of 5% per year.

    The formula below gives the relationship, which relates the present value and future value:

    *[present value]: The value today of some payoffs in the future.
    *[future value]: The value in the future of some payoffs today.

    \[
    CF_t = CF_0 \times (1+r)^t
    \]

## Embrace interactivity

Interactive charts and diagrams are nice and better if they can help illustrate complex concepts.

### Cumulative stock return

For example, we have a few tech stocks. We want to know the cumulative returns we could have earned if we started investing in them, respectively, at some time in the past.

The interactive chart below then does a good job showing the cumulative percentage returns.[^1]

[^1]: The data and plot are both sourced from [https://vega.github.io/](https://vega.github.io/).

```vegalite
{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "description": "An interactive line chart of stock prices, with returns shown relative to a selected date.",
  "width": 650,
  "height": 300,
  "padding": 5,
  "autosize": {"type": "fit", "contains": "padding"},
  "signals": [
    {
      "name": "indexDate",
      "update": "time('Jan 1 2005')",
      "on": [
        {"events": "mousemove", "update": "invert('x', clamp(x(), 0, width))"}
      ]
    },
    {"name": "maxDate", "update": "time('Mar 1 2010')"}
  ],
  "data": [
    {
      "name": "stocks",
      "url": "https://raw.githubusercontent.com/vega/vega-datasets/main/data/stocks.csv",
      "format": {
        "type": "csv",
        "parse": {"price": "number", "date": "date"},
        "delimiter": ","
      }
    },
    {
      "name": "index",
      "source": "stocks",
      "transform": [
        {
          "type": "filter",
          "expr": "month(datum.date) == month(indexDate) && year(datum.date) == year(indexDate)"
        }
      ]
    },
    {
      "name": "indexed_stocks",
      "source": "stocks",
      "transform": [
        {
          "type": "lookup",
          "from": "index",
          "key": "symbol",
          "fields": ["symbol"],
          "as": ["index"],
          "default": {"price": 0}
        },
        {
          "type": "formula",
          "as": "indexed_price",
          "expr": "datum.index.price > 0 ? (datum.price - datum.index.price)/datum.index.price : 0"
        }
      ]
    }
  ],
  "scales": [
    {
      "name": "x",
      "type": "time",
      "domain": {"data": "stocks", "field": "date"},
      "range": "width"
    },
    {
      "name": "y",
      "type": "linear",
      "domain": {"data": "indexed_stocks", "field": "indexed_price"},
      "nice": true,
      "zero": true,
      "range": "height"
    },
    {
      "name": "color",
      "type": "ordinal",
      "range": "category",
      "domain": {"data": "stocks", "field": "symbol"}
    }
  ],
  "axes": [{"orient": "left", "scale": "y", "grid": true, "format": "%"}],
  "marks": [
    {
      "type": "group",
      "from": {
        "facet": {
          "name": "series",
          "data": "indexed_stocks",
          "groupby": "symbol"
        }
      },
      "data": [
        {
          "name": "label",
          "source": "series",
          "transform": [{"type": "filter", "expr": "datum.date == maxDate"}]
        }
      ],
      "marks": [
        {
          "type": "line",
          "from": {"data": "series"},
          "encode": {
            "update": {
              "x": {"scale": "x", "field": "date"},
              "y": {"scale": "y", "field": "indexed_price"},
              "stroke": {"scale": "color", "field": "symbol"},
              "strokeWidth": {"value": 2}
            }
          }
        },
        {
          "type": "text",
          "from": {"data": "label"},
          "encode": {
            "update": {
              "x": {"scale": "x", "field": "date", "offset": 2},
              "y": {"scale": "y", "field": "indexed_price"},
              "fill": {"scale": "color", "field": "symbol"},
              "text": {"field": "symbol"},
              "baseline": {"value": "middle"}
            }
          }
        }
      ]
    },
    {
      "type": "rule",
      "encode": {
        "update": {
          "x": {"field": {"group": "x"}},
          "x2": {"field": {"group": "width"}},
          "y": {
            "value": 0.5,
            "offset": {"scale": "y", "value": 0, "round": true}
          },
          "stroke": {"value": "black"},
          "strokeWidth": {"value": 1}
        }
      }
    },
    {
      "type": "rule",
      "encode": {
        "update": {
          "x": {"scale": "x", "signal": "indexDate", "offset": 0.5},
          "y": {"value": 0},
          "y2": {"field": {"group": "height"}},
          "stroke": {"value": "firebrick"}
        }
      }
    },
    {
      "type": "text",
      "encode": {
        "update": {
          "x": {"scale": "x", "signal": "indexDate"},
          "y2": {"field": {"group": "height"}, "offset": 15},
          "align": {"value": "center"},
          "text": {"signal": "timeFormat(indexDate, '%b %Y')"},
          "fill": {"value": "firebrick"}
        }
      }
    }
  ],
  "config": {}
}
```

### Simulation, visually and vividly

Interactivity can also provide an unforgettable visual intuition of the underlying dynamics.

The example below is to approximate the value of $\pi$ by [summing a circle's area](https://en.wikipedia.org/wiki/Approximations_of_%CF%80#Summing_a_circle's_area).

```vegalite
{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "description": "Estimating the value of π via random sampling methods.",
  "autosize": "pad",
  "height": 258,
  "data": [
    {
      "name": "random_data",
      "transform": [
        {"type": "sequence", "start": 1, "stop": 5001},
        {"type": "formula", "as": "x", "expr": "random()"},
        {"type": "formula", "as": "y", "expr": "random()"},
        {"type": "filter", "expr": "datum.data <= points"}
      ]
    },
    {
      "name": "pi_estimates",
      "source": "random_data",
      "transform": [
        {
          "type": "formula",
          "as": "is_inside",
          "expr": "(datum.x * datum.x + datum.y * datum.y) < 1"
        },
        {
          "type": "window",
          "fields": ["is_inside"],
          "ops": ["sum"],
          "as": ["num_inside"]
        },
        {
          "type": "formula",
          "as": "estimate",
          "expr": "4 * datum.num_inside / datum.data"
        }
      ]
    },
    {
      "name": "pi_estimate",
      "source": "pi_estimates",
      "transform": [
        {"type": "filter", "expr": "datum.data == points"},
        {"type": "formula", "as": "value", "expr": "datum.estimate"}
      ]
    },
    {"name": "pi", "values": [{"value": 3.141592653589793}]}
  ],
  "signals": [
    {
      "name": "points",
      "value": 1000,
      "bind": {
        "input": "range",
        "min": 10,
        "max": 5000,
        "step": 1,
        "debounce": 10
      }
    }
  ],
  "layout": {"padding": 70, "bounds": "flush", "align": "none"},
  "marks": [
    {
      "type": "group",
      "style": "cell",
      "title": {"text": "In Points and Out Points", "frame": "group"},
      "encode": {
        "update": {
          "width": {"signal": "height"},
          "height": {"signal": "height"}
        }
      },
      "marks": [
        {
          "type": "arc",
          "encode": {
            "enter": {
              "stroke": {"value": "#888"},
              "strokeWidth": {"value": 1},
              "startAngle": {"signal": "1.570796"},
              "endAngle": {"value": 0},
              "x": {"value": 0.5},
              "y": {"signal": "height + 0.5"},
              "innerRadius": {"signal": "height"},
              "outerRadius": {"signal": "height"}
            }
          }
        },
        {
          "type": "symbol",
          "style": ["circle"],
          "from": {"data": "random_data"},
          "encode": {
            "update": {
              "opacity": {"value": 0.6},
              "fill": [
                {"test": "hypot(datum.x, datum.y) <= 1", "value": "#003f5c"},
                {"value": "#ffa600"}
              ],
              "x": {"scale": "x_scale", "field": "x"},
              "y": {"scale": "y_scale", "field": "y"},
              "shape": {"value": "circle"}
            }
          }
        }
      ],
      "axes": [
        {
          "scale": "x_scale",
          "orient": "bottom",
          "title": "x",
          "labelFlush": true,
          "labelOverlap": true,
          "zindex": 1
        },
        {
          "scale": "x_scale",
          "orient": "bottom",
          "grid": true,
          "gridScale": "y_scale",
          "domain": false,
          "labels": false,
          "maxExtent": 0,
          "minExtent": 0,
          "ticks": false,
          "zindex": 0
        },
        {
          "scale": "y_scale",
          "orient": "left",
          "title": "y",
          "labelOverlap": true,
          "zindex": 1
        },
        {
          "scale": "y_scale",
          "orient": "left",
          "grid": true,
          "gridScale": "x_scale",
          "domain": false,
          "labels": false,
          "maxExtent": 0,
          "minExtent": 0,
          "ticks": false,
          "zindex": 0
        }
      ]
    },
    {
      "type": "group",
      "name": "concat_1_group",
      "style": "cell",
      "title": {"text": "π Estimate", "frame": "group"},
      "encode": {
        "update": {
          "width": {"signal": "height"},
          "height": {"signal": "height"}
        }
      },
      "marks": [
        {
          "type": "symbol",
          "style": ["circle"],
          "from": {"data": "pi_estimates"},
          "encode": {
            "update": {
              "opacity": {"value": 0.7},
              "fill": {"value": "#4c78a8"},
              "x": {"scale": "data_point_scale", "field": "data"},
              "y": {"scale": "pi_scale", "field": "estimate"},
              "size": {"value": 8},
              "shape": {"value": "circle"}
            }
          }
        },
        {
          "type": "rule",
          "from": {"data": "pi"},
          "encode": {
            "update": {
              "stroke": {"value": "darkgrey"},
              "x": {"value": 0},
              "y": {"scale": "pi_scale", "field": "value"},
              "x2": {"field": {"group": "width"}}
            }
          }
        },
        {
          "type": "text",
          "from": {"data": "pi"},
          "encode": {
            "update": {
              "align": {"value": "left"},
              "x": {"value": 10},
              "fill": {"value": "black"},
              "y": {"scale": "pi_scale", "field": "value", "offset": -5},
              "text": {"value": "Real PI: 3.1415..."}
            }
          }
        },
        {
          "type": "text",
          "from": {"data": "pi_estimate"},
          "encode": {
            "update": {
              "align": {"value": "right"},
              "x": {"signal": "height", "offset": -5},
              "dy": {"value": -5},
              "fill": {"value": "black"},
              "y": {"scale": "pi_scale", "field": "value"},
              "text": {
                "signal": "'Estimate: ' + format(datum.estimate, ',.3f')"
              }
            }
          }
        }
      ],
      "axes": [
        {
          "scale": "data_point_scale",
          "orient": "bottom",
          "title": "Number of Points",
          "labelFlush": true,
          "labelOverlap": true,
          "zindex": 1
        },
        {
          "scale": "data_point_scale",
          "orient": "bottom",
          "grid": true,
          "gridScale": "pi_scale",
          "domain": false,
          "labels": false,
          "maxExtent": 0,
          "minExtent": 0,
          "ticks": false,
          "zindex": 0
        },
        {
          "scale": "pi_scale",
          "orient": "left",
          "title": "Estimated π Value",
          "labelOverlap": true,
          "zindex": 1
        },
        {
          "scale": "pi_scale",
          "orient": "left",
          "grid": true,
          "gridScale": "data_point_scale",
          "domain": false,
          "labels": false,
          "maxExtent": 0,
          "minExtent": 0,
          "ticks": false,
          "zindex": 0
        }
      ]
    }
  ],
  "scales": [
    {
      "name": "x_scale",
      "type": "linear",
      "domain": [0, 1],
      "range": "height",
      "reverse": true,
      "nice": true,
      "zero": true
    },
    {
      "name": "y_scale",
      "type": "linear",
      "domain": [0, 1],
      "range": "height",
      "nice": true,
      "zero": true
    },
    {
      "name": "data_point_scale",
      "type": "linear",
      "domain": {"data": "pi_estimates", "field": "data"},
      "range": "height",
      "reverse": true,
      "nice": false,
      "zero": true
    },
    {
      "name": "pi_scale",
      "type": "linear",
      "domain": {
        "fields": [
          [2, 4],
          {"data": "pi", "field": "value"},
          {"data": "pi_estimates", "field": "estimate"}
        ]
      },
      "range": "height",
      "nice": true,
      "zero": false
    }
  ]
}
```
