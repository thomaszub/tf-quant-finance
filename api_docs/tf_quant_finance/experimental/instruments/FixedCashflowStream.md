<div itemscope itemtype="http://developers.google.com/ReferenceObject">
<meta itemprop="name" content="tf_quant_finance.experimental.instruments.FixedCashflowStream" />
<meta itemprop="path" content="Stable" />
<meta itemprop="property" content="__init__"/>
<meta itemprop="property" content="price"/>
</div>

# tf_quant_finance.experimental.instruments.FixedCashflowStream

<!-- Insert buttons and diff -->

<table class="tfo-notebook-buttons tfo-api" align="left">
</table>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/experimental/instruments/cashflow_stream.py">View source</a>



Represents a batch of fixed stream of cashflows.

```python
tf_quant_finance.experimental.instruments.FixedCashflowStream(
    start_date, end_date, coupon_spec, first_coupon_date=None,
    penultimate_coupon_date=None, dtype=None, name=None
)
```



<!-- Placeholder for "Used in" -->

#### Example:
The following example illustrates the construction of an FixedCashflowStream
and calculating its present value.

```python
import numpy as np
import tensorflow as tf
import tf_quant_finance as tff
dates = tff.experimental.dates
instruments = tff.experimental.instruments
rc = tff.experimental.instruments.rates_common

dtype = np.float64
start_date = dates.convert_to_date_tensor([(2020, 2, 2)])
maturity_date = dates.convert_to_date_tensor([(2023, 2, 2)])
valuation_date = dates.convert_to_date_tensor([(2020, 2, 2)])
period_6m = dates.periods.PeriodTensor(6, dates.PeriodType.MONTH)
fix_spec = instruments.FixedCouponSpecs(
            coupon_frequency=period_6m, currency='usd',
            notional=1.e6, coupon_rate=0.03134,
            daycount_convention=rc.DayCountConvention.ACTUAL_365,
            businessday_rule=dates.BusinessDayConvention.NONE)

cf_stream = instruments.FixedCashflowStream([start_date], [maturity_date],
                                            [fix_spec], dtype=dtype)

curve_dates = valuation_date + dates.periods.PeriodTensor(
      [1, 2, 3, 5, 7, 10, 30], dates.PeriodType.YEAR)
reference_curve = instruments.RateCurve(
    curve_dates,
    np.array([
      0.02834814, 0.03077457, 0.03113739, 0.03130794, 0.03160892,
      0.03213901, 0.03257991
      ], dtype=dtype),
    dtype=dtype)
market = instruments.InterestRateMarket(
    reference_curve=reference_curve, discount_curve=reference_curve)

price = cf_stream.price(valuation_date, market)
# Expected result: 89259.267853547
```

#### Args:


* <b>`start_date`</b>: A rank 1 `DateTensor` specifying the starting dates of the
  accrual of the first coupon of the cashflow stream. The shape of the
  input correspond to the numbercof streams being created.
* <b>`end_date`</b>: A rank 1 `DateTensor` specifying the end dates for accrual of
  the last coupon in each cashflow stream. The shape of the input should
  be the same as that of `start_date`.
* <b>`coupon_spec`</b>: A list of `FixedCouponSpecs` specifying the details of the
  coupon payment for the cashflow stream. The length of the list should
  be the same as the number of streams being created. Each coupon within
  the list must have the same daycount_convention and businessday_rule.
* <b>`first_coupon_date`</b>: An optional rank 1 `DateTensor` specifying the payment
  dates of the first coupon of the cashflow stream. Use this input for
  cashflows with irregular first coupon.
  Default value: None which implies regular first coupon.
* <b>`penultimate_coupon_date`</b>: An optional rank 1 `DateTensor` specifying the
  payment date of the penultimate (next to last) coupon of the cashflow
  stream. Use this input for cashflows with irregular last coupon.
  Default value: None which implies regular last coupon.
* <b>`dtype`</b>: `tf.Dtype`. If supplied the dtype for the real variables or ops
  either supplied to the FixedCashflowStream object or created by the
  object.
  Default value: None which maps to the default dtype inferred by
  TensorFlow.
* <b>`name`</b>: Python str. The name to give to the ops created by this class.
  Default value: `None` which maps to 'fixed_cashflow_stream'.

#### Attributes:

* <b>`contract_index`</b>
* <b>`daycount_fractions`</b>
* <b>`fixed_rate`</b>
* <b>`payment_dates`</b>


## Methods

<h3 id="price"><code>price</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/experimental/instruments/cashflow_stream.py">View source</a>

```python
price(
    valuation_date, market, model=None, name=None
)
```

Returns the present value of the stream on the valuation date.


#### Args:


* <b>`valuation_date`</b>: A scalar `DateTensor` specifying the date on which
  valuation is being desired.
* <b>`market`</b>: A namedtuple of type `InterestRateMarket` which contains the
  necessary information for pricing the cashflow stream.
* <b>`model`</b>: Reserved for future use.
* <b>`name`</b>: Python str. The name to give to the ops created by this function.
  Default value: `None` which maps to 'price'.


#### Returns:

A Rank 1 `Tensor` of real type containing the modeled price of each stream
based on the input market data.



