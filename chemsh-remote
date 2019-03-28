#!/usr/bin/env python

# BSD 3-Clause License
#
# Copyright (c) 2019, Science and Technology Facilities Council
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# * Redistributions of source code must retain the above copyright notice, this
#   list of conditions and the following disclaimer.
#
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the following disclaimer in the documentation
#   and/or other materials provided with the distribution.
#
# * Neither the name of the copyright holder nor the names of its
#   contributors may be used to endorse or promote products derived from
#   this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.

"""
A simple wrapper script to have Longbow launch chemshell simulations on a remote
HPC resource and extract energies from the log files in real time.
"""

import re
import subprocess
import sys
import time


def detecttermination(logfile):

    """
    Query whether or not a process with the given process id is still running
    and return true or false.
    """

    # Open longbow log file and detect if the final "goodbye" lines is there.
    try:

        fi = open(logfile)

        lines = fi.readlines()

        if len(lines)>0:

            if "Good bye from Longbow!" in lines[len(lines)-2]:

                return True

    except:

        pass

    return False

def extractenergies(logfile, energies):

    """
    A method to extract the energies from the Chemshell log file.
    """

    # Regex for generic scientific numbers.
    regex = r'(([-1-9]+\.[-0-9]*)|([-1-9]*\.[-0-9]+)|([-1-9]+))([eE][-+]?[0-9]+)?'

    # Open logfile and parse for the energy lines.
    with open(logfile) as fi:

        for line in fi:

            if "Energy calculation finished, energy:" in line:

                energies.append(re.search(regex, line).group())


def run(cmdline):

    """
    The main method that will orchestrate the calling of Chemshell via Longbow and pull
    energies out of the log file.
    """

    # Setup an empty list for the energies.
    energies = []

    # Launch Chemshell with Longbow.
    cmdline.insert(0, "longbow")
    subprocess.Popen(cmdline)

    # Loop until Longbow terminates and gather energies.
    while detecttermination("log") is not True:

        try:

            extractenergies("jobname.log", energies)

            if len(energies) > 0:

                print(energies)

        except:

            pass

        time.sleep(5)


if __name__ == "__main__":

    """
    Launcher included for debugging but really this is a library.
    """

    # Grab the sys.argv args
    cmdline = sys.argv
    cmdline.pop(0)

    # Launch
    run(cmdline)
