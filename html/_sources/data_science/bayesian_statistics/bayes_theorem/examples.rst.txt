Examples
========

Frequentist example
-------------------

.. math::

  P(\text{Rare}|\text{Pattern}) &=
  \frac{P(\text{Pattern}|\text{Rare})P(\text{Rare})} {P(\text{Pattern}|\text{Rare})P(\text{Rare}) \, + \, P(\text{Pattern}|\text{Common})P(\text{Common})} \\
  &= \frac{0.98 \times 0.001} {0.98 \times 0.001 + 0.05 \times 0.999} \\
  &\approx 1.9\%.

Coin flip example
-----------------

.. math::

  P(\text{Biased coin}) &= \frac{1}{3} \\
  P(\text{Fair coin}) &= \frac{2}{3} \\
  P(\text{H}|\text{Fair coin}) &= \frac{1}{2} \\
  P(\text{HHH}|\text{Fair coin}) &= \frac{1}{8} \\
  P(\text{HHH}|\text{Biased coin}) &= 1 \\
  P(\text{Biased coin}|\text{HHH}) &= \frac{P(\text{HHH}|\text{Biased coin})P(\text{Biased coin})}{P(\text{HHH}|\text{Biased coin})P(\text{Biased coin}) + P(\text{HHH}|\text{Fair coin})P(\text{Fair coin})} \\
  &= \frac{1 \times \frac{1}{3}}{1 \times \frac{1}{3} + \frac{1}{8} \times \frac{2}{3}} \quad = \quad \frac{\frac{1}{3}}{\frac{10}{24}} \quad = \quad \frac{4}{5}

Drug testing
------------

.. math::

  P(\text{User}|\text{+}) &= \frac{P(\text{+}|\text{User}) P(\text{User})}{P(\text{+}|\text{User}) P(\text{User}) + P(\text{+}|\text{Non-user}) P(\text{Non-user})} \\
  &= \frac{0.99 \times 0.005}{0.99 \times 0.005 + 0.01 \times 0.995} \\
  &\approx 33.2\%

