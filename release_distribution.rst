.. QuTiP
   Copyright (C) 2011-2017, Alexander J. G. Pitchford, Paul D. Nation & Robert J. Johansson

.. This file was created using retext 6.1 https://github.com/retext-project/retext

.. _release_distribution:

************************
Release and Distribution
************************

Preamble
++++++++

This document covers the process for managing updates to the current minor release and making new releases.

If just a bug fix update(s) to the current minor release is required, 
then first follow bugfix_. You will most likely then wish to make a micro release, 
unless you are waiting for further bug fixes before doing so.

To make a micro (patch) release follow newmicrorelease_, distbuild_, docbuild_, web_, cforge_, pypi_

For a minor or major release follow newrelease_, distbuild_, docbuild_, web_, cforge_, pypi_

.. _gitwf:

Git workflow
++++++++++++

.. _bugfix:

Apply bug fix to latest release
-------------------------------
Assuming that the bug(s) has been fixed in some commit on the master,
then this bug(s) fix should now be applied to the latest release.
First checkout ``master``, use ``$ git log`` to list the commits,
and copy the GUID(s) for the bug fix commit(s) to some temporary file or similar.

Now check out latest version branch, e.g.

If you have checked out this branch previously, then ::

    $ git checkout qutip-4.0.X
    $ git pull upstream qutip-4.0.X

Otherwise ::

    $ git fetch upstream
    $ git checkout -b qutip-4.0.X upstream/qutip-4.0.X
    $ git push -u origin qutip-4.0.X

Create a branch for the patch ::

    $ git checkout -b patch4.0-fix_bug123 qutip-4.0.X

Pick the commit(s) to be applied to the release.
Using the commit GUID(s) copied earlier, cherry pick them into the current bug fix branch, e.g. ::

    $ git cherry-pick 69d1641239b897eeca158a93b121553284a29ee1

for further info see https://www.kernel.org/pub/software/scm/git/docs/git-cherry-pick.html

push changes to your fork ::

    $ git push --set-upstream origin patch4.0-fix_bug123

Make a Pull Request to the latest release branch on Github. 
That is make a PR from the bug fix branch to the release branch (not the master), e.g. `qutip-4.0.X`

Merge this PR when the tests have passed.

.. _newmicrorelease:

Create a new micro release
--------------------------

If the final step in the release process was completed correctly last time, 
then the version should already being correct for this micro release,
but check to be sure.
That is, one micro release higher than the current release version.
So you should just need to update the ``ISRELEASED`` flag from ``False`` to ``True``.
For example, if the current released version is 4.0.1, 
then the main ``setup.py`` for the project in this branch should now contain ::

    MAJOR = 4
    MINOR = 0
    MICRO = 2
    ISRELEASED = True

.. _newrelease:

Create a new minor or major release
-----------------------------------

