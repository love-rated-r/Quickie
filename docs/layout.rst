Layout
======

.. note::
  Still under construction...

Immediate Mode Layouts
----------------------

.. function:: reset(x,y, pad_x, pad_y)

   :param numbers x,y: Origin of the layout (optional).
   :param pad_x,pad_y: Cell padding (optional).

Reset the layout, puts the origin at ``(x,y)`` and sets the cell padding to
``pad_x`` and ``pad_y``.

.. function:: padding(pad_x,pad_y)

   :param pad_x,pad_y: Cell padding.

Sets the cell padding to ``pad_x`` and ``pad_y``.

.. function:: push(x,y)

   :param numbers x,y: Origin of the inner layout (optional).

Saves the layout state (position, padding, sizes, etc.) on a stack and resets
the layout with position ``(x,y)``.

Used for nested row/column layouts.

.. function:: pop()

Restores the layout parameters from the stack and advances the layout position
according to the size of the popped layout.

Used for nested row/column layouts.

.. function:: row(w,h)

   :param mixed w,h: Cell width and height (optional).
   :returns: Position and size of the cell: ``x,y,w,h``.

Creates a new cell below the current cell with width ``w`` and height ``h``. If
either ``w`` or ``h`` is omitted, the value is set the last used value. Both
``w`` and ``h`` can be a string, which takes the following meaning:

``max``
   Maximum of all values since the last reset.

``min``
   Mimimum of all values since the last reset.

``median``
   Median of all values since the last reset.

Used to provide the last four arguments to a widget, e.g.::

    suit.Button("Start Game", suit.layout.row(100,30))
    suit.Button("Options", suit.layout.row())
    suit.Button("Quit", suit.layout.row(nil, "median"))

.. function:: col(w,h)

   :param mixed w,h: Cell width and height (optional).
   :returns: Position and size of the cell: ``x,y,w,h``.

Creates a new cell right to the current cell with width ``w`` and height ``h``.
If either ``w`` or ``h`` is omitted, the value is set the last used value. Both
``w`` and ``h`` can be a string, which takes the following meaning:

``max``
   Maximum of all values since the last reset.

``min``
   Mimimum of all values since the last reset.

``median``
   Median of all values since the last reset.

Used to provide the last four arguments to a widget, e.g.::

    suit.Button("OK", suit.layout.col(100,30))
    suit.Button("Cancel", suit.layout.col("max"))


Predefined Layouts
------------------

Apart from immediate mode layouts, you can specify layouts in advance.
The specification is a table of tables, where each inner table follows the
convention of :func:`row` and :func:`col`.
The result is a layout definition object that can be used to access the cells.

There is almost only one reason to do so: You know the area of your layout in
advance (say, the screen size), and want certain cells to dynamically fill the
available space.

.. note::
    Unlike immediate mode layouts, predefined layouts **can not be nested**.

Layout Specifications
^^^^^^^^^^^^^^^^^^^^^

Layout specifications are tables of tables, where the each inner table
corresponds to a cell. The inner tables define the width and height of the cell
according to the rules of :func:`row` and :func:`col`, with one additonal
keyword:

``fill``
   Fills the available space, determined by ``min_height`` or ``min_width`` and
   the number of cells with property ``fill``.

For example, this row specification makes the height of the second cell to
``(300 - 50 - 50) / 1 = 200``::

    {min_height = 300,
        {100, 50},
        {nil, 'fill'},
        {nil, 50},
    }

This column specification divides the space evenly among two cells::

    {min_width = 300,
        {'fill', 100}
        {'fill'}
    }

Apart from ``min_height`` and ``min_width``, layout specifications can also
define the position (upper left corner) of the layout using the ``pos`` keyword::

    {min_width = 300, pos = {100,100},
        {'fill', 100}
        {'fill'}
    }

Layout Definition Objects
^^^^^^^^^^^^^^^^^^^^^^^^^

Once constructed, the layout can be executed using a layout definition object
in two ways:

- Using iterators::

    for i, x,y,w,h in definition() do
        suit.Button("Button "..i, x,y,w,h)
    end

- Using the ``cell(i)`` accessor::

    suit.Button("Button 1", definition.cell(1))
    suit.Button("Button 3", definition.cell(3))
    suit.Button("Button 2", definition.cell(2))

Constructors
^^^^^^^^^^^^

.. function:: rows(spec)

   :param table spec: Layout specification.
   :returns: Layout definition object.

Defines a row layout.

.. function:: cols(spec)

   :param table spec: Layout specification.
   :returns: Layout definition object.

Defines a column layout.
