# teamx

## Vertical levels

You can take a look at the vertical levels by running the `teamx` configuration
of the `coordinates_discrete.py` script in my
[python-scripts](https://github.com/jcanton/python-scripts/blob/main/coordinates_discrete.py)
repo.

## Setting up

We have to run on CPU for reading profiles and setting up the initial
conditions.
Hopefully the computers behave...

```[bash]
cd $SCRATCH
git clone git@github.com:jcanton/teamx.git
git clone git@github.com:C2SM/icon-exclaim.git icon-exclaim.teamx
cd icon-exclaim.teamx
git checkout teamx # check out our wonderful branch with the test case
git submodule init
git submodule update
uenv start icon/25.2:v3 --view default
./dsl/setup.sh build_cpu
```

Copy the grid file from my scratch (it's too large for GitHub unfortunately).

```[bash]
```[bash]
cp /capstor/scratch/cscs/jcanton/teamx/input/grid_10240m_x_20480m_res20m.nc $SCRATCH/teamx/input
```

## Namelists

The namelists are almost identical for the six cases, what changes is:

### The sounding file

I added a line that creates a link to one of the sounding files from this
repository.
Otherwise you can copy/link it manually, that also works.

```[bash]
# the sounding file (needs to be called `sound_in` for ICON to read it)
add_link_file ${teamx_folder}/soundings_tables/input_sounding_teamx_u10_ridge100 sound_in
```

### The test case configuration

(actually only the mountain height needs to change from one testcase to the next).
Here you have the mountain height, wave length (saved in a variable with a
wrong (`width`) name for convenience, it's constant anyway), and the
perturbation on theta for the initial condition.

```[bash]
&nh_testcase_nml
 nh_test_name = 'teamx'       ! test case identifier
 mount_height  = 100.0        ! height of the mountain
 mount_width   = 10240        ! = 512*20: wave length of the mountain (used gauss3d name to avoid creating a new one)
 th_perturb    = 0.25         ! [K] perturbation on theta
/
```

### The SLEVE configuration

Use the following for flat and 100m hill:

```[bash]
&sleve_nml
 min_lay_thckn   = 10.0        ! Layer thickness of lowermost layer
 top_height      = 5000.0      ! Height of model top
 flat_height     = 5000.0      ! Height above which the coordinate surfaces are flat
 stretch_fac     = 0.65        ! Scaling factor for stretching/squeezing the model layer distribution (NOTE: 0.65 for flat and 100m hill, 1.0 for 1000m hill)
 decay_scale_1   = 2000.0      ! Decay scale of large-scale topography component (NOTE: 2000.0 for 0 and 100m hill, 3000.0 for 1000m hill)
/
```

and this for 1000m hill (change only `stretch_fac` and `decay_scale_1`):

```[bash]
&sleve_nml
 min_lay_thckn   = 10.0        ! Layer thickness of lowermost layer
 top_height      = 5000.0      ! Height of model top
 flat_height     = 5000.0      ! Height above which the coordinate surfaces are flat
 stretch_fac     = 1.0         ! Scaling factor for stretching/squeezing the model layer distribution (NOTE: 0.65 for flat and 100m hill, 1.0 for 1000m hill)
 decay_scale_1   = 3000.0      ! Decay scale of large-scale topography component (NOTE: 2000.0 for 0 and 100m hill, 3000.0 for 1000m hill)
/
```

## Sounding tables

ICON can read external profiles and interpolate them to model levels
(`mo_nh_torus_exp:read_ext_profile`), but they need to be formatted as written
below, so I cleaned up the files they sent us and saved them in this
repository.

```[fortran]
!  Sounding file is assumed to be in this format:
!  ps,no_vert_levels
!  z(m) theta(k) qv(kg/kg) u(m/s) v(m/s)
!  where the top row is close to surface/or surface and bottom row
!  is near the top
```