Create a new branch on qutip repository using the website, e.g. 'qutip-4.1.X'.
(See https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-and-deleting-branches-within-your-repository#creating-a-branch)
Checkout the branch and push to your fork ::

    $ git fetch upstream
    $ git checkout -b qutip-4.1.X upstream/qutip-4.1.X
    $ git push -u origin qutip-4.1.X

Create a new branch from this, e.g. ::

    $ git checkout -b 4.1-release_ready qutip-4.1.X

Edit the main ``setup.py`` for the project.
Update the ``CLASSIFIERS`` to the correct ``Development Status`` ::

    CLASSIFIERS = """\
    Development Status :: 5 - Production/Stable

Update the version and ``ISRELEASED`` flag ::

    MAJOR = 4
    MINOR = 1
    MICRO = 0
    ISRELEASED = True

push changes to your fork ::

    $ git push --set-upstream origin 4.1-release_ready

Make a Pull Request to the release branch

The ``master`` branch minor version should be bumped up and should remain in Beta status, e.g. ::

    Development Status :: 4 - Beta
    ...
    MAJOR = 4
    MINOR = 2
    MICRO = 0
    ISRELEASED = False

.. _distbuild:

Build distribution
++++++++++++++++++

Check out branch to be distributed, e.g.

If you have checked out this branch previously, then ::

    $ git checkout qutip-4.0.X
    $ git pull upstream qutip-4.0.X

Otherwise ::

    $ git fetch upstream
    $ git checkout -b qutip-4.0.X upstream/qutip-4.0.X
    $ git push -u origin qutip-4.0.X

The next steps are based instructions from https://packaging.python.org/distributing/

.. _sdist:

Build the source code distribution
----------------------------------
On Mac, first create a directory named ``tmp`` in your home directory.  

::

    $ python setup.py egg_info --egg-base ~/tmp sdist --formats=gztar,zip

Note the ``egg_info --egg-base ~/tmp`` here will create the qutip.egg-info in a subfolder ``tmp`` in your home directory. 
This is to avoid the egg-info being included in the gztar / zip, which otherwise causes issues, as it contains absolute paths to some sources.

Test the distribution that is now in the ``dist`` folder. Should try at least two environments, e.g. Windows and Linux / MacOS.


.. _docbuild:

Documentation build
+++++++++++++++++++
Documentation should be rebuilt for a minor or major release. If there have been any documentation updates as part of a micro release, then it should also be built for this.

First:

- The version should be changed in ``conf.py``.
- Update ``api_doc/classes.rst`` for any new / deleted classes.
- Update ``api_doc/functions.rst`` for any new / deleted functions.

Rebuilding of the QuTiP documentation is fully documented in:
https://github.com/qutip/qutip-doc/blob/master/README.md

.. _web:

Website
+++++++

This assumes that qutip.github.io has already been forked and familiarity with the website updating workflow.
The documentation may not be updated for every micro release.

Copying new files
-----------------

All released versions will be saved in a subfolder like ::

    downloads/<MAJOR>.<MINOR>.<MICRO>

Download links will be kept to the highest micro release of the current and all legacy minor release.
Download links will also be kept to the PDF documentation for that version.

For all releases
- copy the ``.tar.gz`` and ``.zip`` created using sdist_ into the downloads folder.
- also move (no new docs) or copy (for new docs) the ``qutip-doc-<MAJOR>.<MINOR>.pdf`` into this folder.

The legacy html documentation should be in a subfolder like ::

    docs/<MAJOR>.<MINOR>
    
For a major or minor release the previous version documentation should be moved into this folder. 

The latest version HTML documentation should be the folder ::

    docs/latest
    
For any release which new documentation is included
- copy the contents ``qutip-doc/_build/html`` into this folder. **Note that the underscores at start of the subfolder names will need to be removed, otherwise Jekyll will ignore the folders**. There is a script in the ``docs`` folder for this. 
https://github.com/qutip/qutip.github.io/blob/master/docs/remove_leading_underscores.py


HTML file updates
-----------------

- Edit ``download.html``

    * The 'Latest release' version and date should be updated.
    * The gztar and zip links will need the micro release number updating in the tractEvent, file name and label.
    * For a minor or major release links to the last micro release of the previous version will need to be moved (copied) to the 'Previous releases' section.

- Edit ``_includes/sidebar.html``

    * The 'Latest release' version should be updated. The gztar and zip file links will need the micro release number updating in the traceEvent and file name.
    * The link to the documentation folder and PDF file (if created) should be updated.

- Edit ``documentation.html``

    * The previous release tags should be moved (copied) to the 'Previous releases' section.

.. _cforge:

Conda-forge
+++++++++++

If not done previously then fork the qutip-feedstock:
https://github.com/conda-forge/qutip-feedstock

You may have to add CircleCI to your fork using 'Services & Integrations' in the settings. 
This is unless conda-forge have resolved the issue, for which you can check: https://conda-forge.github.io/status/

At time of writing CircleCI was reporting 'Major outage'. However, this was circumvented by adding CircleCI to the fork.

Checkout a new branch on your fork, e.g. ::

    $ git checkout -b version-4.0.2

Generate a new sha256 code from the gztar for this version, e.g. ::

    $ openssl sha256 qutip-4.0.2.tar.gz

Edit the ``recipe/meta.yaml`` file.
Change the version. Update the sha256 code. 
Check that the recipe package version requirements at least match those in the setup.py. 
Also ensure that the build number is reset ::

    build:
        number: 0

Push changes to your fork, e.g. ::

    $ git push --set-upstream origin version-4.0.2

Make a Pull Request. This will trigger tests of the package. CircleCI tests the linux build and install. Likewise TravisCI does MacOS and AppVeyor does Windows.

If (when) the tests pass, the PR can be merged, which will trigger the upload of the packages to the conda-forge channel.
To test the packages, add the conda-forge channel with lowest priority ::

    $ conda config --append channels conda-forge

This should mean that the prerequistes come from the default channel, but the qutip packages are found in conda-forge.

.. _pypi:

Upload source distribution to PyPI
++++++++++++++++++++++++++++++++++

You will need a PyPI account that is assigned as an owner on the project.
(see https://packaging.python.org/distributing/#create-an-account)

**The next step cannot be undone**, it can only be superceded by another (micro) version

Upload the source distribution, e.g ::

    $ twine upload dist/qutip-4.0.1.tar.gz

The MICRO version in setup.py qutip-4.0.X branch should now be bumped up one, 
as no more changes can be made to this micro version.

Create a new branch from this, e.g. ::

    $ git checkout -b bump_to-4.0.2 qutip-4.0.X

Edit the main setup.py for the project. Update the version and release status e.g. ::

    MICRO = 2
    ISRELEASED = False

push changes to your fork ::

    $ git push --set-upstream origin bump_to-4.0.2

Make a Pull Request to the release branch
