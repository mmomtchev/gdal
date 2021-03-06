.. _gdal_calc:

================================================================================
gdal_calc.py
================================================================================

.. only:: html

    Command line raster calculator with numpy syntax.

.. Index:: gdal_calc

Synopsis
--------

.. code-block::

    gdal_calc.py --calc=expression --outfile=out_filename [-A filename]
                 [--A_band=n] [-B...-Z filename] [other_options]

.. rubric::  DESCRIPTION
   :name: description

Command line raster calculator with numpy syntax. Use any basic
arithmetic supported by numpy arrays such as ``+``, ``-``, ``*``, and
``\`` along with logical operators such as ``>``. Note that all files
must have the same dimensions, but no projection checking is
performed.

.. option:: --help

    Show this help message and exit

.. option:: -h

    The same as :option:`--help`.

.. option:: --calc=expression

    Calculation in gdalnumeric syntax using ``+``, ``-``, ``/``, ``*``, or any numpy array functions (i.e. ``log10()``).
    Multiple ``--calc`` options can be listed to produce a multiband file (GDAL >= 3.2).

.. option:: -A <filename>

    Input gdal raster file, you can use any letter (A-Z).

.. option:: --A_band=<n>

    Number of raster band for file A (default 1).

.. option::  --outfile=<filename>

    Output file to generate or fill.

.. option:: --NoDataValue=<value>

    Output nodata value (default datatype specific value).

.. option:: --type=<datatype>

    Output datatype, must be one of [``Int32``, ``Int16``, ``Float64``, ``UInt16``, ``Byte``, ``UInt32``, ``Float32``].
 
    .. note::
    
       Despite the datatype set using ``--type``, when doing intermediate aritmethic operations using operands of the
       same type, the operation result will honor the original datatype. This may lead into unexpected results in the final result.
    
.. option:: --format=<gdal_format>

    GDAL format for output file.


.. _creation-option:

.. option:: --creation-option=<option>

    Passes a creation option to the output format driver.  Multiple
    options may be listed. See format specific documentation for legal
    creation options for each format.

.. option:: --co=<option>

        The same as creation-option_.

.. option:: --allBands=[A-Z]

    Process all bands of given raster (A-Z). Requires a single calc for all bands.

.. option:: --overwrite

    Overwrite output file if it already exists.

.. option:: --debug

    Print debugging information.

.. option:: --quiet

    Suppress progress messages.

Example
-------

Add two files together:

.. code-block::

    gdal_calc.py -A input1.tif -B input2.tif --outfile=result.tif --calc="A+B"

Average of two layers:

.. code-block::

    gdal_calc.py -A input.tif -B input2.tif --outfile=result.tif --calc="(A+B)/2"
    
.. note::

   In the previous example, beware that if A and B inputs are of the same datatype, for example integers, you
   may need to force the convertion of one of the operands before the division operation.
   
   .. code-block::

      gdal_calc.py -A input.tif -B input2.tif --outfile=result.tif --calc="(A.astype(numpy.float64) + B) / 2"

Set values of zero and below to null:

.. code-block::

    gdal_calc.py -A input.tif --outfile=result.tif --calc="A*(A>0)" --NoDataValue=0
    
Using logical operator to keep a range of values from input:

.. code-block::

    gdal_calc.py -A input.tif --outfile=result.tif --calc="A*logical_and(A>100,A<150)"

Work with multiple bands:

.. code-block::

    gdal_calc.py -A input.tif --A_band=1 -B input.tif --B_band=2 --outfile=result.tif --calc="(A+B)/2" --calc="B*logical_and(A>100,A<150)"
