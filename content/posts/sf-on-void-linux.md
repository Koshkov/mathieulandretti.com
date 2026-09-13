+++
date = '2024-07-06T20:13:06-05:00'
draft = false 
title = 'Installing sf on Void Linux'
tags = ['linux','sf']
summary = 'I wanted to start working with spatial data in R but was unable to install the sf package.'
+++

I ran into a problem recently. I wanted to start working with spatial data in R. 
However, I was unable to install the sf package.

    > install.package('sf')

The thing that was causing the hangup was a missing udunits2.so file. The R
console error recommended that I install the udunits2-dev package from my Linux
repo. However, Void does not contain the dev package for udunits2. Instead they
have a simple units package which only contains a binary.

To fix this, I had to compile udunits2 from source from the project [website](https://docs.unidata.ucar.edu/udunits/current/) and
move the udunits2.so file to /usr/lib manually.

After downloading the archive from the website, unzip the tar.gz archive
archive, and cd into the unzipped directory (as of this writing, the most
current version of udunits2 is 2.2.28. Yours may be different).

    $ tar -xvf udunits-2.2.28.tar.gz && cd udunits-2.2.28

Then, run the following command (Depending on your system, some dependencies
may be required to successfully compile, all of which can be found in the Void
repos):

     $ ./configure && sudo make all install 

If executed successfully, the project will create four new directories in
../udunits-2.2.28 called lib, share, include, and bin. Finally, cd into the
newly created lib directory and move all of the files into /usr/lib.

    # mv libudunits2.a libudunits2.so.0 libudunits2.la libudunits2.so.0.1.0 \
         libudunits2.so /usr/lib 

Thanks to a helpful email, it was brought to my attention that it may be
necessary to to move the header files from the locally created include
directory to /usr/include.

    # mv converter.h udunits.h udunits2.h /usr/include

After completing this process, I have been able to install R packages such as
sf, units, and mapview without issue.

The reason it hasn't been added to the Void repos yet appears to be a licensing
issue. There is an email chain requesting that the package be added to repos
here, but it appears to have gone stale.
