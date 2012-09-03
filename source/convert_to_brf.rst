*******************************************************************
Converting to surface reflectance
*******************************************************************

The downloaded files contain data which are in scaled units of radiance.
To make meaninful comparisons and analysis, the data need to be 
converted to surface reflectance. This process will, among other things,
correct for the contribution of the atmosphere to signal. Additionally,
a good cloud and cloud shadow mask (better than that obtained by default
from the GLOVIS product) will be generated. These processes are not
straightforward, and they are computationally demanding. Thankfully,
by making use of some scripts, the operation of the processing chain is
relatively straightforward.

Pre-processing to surface reflectance using LEDAPS
--------------------------------------------------------

We make use of the `LEDAPS <http://ledapsweb.nascom.nasa.gov/>`_ tools
to conver the GLOVIS data to surface reflectance.

The script will basically scan the directory where you start it from
for GLOVIS-like files. It will then try to ascertain whether these
files have been processed (the process produces a directory, and the
script will check for the existance of the final product file in the
corresponding directory). If the file is deemed unprocessed, the script
will stage processing in one of the machines available at UCL: it will
copy the data over to the temporary storage space of the processing 
machine, and will then run the processing there. Once this is finished,
it will copy the data over the network to your user space.

Practical example
===================

For this example, it will be assumed that the data files have been
downloaded to ``~/Data/``. In this directory, you will have a number
of ETM7 and TM5 files like this ::

-rw-rw-r-- 1 ucfajlg ucfajlg 225M May 17 19:22 LE71660662011164ASN00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 242M May 17 19:27 LE71660662011292ASN00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 241M May 17 19:34 LE71660662011308ASN00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 267M May 17 19:41 LE71660662011324ASN00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 215M May 17 19:47 LE71660662011340ASN00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 172M May 18 16:25 LT51650672010146JSA00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 143M May 18 16:19 LT51650672010194MLK00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 151M May 18 13:43 LT51650672011101JSA00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 166M May 18 13:49 LT51650672011181MLK01.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 122M May 18 14:23 LT51650672011229MLK00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 188M May 18 19:51 LT51660662010025MLK01.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 183M May 18 19:55 LT51660662010041MLK01.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 120M May 18 19:39 LT51660662010137JSA00.tar.gz
-rw-rw-r-- 1 ucfajlg ucfajlg 192M May 18 20:00 LT51660662010233MLK00.tar.gz

A shell script will process each of these files (which contain the raw
satellite data) and produce a directory with the surface reflectance and
cloud masks, as well as log files etc. This script needs 4 options:

#. The path to the directory where the data sit
#. The Landsat "path"
#. The Landsat "row"
#. The year

The script is simply invoked using the following command line ::

/data/geospatial_19/ucfajlg/LEDAPS/Tanzania/process_pr_year.sh /tmp/ 166 66 2011

The script will in this case look for files in /tmp/ for path/row 166/066
and acquisitions that occurred in 2011. As it won't find any, it will
complain and exit: ::

 ls: *1660662011*.tar.gz: No such file or directory

If we then change the path to the data directory to be one were these
files exist (either where you downloaded them, or 
``/data/geospatial_19/ucfajlg/LEDAPS/Tanzania/``, where some files are
available), then you will get a bit further: ::

 $ ./process_pr_year.sh /data/geospatial_19/ucfajlg/LEDAPS/Tanzania/ 166 66 2011
 File LE71660662011084ASN00.tar.gz is already processed...
 File LE71660662011116ASN00.tar.gz is already processed...
 File LE71660662011132ASN00.tar.gz is already processed...
 File LE71660662011148ASN00.tar.gz is already processed...
 File LE71660662011164ASN00.tar.gz is already processed...
 File LE71660662011292ASN00.tar.gz is already processed...
 File LE71660662011308ASN00.tar.gz is already processed...
 File LE71660662011324ASN00.tar.gz is already processed...
 File LE71660662011340ASN00.tar.gz is already processed...
 File LT51660662011188MLK01.tar.gz is already processed...
 File LT51660662011204MLK00.tar.gz is already processed...

