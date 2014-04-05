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

