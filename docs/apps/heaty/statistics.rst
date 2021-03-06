Statistics
==========

Heaty provides a concept for collecting statistical data about the
heating system at runtime.

Statistics are collected for so-called zones. Each zone consists of one
or more rooms.

A simple statistics configuration for a single zone might look as follows:

::

    statistics:
      upstairs:
        rooms:
          bathroom:
          kidsroom:

This zone, named ``upstairs``, contains the rooms ``bathroom`` and
``kidsroom`` and would already work just fine. But let's see how we can
customize our zone to be really useful.


Statistical Parameters
----------------------

Heaty supports collecting different statistical parameters, which are
described in the following sections.

By default, all parameters are disabled, but you can change that by
adding the ``parameters`` section:

::

    statistics:
      upstairs:
        [...]
        parameters:
          some_parameter:
          other_parameter:

For each of these parameters, a minimum, average and maximum value is
calculated from all available values and three sensor entities to hold
the results are created in Home Assistant. The name of the sensors is
``sensor.heaty_<heaty_id>_zone_<zone_name>_<min/avg/max>_<parameter>``.
The maximum value for the ``temp_delta`` in our sample zone would end
up in ``sensor.heaty_default_zone_upstairs_max_temp_delta``, for instance.

You can then use normal Home Assistant automations to do whatever you
want with these values. A common use case would be to turn a boiler on
or off when the maximum ``temp_delta`` goes beyond some threshold.


temp_delta
~~~~~~~~~~

The difference of target and current temperature per thermostat.

This parameter supports the following configuration options:

* ``off_value``: Specify how to handle thermostats which are turned
  off. Specify either the number to assume as the delta or ``null``, which
  causes the thermostat to be excluded from statistics collection. The
  configured per-thermostat factor is not applied to this value. The
  default value is ``0``.

* ``thermostat_factors``: Specify a factor which the value from an
  individual thermostat should be multiplied with before adding it to
  the list of values. Note that this doesn't change the weighting of a
  thermostat for calculating the average, it instead changes the value
  itself. The default factor is ``1``.

  ::

      thermostat_factors:
        climate.kidsroom: 1.5

* ``thermostat_weights``: Specify how individual thermostats should be
  weighted when calculating the average value. The default weight is ``1``
  and a weight of ``0`` causes the thermostat to be excluded completely.
  You may want to use this feature to indicate that some thermostats
  are more or less important than others and have this fact reflected
  in the statistics.

  ::

      thermostat_weights:
        climate.bathroom: 0.5
        climate.hall: 0