Those files have already been processed. Assume that we have a new
file that has not been processed. Assume also that we are in the same
directory where processed files and the new file are. Let's process 
the new file ::

/data/geospatial_19/ucfajlg/LEDAPS/Tanzania/process_pr_year.sh `pwd` 165 67 2011

Using ``pwd`` just lets the script know to use the same directory
where we are. You could easily just give it the directory where you
keep your data.

The script takes around 20-30 minutes to process one image on UCL's
computers. The results will be stored in a directory which is the
name of the image, minus the ``.tar.gz``. The file that contains the
surface reflectance is ``lndsr.x.hdf``. The file also contains a QA
field, and a band with the estimates of the retrieved aerosol optical 
depth.

Converting to useful data formats
-----------------------------------------

Once the data has been processed, it will be available in HDF format.
While this format has its advantages, it's quite cumbersome to work
with. In addition to this, a set of derived reflectance-based indices
are required to more easily distinguish fires. Finally, active fire
data from the MODIS sensors on board the TERRA and AQUA sensors will also
be mapped to the 30m TM data. These two extra datasets are

:math:`\Delta NBR`
  This combination of bands has been shown to be sensitive to defoliation caused by fires. It is expected that it will be more useful in high tree cover. The NBR of a TM image is given by :math:`NBR=\frac{B4-B7}{B4+B7}`. This layer is the temporal difference in :math:`NBR` between two dates.
  
:math:`\Delta NDVI`
  This combination is again sensitive to changes in green/photosynthetic material. The NDVI is given by :math:`NDVI=\frac{B4-B3}{B4+B3}`, and :math:`\Delta NDVI` is just the difference in NDVI between two dates.
 
It is expected that a reasonable 3 colour composite, plus the  change
ratios and active fire observations will help identify burn scars.

A single tool will process all the available Landsat data for a complete 
year. The output of this tool will be a set of virtual datasets for each
Landsat band (except 6), as well as NDVI and NBR datasets. The scripts
is very simple to use ::

  /data/geospatial_19/ucfajlg/LEDAPS/Tanzania/process_sdr.py \
  -p 166 -r 66 -y 2011 -d /data/geospatial_19/ucfajlg/LEDAPS/Tanzania/
  
In the previous line, the options are

``-p 166`` or ``--path 166``
  Path 166
  
``-r 66`` or ``--row 66``
  Row 66
  
``-y 2011`` or ``--year 2011``
  Year to consider
  
``-d /data/geospatial_19/ucfajlg/LEDAPS/Tanzania/``
  Search for Landsat files on this directory. You can ignore this option and the current directory will be searched for.
  
The above command takes a short while to complete, and there might be
some runtime warnings that can be safely ignored. The resulting set of
files is ::

  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_b01.vrt
  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_b02.vrt
  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_b03.vrt
  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_b04.vrt
  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_b05.vrt
  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_b07.vrt
  -rw-rw-r-- 1 ucfajlg ucfajlg 499M Jul  5 16:05 p166066_2011_NBR.tif
  -rw-rw-r-- 1 ucfajlg ucfajlg 475M Jul  5 16:07 p166066_2011_NDVI.tif
  -rw-rw-r-- 1 ucfajlg ucfajlg 9.1K Jul  5 16:03 p166066_2011_QA.vrt
  
The files with the ``.vrt`` extension are GDAL's virtual datasets, and
can't be opened by e.g. Envi or similar. They can however be converted
to GeoTIFF or any other formats using ``gdal_translate``::

  gdal_translate -of GTiff -co "COMPRESS=LZW" -co "INTERLEAVE=BAND" \
    -co "TILED=YES" p166066_2011_b04.vrt p166066_2011_b04.tif

Note that files will have metadata on each band detailing both the 
acquisition date, and the day of year. The band ordering increases with
day of year. The extent of the raster file is the maximum extent 
needed to accommodate all the different acquisitions, and can change
from year to year, even for the same path/row combination.

.. note:: 

   There's no :math:`\Delta NBR` calculations. It is unclear what is
   required for this at the moment.



  


.. todo::

   These tools do not yet exist. 