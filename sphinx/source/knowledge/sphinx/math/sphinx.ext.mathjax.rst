==================
sphinx.ext.mathjax
==================

Setup
=====

::

  wget https://bitbucket.org/kevindunn/sphinx-extension-mathjax/raw/812aa178b29e22ee0b09a4e2d0a6052538c4590b/mathjax.py

conf.py

::

  extensions.append('sphinx.ext.mathjax')
  mathjax_path = 'http://mathjax.connectmv.com/MathJax.js?config=default'


Usage
=====

::

  ..math:: {mathematical expression}

::

  .. math::
  
    {mathematical expression}

::

  .. math::
  
    {mathematical expression 1}
  
    {mathematical expression 2}
  
    {mathematical expression 3}
  
    ...
  
    {mathematical expression N}


Examples
========

::

  .. math:: E = mc^2

.. math:: E = mc^2

::

  .. math::
  
    (a + b)^2 = a^2 + 2ab + b^2
  
    (a - b)^2 = a^2 - 2ab + b^2

.. math::

  (a + b)^2 = a^2 + 2ab + b^2
  
  (a - b)^2 = a^2 - 2ab + b^2

::

  .. math::
  
    (a + b)^2  &=  (a + b)(a + b) \\
               &=  a^2 + 2ab + b^2

.. math::

   (a + b)^2  &=  (a + b)(a + b) \\
              &=  a^2 + 2ab + b^2

::

  .. math::
    :nowrap:
  
    \begin{eqnarray}
      y    & = & ax^2 + bx + c \\
      f(x) & = & x^2 + 2xy + y^2
    \end{eqnarray}

.. math::
  :nowrap:
  
  \begin{eqnarray}
    y    & = & ax^2 + bx + c \\
    f(x) & = & x^2 + 2xy + y^2
  \end{eqnarray}

::

  .. math::
  
    \frac{1}{1^0} + \log{\exp{1}} = \sum_{i=0}^{9} 0.1

.. math::

  \frac{1}{1^0} + \log{\exp{1}} = \sum_{i=0}^{9} 0.1


References
==========

* `Sphinx extension: mathjax <https://bitbucket.org/kevindunn/sphinx-extension-mathjax/wiki/Home>`_
* `Math support in Sphinx <http://sphinx-doc.org/latest/ext/math.html>`_

