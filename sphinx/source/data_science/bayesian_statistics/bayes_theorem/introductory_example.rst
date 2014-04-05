Example
=======

* W : The event that the conversation was held with a woman
* L : The event that the conversation was held with a long-haired person
* P(W) = 0.5
* P(L|W) = 0.75 (Suppose it is known that 75% of women have long hair)
* P(L|M) = 0.15 (Suppose it is known that 15% of men have long hair)

.. math::

  P(W|L) &= \frac{P(L|W)\, P(W)}{P(L)} \\
         &= \frac{P(L|W)\, P(W)}{P(L|W)\, P(W) + P(L|M)\, P(M)} \\
         &= \frac{0.75\cdot0.50}{0.75\cdot0.50 + 0.15\cdot0.50} \\
         &= \frac56 \\
         &\approx 0.83

