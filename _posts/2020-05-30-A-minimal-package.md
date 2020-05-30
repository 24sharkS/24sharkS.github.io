---
published: false
---
## Testing automated conversion in reticulate and wrapper classes using R6Class. 

This blog features an attempt to try out the automated conversion of python to r data types and vice versa (enabled by convert = TRUE when importing pyopenms module). I have created R6 classes to wrap the corresponding pyopenms class object. The private section of this class contains the python object and the public section has wrapper functions for pyopenms class which make use of the python object.
The motivation for adopting this approach was for abstraction of type conversion details and restrict the user from handling the underlying python object as much as possible.

A limited number of pyopenms classes, with selective functions, have been wrapped to demonstrate a few common use cases like file input/output, getting spectra list of an experiment and setting or extracting the peaks (m/z and Intensity values) for a spectrum.


Currently the included classes are:
1. **MzMLFile**,**FeatureXMLFile**,**IdXMLFile** which have **load() and store()** functions.
2. **MSExperiment** with functions -> **getMSLevels(),getNrSpectra(),getSpectra(),getSpectrum(),setSpectra() and size()**.
3. **MSSpectrum** with functions -> **getMSLevel(),setMSLevel(),getRT(),setRT(),get_peaks(),size() and set_peaks()**.
4. **FeatureMap** with function **getFeature()** to access specific feature by index. As FeatureMap is one of the many classes which support iteration, this function was made taking the fact into account.
5. **Feature**

Apart from the wrapper functions, setter and getter methods (```set_py_obj() & get_py_obj()```) are also present in some classes to handle the underlying python object. This is one of the drawbacks as it weakens the abstraction.


## Type Conversion.
Reticulate converts the R data types into equivalent python types when passed to a function. Similarly, when values are returned from Python to R they are converted back to R types.

We don't need to convert the values returned from a function. But, we need to perform explicit to and fro conversion in case where the passed argument is modified. For example, consider the implementation of function ```load()``` of IdXMLFile.
![load.JPG]({{site.baseurl}}/_posts/load.JPG)
Here, if **protein_ids** and **peptide_ids** lists are passed directly, then the problem is that reticulate first converts these to python lists and then passes these new objects to the function. Thus, only the converted python lists will get modified.

We need to save the reference to the converted python list, in order to reflect back the changes in the R list.

For setting or extracting peaks, we don't need to do explicit conversion. The python tuple of two numpy arrays get converted to an R list with two arrays and vice versa.
![peaks.JPG]({{site.baseurl}}/_posts/peaks.JPG)
Here, using the wrapped python object we call the respective functions passing the arguments directly.

For functions using integer parameter, we need to explicitly coerce the argument to integer as otherwise reticulate will convert it as float since by default, the internal type of any integral value in R is double unless specified by "L".
![coercion.JPG]({{site.baseurl}}/_posts/coercion.JPG)





