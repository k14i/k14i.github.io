====================================
Elixir's new continuable enumerators
====================================

A recap of enumerators, and some terminology
============================================

ex. How you can count the total length of strings in a list
-----------------------------------------------------------

.. code-block:: elixir

  Enumerable.reduce(l, 0, fn x, acc -> String.length(x) + acc end)

ex. Define a "sum" function using "Enumerable.reduce/3", which is hidden inside another function.
-------------------------------------------------------------------------------------------------

.. code-block:: elixir

  def sum(coll) do
    Enumerable.reduce(coll, 0, fn x, acc -> x + acc end)
  end

This means:

.. code-block:: elixir

  Enum.map(1..10, &(&1 * &1)) |> sum()

or

.. code-block:: elixir

  sum(Enum.map(1..10, &(&1 * &1)))

The general pattern is this:

.. code-block:: elixir

  def outer_function(coll, ...) do
    ...
    Enumerable.reduce(coll, initial_consumer_acc, consumer)
    ...
  end
  
  something_that_returns_an_enumerable(...) |> outer_function(...)

Slightly uncommon terminology
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* outer function

  * The function to which the enumerable is passed.

* consumer ("iteratee")

  * "An iteratee consumes values."


Two issues with classic Elixir enumerators
==========================================

Enumrators' limitations
-----------------------

* It's not possible to define a function 
  that only returns at most 3 elements 
  without traversing all elements 
  or using ugly tricks
  such as "throw"

* There's no way to interleave two enumerables.

The underlying problem
----------------------

The producer is fully in control.

There's no way aside from "throw"/"raise" for a consumer to tell a producer "stop producing".

There's definitely no way to tell a producer "stop for now but be prepared to continue where you left off later".


Power to the consumer!
======================


Implementing "interleave"
=========================


Conclusion
==========


