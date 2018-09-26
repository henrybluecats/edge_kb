 # Versioning and naming

For the purpose of distinguishing versions from each other by reading the version string,
Here I define the version  major strings for all firmware stacks:

 | Firmware Stack | Version major string |
 |---------------|-----------------|
 |BC51X AC Stack| merge to BC51X Stack|
 |BC51X HS Stack| 0.8.N |
 |BC51X Stack| 0.5.N |
 |BC51X Stack - TEST | 0.15.N |
 |BC51X LTE LE Stack | 0.6.N |
 |BC51X LTE LE Stack - TEST | 0.16.N |
 |BC51X LE Stack | 0.7.N |
 |BC51X LE Stack - TEST | 0.17.N |
 |BC51X Legacy Stack | 0.4.X |
 
 * N,X = deximal(FWUID)
 
 ## For example
 FWUID is `0x31`, then the firmware on `BC51X Stack` is `0.5.49`. So the last part in the version string is alway increasing along with FWUID whatever the middle part of the version string is. 
 
 Suppose we get an issue report whose edge version string is `0.16.48`, at the sight of the version string, we know the firemware comes from `BC51X LTE LE Stack - TEST` and its FWUID is `0x30`.
 
