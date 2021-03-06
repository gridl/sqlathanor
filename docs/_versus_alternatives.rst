Since :term:`serialization` and :term:`de-serialization` are common problems,
there are a variety of alternative ways to serialize and de-serialize your
`SQLAlchemy`_ models. Obviously, I'm biased in favor of **SQLAthanor**. But
it might be helpful to compare **SQLAthanor** to some commonly-used alternatives:

.. tabs::

  .. tab:: Rolling Your Own

    Adding your own custom serialization/de-serialization logic to your `SQLAlchemy`_
    declarative models is a very viable strategy. It's what I did for years,
    until I got tired of repeating the same patterns over and over again,
    and decided to build **SQLAthanor** instead.

    But of course, implementing custom serialization/de-serialization logic takes
    a bit of effort.

    .. tip::

      **When to use it?**

      In practice, I find that rolling my own solution is great when it's a simple
      model with very limited business logic. It's a "quick-and-dirty" solution,
      where I'm trading rapid implementation (yay!) for less
      flexibility/functionality (boo!).

      Considering how easy **SQLAthanor** is to configure / apply, however, I
      find that I never really roll my own serialization/de-serialization approach
      when working `SQLAlchemy`_ models any more.

  .. tab:: Marshmallow

    The `Marshmallow`_ library and its `Marshmallow-SQLAlchemy`_ extension are
    both fantastic. However, they have one major architectural difference to
    **SQLAthanor** and several more minor differences:

    The biggest difference is that by design, they force you to maintain *two*
    representations of your data model. One is your `SQLAlchemy`_
    :term:`model class`, while the other is your `Marshmallow`_ schema (which
    determines how your model is serialized/de-serialized). `Marshmallow-SQLAlchemy`_
    specifically tries to simplify this by generating a schema based on your
    :term:`model class`, but you still need to configure, manage, and maintain
    both representations - which as your project gets more complex, becomes
    non-trivial.

    **SQLAthanor** by contrast lets you configure serialization/deserialization
    **in** your `SQLAlchemy`_ :term:`model class` definition. You're only maintaining
    *one* data model representation in your Python code, which is a massive
    time/effort/risk-reduction.

    Other notable differences relate to the API/syntax used to support
    non-:term:`JSON <JavaScript Object Notation (JSON)>` formats. I think `Marshmallow`_
    uses a non-obvious approach, while with **SQLAthanor** the APIs are clean and simple.
    Of course, on this point, YMMV.

    .. tip::

      **When to use it?**

      `Marshmallow`_ has one advantage over **SQLAthanor**: It can serialize/de-serialize
      *any* Python object, whether it is a `SQLAlchemy`_ model class or not.
      **SQLAthanor** only works with `SQLAlchemy`_.

      As a result, it may be worth using `Marshmallow`_ instead of **SQLAthanor**
      if you expect to be serializing / de-serializing a lot of non-`SQLAlchemy`_
      objects.

  .. tab:: Colander

    The `Colander`_ library and the `ColanderAlchemy`_ extension are both great,
    but they have a similar *major* architectural difference to **SQLAthanor** as
    `Marshmallow`_/`Marshmallow-SQLAlchemy`_:

    By design, they force you to maintain *two* representations of your data model.
    One is your `SQLAlchemy`_ :term:`model class`, while the other is your
    `Colander`_ schema (which determines how your model is serialized/de-serialized).
    `ColanderAlchemy`_ tries to simplify this by generating a schema based on your
    :term:`model class`, but you still need to configure, manage, and maintain
    both representations - which as your project gets more complex, becomes
    non-trivial.

    **SQLAthanor** by contrast lets you configure serialization/deserialization
    **in** your `SQLAlchemy`_ :term:`model class` definition. You're only maintaining
    *one* data model representation in your Python code, which is a massive
    time/effort/risk-reduction.

    A second major difference is that, again by design, `Colander`_ is designed
    to serialize/de-serialize Python objects to a set of Python primitives. Since
    neither :term:`JSON <JavaScript Object Notation (JSON)>`,
    :term:`CSV <Comma-Separated Value (CSV)>`, or
    :term:`YAML <YAML Ain't a Markup Language (YAML)>` are Python primitives, you'll
    still need to serialize/de-serialize `Colander`_'s input/output to/from its
    final "transmissable" form. Once you've got a Python primitive, this isn't
    difficult - but it is an extra step.

    .. tip::

      **When to use it?**

      `Colander`_ has one advantage over **SQLAthanor**: It can serialize/de-serialize
      *any* Python object, whether it is a `SQLAlchemy`_ model class or not.
      **SQLAthanor** only works with `SQLAlchemy`_.

      As a result, it may be worth using `Colander`_ instead of **SQLAthanor**
      if you expect to be serializing / de-serializing a lot of non-`SQLAlchemy`_
      objects.

  .. tab:: pandas

    `pandas`_ is one of my favorite analytical libraries. It has a number of
    great methods that adopt a simple syntax, like ``read_csv()`` or ``to_csv()``
    which de-serialize / serialize data to various formats (including SQL, JSON, CSV,
    etc.).

    So at first blush, one might think: Why not just use `pandas`_ to handle
    serialization/de-serialization?

    Well, `pandas`_ isn't really a serialization alternative to **SQLAthanor**.
    More properly, it is an ORM alternative to `SQLAlchemy`_ itself.

    I could write (and `have written <https://www.reddit.com/r/Python/comments/90jxnv/sqlathanor_serialization_deserialization_for/e2s8aeh/>`_)
    a lot on the subject, but the key difference is that `pandas`_ is a "lightweight"
    ORM that focuses on providing a Pythonic interface to work with the output
    of single SQL queries. It does not support complex relationships between tables,
    or support the abstracted definition of business logic that applies to an
    object representation of a "concept" stored in your database.

    `SQLAlchemy`_ is *specifically* designed to do those things.

    So you can think of `pandas`_ as being a less-abstract, "closer to bare metal"
    ORM - which is what you want if you want very efficient computations, on
    relatively "flat" (non-nested/minimally relational) data. Modification or
    manipulation of the data can be done by mutating your `pandas`_ ``DataFrame``
    without *too much* maintenance burden because those mutations/modifications
    probably don't rely too much on complex abstract business logic.

    **SQLAthanor** piggybacks on `SQLAlchemy`_'s business logic-focused ORM
    capabilities. It is designed to allow you to configure expected behavior *once*
    and then re-use that capability across all instances (records) of your data.
    And it's designed to play well with all of the other complex abstractions that
    `SQLAlchemy`_ supports, like :term:`relationships <relationship>`,
    :term:`hybrid properties <hybrid property>`, :ref:`reflection <using_reflection>`,
    or :term:`association proxies <association proxy>`.

    `pandas`_ serialization/de-serialization capabilities can only be configured
    "at use-time" (in the method call), which leads to a higher maintenance burden.
    **SQLAthanor**'s serialization/de-serialization capabilities are specifically
    designed to be configurable when defining your data model.

    .. tip::

      **When to use it?**

      The decision of whether to use `pandas`_ or `SQLAlchemy`_ is a complex one,
      but in my experience a good rule of thumb is to ask yourself whether you're
      going to need to apply complex business logic to your data.

      The more complex the business logic is, the more likely `SQLAlchemy`_ will
      be a better solution. And *if* you are using `SQLAlchemy`_, then **SQLAthanor**
      provides great and easy-to-use serialization/de-serialization capabilities.

.. _Marshmallow: https://marshmallow.readthedocs.io/en/3.0/
.. _Marshmallow-SQLAlchemy: https://marshmallow-sqlalchemy.readthedocs.io/en/latest/
.. _Colander: https://docs.pylonsproject.org/projects/colander/en/latest/
.. _ColanderAlchemy: https://colanderalchemy.readthedocs.io/en/latest/
.. _pandas: http://pandas.pydata.org/
