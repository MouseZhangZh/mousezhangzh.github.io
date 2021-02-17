---
layout: post
title: "使用mk-build-deps安装依赖"
excerpt: "在日常开发中，常常会遇到依赖问题，使用mk-build-deps可一键安装所有依赖，当然，前提是你需要在项目的control文件中先写好所有的依赖:)"
categories: [Debian, C++, Qt]
comments: true
---

# Use `mk-build-deps` instead of `apt-get build-dep`



If you are building a package from source on Debian or Ubuntu, usually the first step is to install the build-dependencies of the package. This is usually done with one of two commands:
```
$ sudo apt-get build-dep PKGNAME
```
or
```
$ sudo aptitude build-dep PKGNAME
```
The problem is that there is no easy way to undo or revert the installation of the build dependencies. All the installed packages are marked as manually installed, so later one cannot simply expect to “autoremove” those packages. [Webupd8](http://www.webupd8.org/2010/10/undo-apt-get-build-dep-remove-build.html) suggests clever one-liner that tries to parse the build dependencies out of apt-cache and mark them as automatically installed. However, as mentioned in the comments, it may be too liberal in marking packages as automatically installed, and hence remove too many packages.

The real solution is `mk-build-deps`. First you have to install it:
```
$ sudo apt install devscripts
```
Now, instead of using apt-get or aptitude directly to install the build-dependencies, use `mk-build-deps`.
```
$ mk-build-deps PKGNAME --install --root-cmd sudo --remove
```
`mk-build-deps` will create a new package, called `PKGNAME-build-deps`, which depends on all the build-dependencies of `PKGNAME` and then install it, therefore pulling all the build-dependencies and installing them as well. As those packages are now installed as dependencies they are marked as automatically installed. Once, you no longer need the build-dependencies, you can remove the package `PKGNAME-build-deps`, and `apt` will autoremove all the build-dependencies which are no longer necessary.

Reference from [here](http://www.guyrutenberg.com/2017/09/23/use-mk-build-deps-instead-of-apt-get-build-dep/).

Ps. If we have `debian/control` in our project, we can use mk-build-deps to install all dependence in the `control`:
```
sudo mk-build-deps -i debian/control
```
