Algencan.jl
===========

**Algencan.jl** is a [JuMP / MathProgBase](https://www.juliaopt.org/) interface
to the [Algencan](https://www.ime.usp.br/~egbirgin/tango/codes.php)
nonlinear solver.

**Status**

At this point this is pre-alpha software. It has the following limitations:

1. Lower bounds on the inequality constraints are ignored.

1. Options can only be passed for the solver when creating it.

1. You have to compile Algencan yourself. See details below.

**Installation**:

I have not yet created a Package out of it. Up to now it is only a module,  so
to use it you just clone the repository and add the `src` directory to Julia's
`LOAD_PATH`, see the example and the `example` directory.

Algencan is a high performance and large scale Augmented Lagrangian solver
written by Ernesto Birgin and Mario Martínez. It has many special features like
being able to use HSL library functions to speed up linear algebra with sparse
matrices and some smart acceleration strategies. To use it, at least for now,
you need to compile Algencan yourself. Go to Tango's project
[website](https://www.ime.usp.br/~egbirgin/tango/codes.php) grab Algencan and
compile it. However, you need to make the small changes below to be able to get
a dynamic library from it as Algencan creates a static library by default.

Obs: I have had problems using the 7.3.0 version of gcc compilers from Ubuntu
18.04. I suggest that you install the 8.0.1 and compile with it.

**Obs2: Using Algencan without HSL is quite limited. You should compile it with
HSL whenever possible.**

1. Add the option `-f PIC` to  `CFLAGS` and `FFLAGS` in the top of the main
Makefile. Change `gcc`, `g++`, and `gfortran` to `gcc-8`, `g++-8`, and
`gfortran-8`. Now we have two cases:

    * You are not going to use HSL libraries (this may preclude good performance)
    in some problems:

      1. Just type `make` to compile Algencan.

      1. Move to the `lib` directory, where you can find the `libalgencan.a` file
      and type:
      ```bash
      gcc-8 -shared -o libalgencan.so -Wl,--whole-archive libalgencan.a \\
          -Wl,--no-whole-archive -lgfortran
      ```
    * You are going to use HSL.

      1. Prepare your HSL code as instructed in the `README` file you got from
      Algencan. It should be located in `sources\hsl`.

      1. Go back to the initial Algencan dir.

      1. Type `make` and compile Algencan.

      1.  Move to the `lib` directory, where you can find the `libalgencan.a` file
      and type:
      ```bash
      gcc-8 -shared -o libalgencan.so -Wl,--whole-archive libalgencan.a \\
          -Wl,--no-whole-archive -lgfortran -L$PWD -lhsl
      ```

1. You should now have a file named `libalgencan.so` in the `lib` directory.

3. Create a environmental library named `ALGENCAN_LIB_DIR` pointing to the
`lib` directory. You can proceed to use Algencan.jl.
