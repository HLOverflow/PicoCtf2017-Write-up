
# Leaf of the Tree [ 20 Pts ]

## Problem

We found this annoyingly named directory tree starting at /problems/a47d10dd80018fc6e7e1c5094c1ca323. It would be pretty lame to type out all of those directory names but maybe there is something in there worth finding? And maybe we dont need to type out all those names...? Follow the trunk, using cat and ls!

## Approach

One can ls -r and you will see a file name called flag.
An even nicer way without having to type the entire path would be to use the find command with exec option.

## Output

```
XXXXXXX@shell-web:/problems/a47d10dd80018fc6e7e1c5094c1ca323$ ls -laRt
.:
total 48
drwxr-x--x 573 root root 36864 Apr 16  2017 ..
drwxr-xr-x   3 root root  4096 Mar 31  2017 .
drwxr-xr-x   3 root root  4096 Mar 31  2017 trunk


./trunk:
total 12
drwxr-xr-x 3 root root 4096 Mar 31  2017 .
drwxr-xr-x 3 root root 4096 Mar 31  2017 ..
drwxr-xr-x 3 root root 4096 Mar 31  2017 trunkbe9c


./trunk/trunkbe9c:
total 12
drwxr-xr-x 3 root root 4096 Mar 31  2017 .
drwxr-xr-x 3 root root 4096 Mar 31  2017 ..
drwxr-xr-x 4 root root 4096 Mar 31  2017 trunk8ec3


./trunk/trunkbe9c/trunk8ec3:
total 16
drwxr-xr-x 4 root root 4096 Mar 31  2017 .
drwxr-xr-x 3 root root 4096 Mar 31  2017 ..
drwxr-xr-x 2 root root 4096 Mar 31  2017 branch4118
drwxr-xr-x 3 root root 4096 Mar 31  2017 trunk708d


./trunk/trunkbe9c/trunk8ec3/branch4118:
total 12
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf33d8
drwxr-xr-x 2 root       root       4096 Mar 31  2017 .
drwxr-xr-x 4 root       root       4096 Mar 31  2017 ..


./trunk/trunkbe9c/trunk8ec3/trunk708d:
total 12
drwxr-xr-x 3 root root 4096 Mar 31  2017 .
drwxr-xr-x 4 root root 4096 Mar 31  2017 ..
drwxr-xr-x 4 root root 4096 Mar 31  2017 trunk664c
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c:                             
total 16                                                                     
drwxr-xr-x 4 root root 4096 Mar 31  2017 .
drwxr-xr-x 3 root root 4096 Mar 31  2017 ..
drwxr-xr-x 2 root root 4096 Mar 31  2017 branchcd3c
drwxr-xr-x 4 root root 4096 Mar 31  2017 trunk430b
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/branchcd3c:                  
total 16                                                                     
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf280d                
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf29a9                
drwxr-xr-x 2 root       root       4096 Mar 31  2017 .
drwxr-xr-x 4 root       root       4096 Mar 31  2017 ..
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b:                   
total 16                                                                     
drwxr-xr-x 4 root root 4096 Mar 31  2017 .
drwxr-xr-x 4 root root 4096 Mar 31  2017 ..
drwxr-xr-x 2 root root 4096 Mar 31  2017 branch8979
drwxr-xr-x 5 root root 4096 Mar 31  2017 trunk122c
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/branch8979:        
total 16                                                                     
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf52a9                
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf184f                
drwxr-xr-x 2 root       root       4096 Mar 31  2017 .
drwxr-xr-x 4 root       root       4096 Mar 31  2017 ..
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/trunk122c:         
total 20                                                                     
drwxr-xr-x 5 root root 4096 Mar 31  2017 .
drwxr-xr-x 4 root root 4096 Mar 31  2017 ..
drwxr-xr-x 2 root root 4096 Mar 31  2017 branch3c2e
drwxr-xr-x 2 root root 4096 Mar 31  2017 branch67f2
drwxr-xr-x 3 root root 4096 Mar 31  2017 trunkc000
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/trunk122c/branch3c2e:                                                                           
total 12                                                                     
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaff81e                
drwxr-xr-x 2 root       root       4096 Mar 31  2017 .
drwxr-xr-x 5 root       root       4096 Mar 31  2017 ..
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/trunk122c/branch67f2:                                                                           
total 16                                                                     
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf5dd4                
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaf7813                
drwxr-xr-x 2 root       root       4096 Mar 31  2017 .
drwxr-xr-x 5 root       root       4096 Mar 31  2017 ..
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/trunk122c/trunkc000:                                                                            
total 16                                                                     
-rw-rw-r-- 1 hacksports hacksports   32 Apr 16  2017 flag                    
drwxr-xr-x 3 root       root       4096 Mar 31  2017 .
drwxr-xr-x 5 root       root       4096 Mar 31  2017 ..
drwxr-xr-x 2 root       root       4096 Mar 31  2017 branch8211
                                                                             
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/trunk122c/trunkc000/branch8211:                                                                 
total 12                                                                     
-rw-rw-r-- 1 hacksports hacksports   76 Apr 16  2017 leaffbf3                
drwxr-xr-x 2 root       root       4096 Mar 31  2017 .
drwxr-xr-x 3 root       root       4096 Mar 31  2017 ..

XXXXXXX@shell-web:/problems/a47d10dd80018fc6e7e1c5094c1ca323$ find . -iname flag
./trunk/trunkbe9c/trunk8ec3/trunk708d/trunk664c/trunk430b/trunk122c/trunkc000/flag

XXXXXXX@shell-web:/problems/a47d10dd80018fc6e7e1c5094c1ca323$ find . -iname flag -exec cat {} \;
5e3d48f32a6d6e17a8102d3cbae36283
```

The flag is 5e3d48f32a6d6e17a8102d3cbae36283

(Completed)
