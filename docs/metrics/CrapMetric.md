# CRAP Metric

  Measures the [C.R.A.P.](http://www.artima.com/weblogs/viewpost.jsp?thread=210575) (Change Risk Anti-Patterns)
  score. It is designed to analyze and predict the amount of effort, pain, and time required to maintain an existing
  body of code.

  A method with a **CRAP score** over **30** is considered *CRAPpy* (i.e., *unacceptable*, *offensive*, etc.). [2]

 Implemented by the `org.gmetrics.metric.crap.CrapMetric` class.


## CRAP Formula

  Given a Groovy method m, C.R.A.P. for m is calculated as follows:

```
  C.R.A.P.(m) = comp(m)^2 * (1 – cov(m)/100)^3 + comp(m)
```

  Where **comp(m)** is the *cyclomatic complexity* of method m, and **cov(m)** is the test code coverage provided
  by automated tests.


## Metric Properties

  The following properties can be configured for this metric within a *MetricSet*. See [Creating a MetricSet](../CreatingMetricSet) for information on the syntax of setting a metric property.

| **Property**      | **Description**                                                    | **Default Value**      |
|-------------------|--------------------------------------------------------------------|------------------------|
| *enabled*         | This `boolean` property controls whether the metric is *enabled*. If set to `false`, then the metric is not included as part of the results or the output reports. | `true`                
| *functions*       | This `List<String>` property contains the names of the functions to be calculated at the *method*, *class* and *package* levels and (potentially) included within the report(s). Valid values are: "total", "average", "minimum", "maximum" | `["total","average"]`  |
| *coverageMetric*  | The <<GMetrics>> <Metric> instance to provide code coverage data. <<GMetrics>> includes a <Cobertura>-based coverage metric. See [CoberturaLineCoverageMetric](./gmetrics-CoberturaLineCoverageMetric.html). This property is REQUIRED. | *N/A*
| *complexityMetric*| The <<GMetrics>> <Metric> instance to provide <cyclomatic complexity> data. **GMetrics** includes a [CyclomaticComplexityMetric](./CyclomaticComplexityMetric), which is used as the default provider. | An instance of [CyclomaticComplexityMetric](./CyclomaticComplexityMetric)

## Sample MetricSet Definition

  Here is a sample <MetricSet> definition that includes a <<CrapMetric>>.

```
  final COBERTURA_FILE = 'coverage/GMetrics/coverage.xml'

  metricset {
      def coberturaMetric = CoberturaLineCoverage {
          coberturaFile = COBERTURA_FILE
          functions = ['total']
      }

      CRAP {
          functions = ['total']
          coverageMetric = coberturaMetric
    }
  }
```

  Note setting the *coverageMetric* field is required.

  The **CRAP** metric is a special *composite* metric -- it uses other metrics to calculate complexity
  and code coverage. In the example above, the **CoberturaLineCoverage** metric is also included within
  the *MetricSet* being defined. Alternatively, if you define the **CoberturaLineCoverage** metric *within*
  the **CRAP** metric definition, then the **CoberturaLineCoverage** metric is not included within the
  *MetricSet*, as in the example below.

```
  final COBERTURA_FILE = 'coverage/GMetrics/coverage.xml'

  metricset {
      CRAP {
          // CoberturaLineCoverage is not included in the MetricSet
          coverageMetric = CoberturaLineCoverage {
              coberturaFile = COBERTURA_FILE
          }
    }
  }
```

  There is one annoying *known limitation* with that behavior. If you use the *Map* syntax, rather
  than the *Closure* syntax, to define the outer metric, then inner metric is also included within the
  *MetricSet*, as in the example below.

```
  metricset {
      // Both CRAP and CoberturaLineCoverage metrics are included in the MetricSet
      CRAP(coverageMetric:CoberturaLineCoverage(coberturaFile:'coverage/GMetrics/coverage.xml'))
  }
```


## References

 * **[1]** The original 2007 [blog post](http://www.artima.com/weblogs/viewpost.jsp?thread=210575) that defined the **CRAP** metric.

 * **[2]** A 2011 [blog post](http://googletesting.blogspot.com/2011/02/this-code-is-crap.html) from Alberto Savoia
   (the co-creator of the **CRAP** metric with Bob Evans), describing the formula, the motivation, and the **CRAP4J**
   tool for calculating **CRAP** score for Java code.
