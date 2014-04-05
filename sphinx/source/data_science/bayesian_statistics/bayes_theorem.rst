==============
Bayes' theorem
==============

Statement
=========

* P(A) : Probablities of A
* P(B) : Probablities of B
* P(A|B) : Conditional probablities of A given B
* P(B|A) : Conditional probablities of B given A

.. math:: P(A|B) = \frac{P(B|A)\, P(A)}{P(B)}\cdot


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


Interpretations
===============

Bayesian interpretation
-----------------------

* Probability measures a *degree of belief*.
* For proposition A and evidence B,

  * P(A), the *prior*, is the initial degree of belief in A.
  * P(A|B), the *posterior*, is the degree of belief having accounted for B.
  * the quotient P(B|A)/P(B) represents the support B provides for A.

Frequentist interpretation
--------------------------

* Probability measures a *proportion of outcomes*.
* For example, suppose an experiment is performed many times.

  * P(A), the proportion of outcomes with property A.
  * P(B), that with property B.
  * P(B|A), the proportion of outcomes with property B *out of* outcomes with property A.
  * P(A|B), the proportion of those with A *out of* those with B.


Forms
=====

Events
------

Simple form
^^^^^^^^^^^

.. math:: P(A|B) = \frac{P(B | A)\, P(A)}{P(B)}\cdot
.. math:: P(A|B) \propto  P(A) \cdot P(B|A).
.. math::

  P(A|B) = c \cdot P(A) \cdot P(B|A) \ 
  and \ 
  P(\neg A|B) = c \cdot P(\neg A) \cdot P(B|\neg A)\cdot

.. math:: c = \frac{1}{P(A) \cdot P(B|A) +  P(\neg A) \cdot P(B|\neg A) }.

Extended form
^^^^^^^^^^^^^

.. math::

  P(B) = {\sum_j P(B|A_j) P(A_j)},
  
  \implies P(A_i|B) = \frac{P(B|A_i)\,P(A_i)}{\sum\limits_j P(B|A_j)\,P(A_j)}\cdot

.. math:: P(A|B) = \frac{P(B|A)\,P(A)}{ P(B|A) P(A) + P(B|\neg A) P(\neg A)}\cdot

Random variables
----------------

Simple form
^^^^^^^^^^^

.. math:: f_X(x|Y=y) = \frac{P(Y=y|X=x)\,f_X(x)}{P(Y=y)}.
.. math:: P(X=x|Y=y) = \frac{f_Y(y|X=x)\,P(X=x)}{f_Y(y)}.
.. math:: f_X(x|Y=y) = \frac{f_Y(y|X=x)\,f_X(x)}{f_Y(y)}.

Extended form
^^^^^^^^^^^^^

.. math:: f_Y(y) = \int_{-\infty}^\infty f_Y(y|X=\xi )\,f_X(\xi)\,d\xi .

Bayes' rule
-----------

.. math:: O(A_1:A_2|B) =  O(A_1:A_2) \cdot \Lambda(A_1:A_2|B)
.. math:: \Lambda(A_1:A_2|B) = \frac{P(B|A_1)}{P(B|A_2)}
.. math:: O(A_1:A_2) =  \frac{P(A_1)}{P(A_2)}
.. math:: O(A_1:A_2|B) =  \frac{P(A_1|B)}{P(A_2|B)}


Derivation
==========

For events
----------

.. math:: P(A|B)=\frac{P(A \cap B)}{P(B)}, \text{ if } P(B) \neq 0, \!
.. math:: P(B|A) = \frac{P(A \cap B)}{P(A)}, \text{ if } P(A) \neq 0, \!
.. math:: \implies P(A \cap B) = P(A|B)\, P(B) = P(B|A)\, P(A), \!
.. math:: \implies P(A|B) = \frac{P(B|A)\,P(A)}{P(B)}, \text{ if } P(B) \neq 0.

For random variables
--------------------

.. math:: f_X(x|Y=y) = \frac{f_{X,Y}(x,y)}{f_Y(y)}
.. math:: f_Y(y|X=x) = \frac{f_{X,Y}(x,y)}{f_X(x)}
.. math:: \implies f_X(x|Y=y) = \frac{f_Y(y|X=x)\,f_X(x)}{f_Y(y)}.

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


References
==========

* `Bayes' theorem - Wikipedia, the free encyclopedia <http://en.wikipedia.org/wiki/Bayes%27_theorem>`_

