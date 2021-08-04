University of Florida Galactic SED Fitting Model, Hanson 2021
=====
version 1.0

Overview
----------
Hello, my name is Ben Hanson, and I am a student at the Colorado School of Mines, majoring in Enigneering Physics. I am a rising senior at the time of writing this, and participated in the UF Summer 2021 Astro REU with mentor Dr. Paul Torrey. This code was my REU project that I worked on for 10 weeks. The code is an Markov Chain Monte Carlo framework for fitting galactic spectral energy distributions (SEDs) and inferring simple stellar population (SSPs) parameters from the SSPs that make up that galaxy. There are models that currently do about the same thing as this model (Prospector, which was used extensively within this code), but this model has some unique capabilities, as well as some future theoretical possiblities that could make it more worthwhile to use that others. For one, this model can infer mutliple SSP metallicities, not just ages, while models like prospector can only infer a single metallicity for an entire galaxy. Theoretically, this model should be able to use Bayesian evidence to determine the number of age bins and their values that are most favorable for fitting, which is something I may want to work towards in the future. If you have any questions about certain parts of the code, or have found a bug, please email me at benjamin.hanson.2000@gmail.com. 

Installation
----------
These are a list of required modules that will most likely need installing and inserting into your PATH. I did this by pip installing, which should work fine. 

 * h5py
 * fsps (for this module, git clone the code from https://github.com/cconroy20/fsps.git, as it will have all of the files and folders you need to run Conroy's FSPS. Also, set os.environ['SPS_HOME'] to be wherever those files and folders are located so that fsps has access to them)
 * emcee
 * dynest
 * corner
 * multiprocessing
 * astro-sedpy
 * astro-prospector (if having trouble with prospector, you could also git clone that from github https://github.com/bd-j/prospector.git, and run through the demo to get used to the product.)
 * Some other astrophysics modules, but those installations should show explanatory error messages if the code doesn't run

Function Definitions
---------
All functions are fairly self-explanatory. They may not be the most pythonic in accomplishing the task that they do, but all should be able to accomplish what they are supposed to do. I will explain a few functions that are slightly less self-explanatory:

 * `creating_array` and `reading_csv`: The creating_array function calls FSPS a given number of times in order to create the spectrums at the given age and metallicity combination, and then writes all of those spectrums to a csv file. The reason for this is that FSPS takes a long time to run, so by creaing this csv, and then reading it in later, you only have to run the creation function once, and can then just read the csv in every other time you start the kernel. By defining an age and metallicity space, the code will create the FSPS spectrum from any single combination of those spaces and write them in order. The read function then reads in that csv, so make sure that whenever you call that function, the csv that you just wrote is available to your code. Rows corresponds to the number of metallicity bins, and columns corresponds to the number of age bins. 

 * `generate_random_weight_array`: This function generates a random, 2D gaussian the size of which is set by your age and metallicity spaces. The range of the gaussian is determined by the upper bounds (ub) and lower bounds (lb) that you give to it. 

 * Likelihood: All likelihood functions are currently log likelihood functions, meaning they take the log of the model vs. the target spectrum, and then compute the difference. This should result in better fits at smaller values of solar luminosity. 

 * `add_to_bank`: Add to bank can be used to write specific spectrum, weight arrays, or any other lists you want to save after the kernel restarts to a csv to be used again later. Somewhat been made obsolete by the save_variables function, but still can be used if needed.

 * `mass_to_light_calc`: The mass-to-light calculation considers that the luminosity array is in units of stellar solar luminosity/hz and that the wavelength array is units of angstrom (same as what FSPS outputs).

* `get_uncertainty` and `prospector_uncertainty`: Get uncertainty is used for finding the uncertainy in the parameter fits when using a 'sampler', such as the model does. Prospector_uncertainty is used when using prospector to fit parameters, and thus requires a 'results' variable. Both are naturally created when running either. 

Initialization
---------
- When running the code for the first time on a new kernel, always run the "Initializing Wavelength Array" cell and the "Creating Interpolate Function" cell. These variables are both used throughout the code and are required to be run at the beginning. If you want to create a new interpolate function with a larger range, higher resolution, etc, that can be done by using the 'creating_array' and 'reading_csv' functions, and by changing the metallicity and age spaces. The ranges are pretty large and work pretty well currently, however. 
- Make sure to remember to set the age and metallicity spaces to their correct arrays. A lot of  the functions use those global variables, and since most don't require them as input parameters, they could be overlooked annd cause breakdowns. 

Usage, Tips, and Tricks
---------
- Main code is meant to run on Jupyter Notebooks. I haven't actually run the code in a pure python environment but I assume because it works in Jupyter, with some configuration it would be able to run in a python editor. 
- Most of the code is pretty self-explanatory, so just follow the directions and you should be able to get the hang of it. The emcee code is configured such that it should be "crash-proof", meaning that even if the kernel crashes or the server disconnects for some reason, the 'save_variables' function matched with the saving current position function both write values to a csv and h5 file with every loop. There may require some backtrack if the code crashes in the middle of the walk, but as long as no_of_steps is kept low (say, 1000) then this shouldn't be too much of a worry. 
- By setting a path early, all of the files and images from one run are saved in the same spot for easy finding, and also for assuring that files don't get overwritten which can happen pretty easily in Jupyter.
- I've found that the best fits for the model occur after about 1,000 steps and with (5 * number of parameters fitting) walkers. This could very easily change however if your spaces change as well. 
- If you are ever unsure why a fit looks wrong, the first place always to look are the walkers. They are the best indicator to show where or why your code may be going wrong. Walkers that don't deviate from their path mean that there acceptance percentage is basically 0, which indicates a problem with priors and likelihoods, or maybe upper and lower bounds. 
- There are sections for removing metallicity and testing on observable data, but those have not been used very often and may have some bugs in them, so use them with caution
- H5 files take up a large amount of memory, so ensure that you have that space before writing them. 
- I've included some example plots in the folder labeled "Plots" in order to see what the model is capable of. 


I hope this documentation will be enough to get you started!



