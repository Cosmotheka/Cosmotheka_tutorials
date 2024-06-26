# Cosmotheka Tutorials
## Mappers
### The Challenge
![](https://raw.githubusercontent.com/JaimeRZP/Cosmoteka_tutorials/master/docs/src/assets/cosmoteka_tutorials_diagram.png)

The purpose of Cosmoteka is to turn cosmological catalogs into consitently combined angular power spectra with an accurate covariance matrix. In order to do so, Cosmoteka is equipped with a series of `Mapper` classes whose role is to extract the signal map and its noise properties from a given dataset. Then, these maps and their noise properties can be passed on to the angular power spectrum modules to compute the corresponding angular power spectra and their covariance matrix. To succesfully accomplish this Cosmoteka's mappers must be:
- General enough such that they remain inter-operable while being tailored to the catalog's specific needs.
- Able to deal with the large size of modern day catalogs.
### Solutuion 1: Nested Structure
Cosmoteka achieves this by adopting a nested structure. Cosmoteka exploits the object oriented nature of the Python language to create a series of mapper classes which are subordinated to one another. ```MapperBase``` acts as the basis of all other mappers in Cosmoteka. As the parent class, ```MapperBase``` defines the public methods common to all individual mappers. Moreover, it also defines a series of abstract private methods which are then overwritten by the child mappers to perform the data processing specific to each data set. A listing of the methods can be found below:

| Function                 | Description                                                                                                                                                    |
| -----------              | :-----------                                                                                                                                                   |
| ```_get_defaults()```    | Reads the configuration file to obtain the desired resolution and coordinates. Initialises the ```NaMaster``` field, signal map, mask and beam as ```None```.   |
| ```get_signal_map()```   | If the Mapper signal map is not ```None```, returns it. Otherwise, asks ```_get_signal_map()``` to compute it.                                                 |
| ```_get_signal_map()```  | Redirects MapperBase to the specific Mapper method to compute the signal map from the catalog or otherwise.                                                    |
| ```get_mask()```         | If the Mapper mask is not ```None```, returns it. Else, asks ```_get_mask()``` to compute it.                                                                  |
| ```_get_mask()```        | Redirects MapperBase to the specific Mapper method to compute the mask from the catalog or otherwise.                                                          |
| ```get_beam()```         | If the Mapper beam is not ```None```, returns it. Else, asks ```_get_beam()``` to compute it.                                                                  |
| ```_get_beam()```        | Redirects MapperBase to the specific Mapper method to compute the beam from the catalog or otherwise.                                                          |
| ```get_nmt_signal()```   | If the Mapper ```NaMaster``` field is not ```None```, returns it. Else, asks ```_get_nmt_signal()``` to compute it.                                            |
| ```_get_nmt_signal()```  | Calls  ```get_signal_map()```,  ```get_mask()``` and  ```get_beam()``` to obtain the Mapper's signal map, mask and beam respectively. Then it passes them on to  ```Namaster``` to compute the field. |
| ```get_ell()```          | Get l range for the mapper's power spectrum given the desired configuration.                                                                                   |
| ```get_nl_coupled()```   | Returns the coupled noise power spectrum of the mapper if computed from the catalog.                                                                           |
### Solutuion 2: Caching
In addition to this, Cosmoteka must manage the large RAM memory usage (up to 100Gbs) required to load and process mordern day datasets. Cosmoteka can cache all its computations to disk, such that they can be accessed later on without the need of recomputing. Moreover, Cosmoteka generates and saves light versions of the catalogs in processes such that the full catalog is not loaded into memory again.
