==========================
Plotter modules
==========================

..  module:: plotter

..  class:: Plotter

``Plotter``
===================

A base class for the BrachioGraph and PantoGraph subclasses.

This class provides all the interfaces you'll need to use with the plotter in normal use, with the
exception of ``__init__()``.

All the classes, including this base class, can be instantiated *without any arguments* and will
work for testing.

For testing with turtle graphics, you will need to use one of the subclasses.

..  automethod:: Plotter.__init__

    :param virtual:
        A virtual plotter will run in software only, and doesn't expect any attached hardware. This
        allows work and development on a machine other than a Raspberry Pi, and to run automated
        tests.
    :param turtle:
        Produces a graphical representation of the plotter and its behaviour using Python turtle
        graphics, as well as or instead of a physical plotter.
    :param turtle_coarseness:
        For use with ``turtle``; a factor, in degrees, to represent the resolution of the servos by
        rounding values. Defaults to 1˚ if not specified.
    :param bounds:
        Four numbers, indicating the area that the plotter should treat as its
        available area for drawing in. The numbers represent, in order the left, top, right and
        bottom boundaries. Defaults to usable values in the default subclass definitions.
    :param int servo_1_parked_pw: The pulse-width of servo 1 when parked.
    :param int servo_2_parked_pw: The pulse-width of servo 2 when parked.
    :param float servo_1_degree_ms: Milliseconds pulse-width difference per degree of movement.
    :param float servo_2_degree_ms: Milliseconds pulse-width difference per degree of movement.
    :param float servo_1_parked_angle: The arm angle in the parked position.
    :param float servo_2_parked_angle: The arm angle in the parked position.
    :param float hysteresis_correction_1:
        Servo 1 :ref:`hysteresis <about-hysteresis>` error compensation.
    :param float hysteresis_correction_2:
        Servo 2 hysteresis error compensation.
    :param tuple servo_1_angle_pws: Pulse-widths for various angles of servo 1.
    :param tuple servo_2_angle_pws: Pulse-widths for various angles of servo 2.
    :param tuple servo_1_angle_pws_bidi: Pulse-widths for various angles of servo 1, collected in
        both clockwise and anti-clockwise directions. This is introduced in the :ref:`tutorial
        <tutorial-sophisticated-calibration>`.
    :param tuple servo_2_angle_pws_bidi: Pulse-widths for various angles of servo 2, collected in
        both clockwise and anti-clockwise directions.
    :param int pw_up: The pulse-width for the pen's up position.
    :param int pw_down: The pulse-width for the pen's down position.
    :param float wait: A time in seconds that the plotter will rest after making a
        movement. If not specified, will be initialised as 0.1, or 0 for a virtual-only plotter.
    :param float resolution:
        A distance in centimetres. When drawing between two points, any line longer than
        ``resolution`` will be broken down into a series of points no more than ``resolution`` cm
        apart. This allows the plotter to approximate straight lines by drawing a series of shorter
        curved lines (all the lines the plotter naturally draws are curved). If not specified, will 
        be initialised as 0.1.

In all the methods below, arguments that are also attributes of the plotter class need only be used
to override those values (which is generally not required).


Plotting methods
-------------------------------

..  automethod:: Plotter.plot_file

..  automethod:: Plotter.plot_lines


Drawing methods using x/y values
--------------------------------

..  automethod:: Plotter.box

..  automethod:: Plotter.test_pattern

..  automethod:: Plotter.vertical_lines

..  automethod:: Plotter.horizontal_lines

..  automethod:: Plotter.draw_line

..  automethod:: Plotter.xy


Drawing methods using servo angle values
----------------------------------------

..  automethod:: Plotter.move_angles


Pen-moving methods
-------------------

..  automethod:: Plotter.set_angles

..  method:: Plotter.park


Angles to pulse widths
----------------------

A plotter needs to move its arms to the correct angles, by providing the appropriate
pulse-width to each servo.

..  method:: Plotter.angles_to_pw_1
..  method:: Plotter.angles_to_pw_2

These methods - one for each servo - take the angle as an argument and return a pulse-width.

The methods themselves stand in for functions that do the actual calculation; which function is
assigned to the ``angles_to_pw_1``/``angles_to_pw_2`` attributes depends upon how much
information is provided about the servos when the plotter is initialised.


Naive calculation
~~~~~~~~~~~~~~~~~

The default is to use  "naive" functions (``naive_angles_to_pulse_widths_1`` and
``naive_angles_to_pulse_widths_2``), that assume linearity (1˚ of movement corresponds to a 10µs
change in pulse-width), will be used.

..  automethod:: Plotter.naive_angles_to_pulse_widths_1

..  automethod:: Plotter.naive_angles_to_pulse_widths_2


Sophisticated calculation
~~~~~~~~~~~~~~~~~~~~~~~~~

In practice :ref:`the response of servos is not linear <explanation-non-linearity>`. If a series of
pulse-width/angle values are supplied, then numpy ``(numpy.poly1d(numpy.polyfit))`` will provide a
polynomial funtion that matches the curve corresponding to those values.


Line processing
---------------

..  automethod:: Plotter.analyse_lines

..  automethod:: Plotter.rotate_and_scale_lines


Physical control
----------------

..  automethod:: Plotter.set_pulse_widths

..  automethod:: Plotter.get_pulse_widths

..  automethod:: Plotter.quiet


Manual driving
---------------

..  automethod:: Plotter.drive

    The controls are:

    ..  list-table::
        :stub-columns: 1

        * -
          - Exit
          - -10 µs
          - -1 µs
          - \+ 10 µs
          - \+ 1 µs
        * -
          - ``0``
          -
          -
          -
          -
        * - Servo 1
          -
          - ``a``
          - ``A``
          - ``s``
          - ``S``
        * - Servo 2
          -
          - ``k``
          - ``K``
          - ``l``
          - ``L``

..  automethod:: Plotter.drive_xy

    The controls are:

    ..  list-table::
        :stub-columns: 1

        * -
          - Exit
          - -1 cm
          - -1 mm
          - \+ 1 cm
          - \+ 1 mm
        * -
          - ``0``
          -
          -
          -
          -
        * - Servo 1
          -
          - ``a``
          - ``A``
          - ``s``
          - ``S``
        * - Servo 2
          -
          - ``k``
          - ``K``
          - ``l``
          - ``L``


Reporting methods
-----------------

..  automethod:: Plotter.status


Trigonometric methods
----------------------

..  automethod:: Plotter.xy_to_angles

..  automethod:: Plotter.angles_to_xy



``BrachioGraph``
================

..  module:: brachiograph

..  automethod:: BrachioGraph.__init__

    Parameters are as for the ``Plotter`` parent class, except for:

    :param float inner_arm: The length of the inner arm, in cm.
    :param float outer_arm: The length of the outer arm, in cm.
