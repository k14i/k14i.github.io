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

Instead of returning only an accumulator at every step (for every produced value) 
the consumer returns a combination of an accumulator and an instruction to the producer.

Three instructions are available:

* :cont

  * Keep producing
  * A consumer that always returns :cont makes the producer behave 
    exactly the same as in the old system.

* :halt

  * Stop producing
  * A consumer may return :halt to have the producer terminate 
    earlier than it normally would.

* :suspend

  * Temporarily stop producing.
  * tells a producer to return the accumulator and a continuation function.

.. code-block:: elixir

  { :suspended, n_, cont } = Enumerable.reduce(1..5, { :cont, 0 }, fn x, n ->
    if x == 3 do
      { :suspend, n }
    else
      { :cont, n + x }
    end
  end)

Implementing "interleave"
=========================

.. code-block:: elixir

  defmodule Interleave do
    def interleave(a, b) do
      step = fn x, acc -> { :suspend, [x|acc] } end
      af = &Enumerable.reduce(a, &1, step)
      bf = &Enumerable.reduce(b, &1, step)
      do_interleave(af, bf, []) |> :lists.reverse()
    end
  
    defp do_interleave(a, b, acc) do
      case a.({ :cont, acc }) do
        { :suspended, acc, a } ->
          case b.({ :cont, acc }) do
            { :suspended, acc, b } ->
              do_interleave(a, b, acc)
            { :halted, acc } ->
              acc
            { :done, acc } ->
              finish_interleave(a, acc)
          end
        { :halted, acc } ->
          acc
        { :done, acc } ->
          finish_interleave(b, acc)
      end
    end
  
    defp finish_interleave(a_or_b, acc) do
      case a_or_b.({ :cont, acc }) do
        { :suspended, acc, a_or_b } ->
          finish_interleave(a_or_b, acc)
        { _, acc } ->
          acc
      end
    end
  end
  
  Interleave.interleave([1,2], [:a, :b, :c, :d])
  #=> [1, :a, 2, :b, :c, :d]

Conclusion
==========


