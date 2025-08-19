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

## Sounding tables

ICON can read external profiles and interpolate them to model levels
(`mo_nh_torus_exp:read_ext_profile`), they need to be formatted as follows:

```[fortran]
!  Sounding file is assumed to be in this format:
!  ps,no_vert_levels
!  z(m) theta(k) qv(kg/kg) u(m/s) v(m/s)
!  where the top row is close to surface/or surface and bottom row
!  is near the top
```
