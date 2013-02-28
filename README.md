NAME: libflx

VERSION: .01  

AUTHOR: Mike Maul

AUTHOR_EMAIL: 

PKG_URL: https://github.com/mmaul/libflx

DESCRIPTION: Scriptable flx front end

CATEGORY: Development

LIBDIR: LIBFLX

-----
Quickstart SetupTool application template for a generic library.

## Quickstart Installation ##
* 'install' must be able to write to Felix INSTALL_ROOT

    scoop install libflx

libflx the flx API interface
============================
libflx is progrmattic interface to the functionality offered by the 'flx' executable. It is implemented as a
Felix object and offered as a plugin. It was motivated by a sick to deathness of stringing together argument
strings to call the 'flx' compiler. Unfortunately it came alittle late to the party so it is not currently
used extensively in PkgTool, howeer in the future PkgTool may be refactored to use libflx inplace of shell
calls to execute the 'flx' compiler frontend. It is available in the PkgTool through the run_flx procedure.
libflx can be used outside of the Package tool frame work and would be quite useful for in an IDE.
Good examples of usage of libflx can be found in the the PkgTool tests.

Below is one such test:

    include "LIBFLX/libflx_factory";
    include "PKGTOOL/pkgtool";
    open PkgTool;
    var myflx = libflx_factory();
    myflx.set_dbug(false);
    myflx.set_showcode(true);
    myflx.set_snort(true);
    match myflx.init_compiler() with
    |OK[string] => {
      imply("Compiler initialization");
      match myflx.flx_compile("test/D01-libflx.flx") with
      |OK[string] => {
        imply("Compiliation of D01-libflx.flx");
        match myflx.c_compile() with
          |ERR[string] (?e,?m) => { test_fail("Returned with error code:"+e+":"+m); }
          |OK[string] ?m => {
          assert_true(m == "A\na\n", 
            "Compiling D01-libflx.flx to shared object and running");
        }
        endmatch;
      }
      |ERR[string] (?e,?m) => { test_fail("Returned with error code:"+e+":"+m);}
      endmatch;
    }
    |ERR[string] (?e,?m) => { test_fail("Returned with error code:"+e+":"+m); }
    endmatch;
    imply("Control returned to test harness");


