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

