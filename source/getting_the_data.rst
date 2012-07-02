*****************************
Getting hold of the data
*****************************

The first step is to get hold of all available data. In principle, 
we will use the `GLOVIS website <http://glovis.usgs.gov>`_ from the 
USGS to list all available acquisitions from both Landsat 5 TM and 
Landsat 7 ETM, order and download the data.

.. image:: snapshot1.png
   :alt: The main GLOVIS webpage
   
It is useful if you create an account on this website (click on 
``Register`` for the typical website account setting up).
   
The region of interest extends the following area:

NE corner 
  8d11'53"S, 39d33'42"E
NW corner
  8d21'11"S, 38d33'30"E
SW corner
  9d58'37"S, 38d38'10"E
SE corner
  9d55'02"S, 39d55'06"E

This area is covered by multiple TM scenes, which will probably make
it easier to locate acquisitions. The path and row can be indicated on
the WRS-2 Path/Row entry box just below the world map on the left
hand site of the GLOVIS window:

* 165/66
* 165/67
* 166/66
* 166/67

Before selecting the scene, it's useful to set the ``Max Cloud`` option
to 100%. This setting allows for limiting results that may be too cloudy, 
but this is irrelevant for this project, so set it to 100% (effectively
ignore it). Enter the path/row combinations on the box and press 
``Go``. The big display window on the right hand side will show some
recent acquisitions over the area of interest, with the central scene
bordered in yellow. In the bottom left of the screen, you can read some
scene information.

The process is then fairly straightforward. For each path & row:

#. Go to the beggining of the time period, and get the first available acquisitions

#. Click on "Add" (at the bottom of the ``L4-7 Combined Scene List`` widget)

#.  Make a note of the scene ID number (in the ``Scene Information`` box), as well as of the acquisition data, and path/row information.

#. Go to the next scene (``Next Scene`` button)

#. Repeat until you have all the available scenes.

#. Click on ``Send to Cart``.

.. note::

  It might be worthwile to do this by year, bagging all the scenes in 
  a given year. That way, if there's any problem or crash, only one year
  needs to be re-entered.
  
Once the the added scenes have been set to the cart, a new window
will open. There will be a prompt to either use your GLOVIS account,
or some way of inputting your email address. This is required, as 
most of the data is not directly accessible, but needs to be staged
for downloading. This process can take a couple of days to clear, but
most often only takes a few hours. You will get email alerts directing
you to the files to download when they are ready. 

Once you get the confirmation emails, you can proceed to download the
files. It is recommended that you download them using a command-line
tool such as `wget <http://www.gnu.org/software/wget/manual/wget.html>`_ 
or `curl <http://curl.haxx.se/docs/manpage.html>`_. These are 
installed in the machines in the UCL system, so you would just change 
directory to your work space (``cd ~/Data/``, for example), and issue
the commands required there. This will download the relevant files in
that directory.

.. note::

  There are plenty of good resources on the Internet on using ``wget``
  or ``curl`` for automated downloading. See e.g. 
  `this site <http://daipratt.co.uk/wget-tricks-tips/>`_ or 
  `Wikipedia <http://en.wikipedia.org/wiki/Wget#Using_Wget>`_.
