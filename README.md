SpaceVecAlg
========

[![License](https://img.shields.io/badge/License-BSD%202--Clause-green.svg)](https://opensource.org/licenses/BSD-2-Clause)
[![Build Status](https://travis-ci.org/jrl-umi3218/SpaceVecAlg.svg?branch=master)](https://travis-ci.org/jrl-umi3218/SpaceVecAlg)
[![AppVeyor status](https://ci.appveyor.com/api/projects/status/m58vf1fke7nc9k95/branch/master?svg=true)](https://ci.appveyor.com/project/gergondet/spacevecalg/branch/master)
[ ![Download](https://api.bintray.com/packages/gergondet/multi-contact/SpaceVecAlg%3Agergondet/images/download.svg) ](https://bintray.com/gergondet/multi-contact/SpaceVecAlg%3Agergondet/_latestVersion)

SpaceVecAlg aim to implement Spatial Vector Algebra with the Eigen3 linear algebra library.

All this implementation is based on appendix A of [Roy Featherstone Rigid Body Dynamics Algorithms book](http://www.springer.com/fr/book/9780387743141).

Installing
------

## Ubuntu LTS (14.04, 16.04, 18.04): PPA

Use the [multi-contact-unstable](https://launchpad.net/~pierre-gergondet+ppa/+archive/ubuntu/multi-contact-unstable) ppa:
```bash
sudo add-apt-repository ppa:pierre-gergondet+ppa/multi-contact-unstable
sudo apt-get update
sudo apt-get install libspacevecalg-dev libspacevecalg-doc python-spacevecalg python3-spacevecalg
```

## Homebrew OS X install

Install from the command line using [Homebrew](brew.sh):

```bash
# install homebrew package manager
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# install caskroom application manager
brew install caskroom/cask/brew-cask
# tap homebrew-science package repository
brew tap homebrew/science
# tap ahundt-robotics repository
brew tap ahundt/robotics
# install tasks and all its dependencies
brew install spacevecalg
```

## Manually build from source

### Dependencies

To compile you need the following tools:

 * [Git]()
 * [CMake]() >= 2.8
 * [pkg-config]()
 * [doxygen]()
 * [g++]() >= 4.7 (for C++11 support)
 * [Boost](http://www.boost.org/doc/libs/1_58_0/more/getting_started/unix-variants.html) >= 1.49
 * [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) >= 3.2

For Python bindings:

 * [Cython](http://cython.org/) >= 0.25
 * [Eigen3ToPython](https://github.com/jrl-umi3218/Eigen3ToPython) (to use the Python binding)

### Building

```sh
git clone --recursive https://github.com/jrl-umi3218/SpaceVecAlg
cd SpaceVecAlg
mkdir build
cd build
cmake [options] ..
make && make intall
```

#### CMake options

By default, the build will use the `python` and `pip` command to install the bindings for the default system version (this behaviour can be used to build the bindings in a given virtualenv). The following options allow to control this behaviour:

 * `PYTHON_BINDING` Build the python binding (ON/OFF, default: ON)
 * `PYTHON_BINDING_FORCE_PYTHON2`: use `python2` and `pip2` instead of `python` and `pip`
 * `PYTHON_BINDING_FORCE_PYTHON3`: use `python3` and `pip3` instead of `python` and `pip`
 * `PYTHON_BINDING_BUILD_PYTHON2_AND_PYTHON2`: builds two sets of bindings one with `python2` and `pip2`, the other with `python3` and `pip3`
 * `BUILD_TESTING` Enable unit tests building (ON/OFF, default: ON)

### Arch Linux

You can use the following [AUR package](https://aur.archlinux.org/packages/spacevecalg-git).


Documentation
-----

Features:
 * Featherstone Spatial Vector Algebra C++11 implementation
 * Header only
 * Use Eigen3 as linear algebra library
 * Python binding

A short tutorial is available [here](https://github.com/jorisv/sva_rbdyn_presentation/blob/master/presentation_release.pdf).

To learn more about Spatial Vector Algebra you can find some presentations on the following [page](http://royfeatherstone.org/spatial/).

The [SpaceVecAlg and RBDyn tutorial](https://github.com/jorisv/sva_rbdyn_tutorials) is also a big ressource to understand [how to use SpaceVecAlg](http://nbviewer.ipython.org/github/jorisv/sva_rbdyn_tutorials/blob/master/SpaceVecAlg.ipynb).
Also you will find a lot of IPython Notebook that will present real use case.

Finally you can build a Doxygen documentation by typing `make doc` in the build directory. After a `make install` the documentation will be in `CMAKE_INSTALL_PREFIX/share/doc/SpaceVecAlg` (see the Installing section).

An up-to-date doxygen documentation is also available [online](http://jrl-umi3218.github.io/SpaceVecAlg/doxygen/HEAD/index.html).

#### Handedness - Left Hand Rule

When getting started with SpaceVecAlg it is important to know that PTransform utlizes the Left Hand Rule for rigid body transforms and not the [Right Hand Rule](https://en.wikipedia.org/wiki/Right-hand_rule) used by many other libraries and classes. Switching the handedness of a rigid body transform can be done with the functions in [Conversions.h](https://github.com/jrl-umi3218/SpaceVecAlg/blob/master/src/SpaceVecAlg/Conversions.h), or inverting the rotation component. If your transforms are not working as you expect, the handedness is worth double checking.

### Appendix A table transcription to C++

In this section `a` stand for a double, `v` for a motion vector, `f` for a force vector,
`I` for a rigid body inertia, `I^a` for a articulated body inertia and
`X` for a plücker transfrom.

`.` stand for the dot product, `x`for the cross product and `x^{\*}` for the cross product dual.

`r` stand for a 3d translation vector, `E` for a 3d rotation matrix,
`m` for a mass, `c` for the center of mass 3d vector from the body origin,
`I_c` for the 3d rotational inertia matrix at CoM frame.

#### Table A.2 transcription

Operation                    | C++
-----------------------------|----
rx(theta)                    | `sva::RotX(theta)`
ry(theta)                    | `sva::RotY(theta)`
rz(theta)                    | `sva::RotZ(theta)`
X = rotx(theta)              | `sva::PTransformd(sva::RotX(theta))`
X = roty(theta)              | `sva::PTransformd(sva::RotY(theta))`
X = rotz(theta)              | `sva::PTransformd(sva::RotZ(theta))`
X = xlt(r)                   | `sva::PTransformd(r)`
x = crm(v)                   | `sva::vector6ToCrossMatrix(v)`
v x^{\*} = crf(v)            | `sva::vector6ToCrossDualMatrix(v)`
I = E\*mcI(m, c, I_c)\*E{^T} | `inertiaToOrigin(I_c, m, c, E)`
v = XtoV(X)                  | `sva::transformVelocity(X)`


#### Table A.4 transcription

Operation           | C++
--------------------|----
a v                 | `a*sva::MotionVecd()`
a f                 | `a*sva::ForceVecd()`
a I                 | `a*sva::RBInertiad()`
a I^a               | `a*sva::ABInertiad()`
v_1 + v_2           | `sva::MotionVecd() + sva::MotionVecd()`
f_1 + f_2           | `sva::ForceVecd() + sva::ForceVecd()`
I_1 + I_1           | `sva::RBInertiad() + sva::RBInertiad()`
I_1^a + I_2^a       | `sva::ABInertiad() + sva::ABInertiad()`
I_1^a + I_2^a       | `sva::ABInertiad() + sva::RBInertiad()`
v . f               | `sva::MotionVecd().dot(sva::ForceVecd())`
v_1 x v_2           | `sva::MotionVecd().cross(sva::MotionVecd())`
v x^\* f            | `sva::MotionVecd().crossDual(sva::ForceVecd())`
I v                 | `sva::RBInertiad()\*sva::MotionVecd()`
I^a v               | `sva::ABInertiad()\*sva::MotionVecd()`
X_1 X_2             | `sva::PTransformd()\*sva::PTransformd()`
X^{-1}              | `sva::PTransformd().inv()`
X v                 | `sva::PTransformd()\*sva::MotionVecd()`
X^{-1} v            | `sva::PTransformd().invMul(sva::MotionVecd())`
X^{\*} f            | `sva::PTransformd().dualMul(sva::ForceVecd())`
X^{T} f             | `sva::PTransformd().transMul(sva::ForceVecd())`
X^{\*} I X^{-1}     | `sva::PTransformd().dualMul(sva::RBInertiad())`
X^{T} I X           | `sva::PTransformd().transMul(sva::RBInertiad())`
X^{\*} I^a X^{-1}   | `sva::PTransformd().dualMul(sva::ABInertiad())`
X^{T} I^a X         | `sva::PTransformd().transMul(sva::ABInertiad())`

#### Table A.3 transcription

Here `w` stand for the 3d angular velocity, `v` for the 3d linear velocity,
`n` for the 3d torque, `f` for the 3d force, `E` for the 3d rotation matrix,
`r` for the 3d translation vector, `q` for a unit quaternion, `m` for a mass,
`h` for the first moment of mass (h = m c) at body frame,
`I` for the 3d rotational inertia at body frame, `M` for the 3d mass matrix,
and `H` for the 3d generalized inertia matrix.

Operation                    | C++
-----------------------------|----
mv(w, v)                     | `sva::MotionVecd(w, v)`
fv(n, f)                     | `sva::ForceVecd(n, f)`
plx(E, r)                    | `sva::PTransform(E, r)`
plx(q, r)                    | `sva::PTransform(q, r)`
rbi(m, h, I)                 | `sva::RBInertia(m, h, I)`
abi(M, H, I)                 | `sva::ABInertia(M, H, I)`
