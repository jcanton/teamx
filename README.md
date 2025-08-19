# teamx

## Structure

You can take a look at the vertical levels by playing around with the `teamx`
configuration of the `coordinates_discrete.py` script in my
[python-scripts](https://github.com/jcanton/python-scripts/blob/main/coordinates_discrete.py)
repo.

## Setting up

Hopefully the computers behave...

```[bash]
cd $SCRATCH
git clone git@github.com:jcanton/teamx.git
git clone git@github.com:C2SM/icon-exclaim.git icon-exclaim.teamx
cd icon-exclaim.teamx
git checkout teamx # check out our wonderful branch
git submodule init
git submodule update
uenv start icon/25.2:v3 --view default
./dsl/setup.sh build_acc
```

## Namelists

The namelists are almost identical for the six cases, what changes is:

### The sounding file

```[bash]
# the sounding file (needs to be called `sound_in` for ICON to read it)
add_link_file ${teamx_folder}/soundings_tables/input_sounding_teamx_u10_ridge100 sound_in
```

### The test case configuration

(actually only the mountain height)

```[bash]
&nh_testcase_nml
 nh_test_name = 'teamx' ! test case identifier
 mount_height  = 100.0  ! height of the mountain
 mount_width   = 10240  ! = 512*20: wave length of the mountain (used gauss3d name to avoid creating a new one)
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

and change `stretch_fac` and `decay_scale_1` to this for 1000m hill:

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
below, so I cleaned up the files they sent us.

```[fortran]
!  Sounding file is assumed to be in this format:
!  ps,no_vert_levels
!  z(m) theta(k) qv(kg/kg) u(m/s) v(m/s)
!  where the top row is close to surface/or surface and bottom row
!  is near the top
```
