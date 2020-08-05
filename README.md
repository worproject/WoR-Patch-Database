# WoR-Patch-Database
A list of patches that enable some missing features (such as USB 3.0) on a Raspberry Pi 4 device running Windows 10 ARM64.

Note: the [Windows on Raspberry imager](https://www.worproject.ml) will make use of this repository in the next version (2.0.0).

## Contributing
If you've found that the current patches work on a build of Windows that's not in the specified range, you can report it in a new issue.

If the existing patches do not apply to the build that you're testing, you can open an issue to report that (but fixing it is up to the maintainers / anyone interested), or you can attempt to find the required patch data yourself and send a Pull Request.

This article nicely explains the process of finding patches: https://pete.akeo.ie/2020/07/abusing-microsofts-symbol-servers-for.html

The tool used for patching system files can be found here: https://github.com/pbatard/winpatch

## About the database
The `wor_patch_database` root element of the XML database is an array of `patch_pack` elements.

* a `patch_pack` element has the following properties:

    * `build_min` and `build_max` attributes represent the **minimum** and the **maximum** build numbers of Windows on which the set of patches can be successfully applied.

      **Restrictions:** `build_min` <= `build_max` , and they **must not** have the values of another `patch_pack` element.
      
    * the `memory_limit` element represents the RAM limit **(measured in MB)** required for the patched files to work properly.
       
      **This element can be set to `0` if no memory limit is required.**       
       
    * the `files` element is an array of `file` elements. A `file` element has the following properties:
    
        * the `name` attribute is the path to the file that requires patching.
        
          **The path must be on the partition that has the Windows image.**
        
        * the `data` element is a sequence of pairs that make up the patch itself. Details here: https://github.com/pbatard/winpatch#usage
       
        * the `expected_sections` element is the number of data sections that should be patched (winpatch is going to output that information, and warn if duplicate sections are found)

      **This array can be left empty if no patches are required for the provided build range.**

The parser will first look for a `patch_pack` whose [`build_min`,`build_max`] range contains the given build of Windows.
If it can't find any, it will pick the closest range to the given build number (and it'll display a warning message).

**Note: don't create a new `patch_pack` element unless it's absolutely necessary (e.g.: things got fixed in a recent build of Windows)**
