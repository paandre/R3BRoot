##############################################################################
#   Copyright (C) 2019 GSI Helmholtzzentrum für Schwerionenforschung GmbH    #
#   Copyright (C) 2019 Members of R3B Collaboration                          #
#                                                                            #
#             This software is distributed under the terms of the            #
#                 GNU General Public Licence (GPL) version 3,                #
#                    copied verbatim in the file "LICENSE".                  #
#                                                                            #
# In applying this license GSI does not waive the privileges and immunities  #
# granted to it by virtue of its status as an Intergovernmental Organization #
# or submit itself to any jurisdiction.                                      #
##############################################################################

# Create a library called "libBase" which includes the source files given in
# the array .
# The extension is already found.  Any number of sources could be listed here.

# Check for gcc version, since for gcc 4 one has to add friend-injection
# as option to compile FairPlutoGenerator
#EXEC_PROGRAM( gcc ARGS "-dumpversion" OUTPUT_VARIABLE GCC_VERSION )
#STRING(REGEX REPLACE "^([0-9]+)\\.[0-9]+\\.[0-9]" "\\1" req_gcc_major_vers "${GCC_VERSION}")
#STRING(REGEX REPLACE "^[0-9]+\\.([0-9]+)\\.[0-9]" "\\1" req_gcc_minor_vers "${GCC_VERSION}")
#IF(${req_gcc_major_vers} MATCHES "4" AND NOT ${req_gcc_minor_vers} MATCHES "0")
#  SET_SOURCE_FILES_PROPERTIES(FairPlutoGenerator.cxx PROPERTIES COMPILE_FLAGS
#  "-ffriend-injection")
#ENDIF(${req_gcc_major_vers} MATCHES "4" AND NOT ${req_gcc_minor_vers} MATCHES "0")


#########################################################
# temporary workaround for testR3BPhaseSpaceGenerator.cxx
# while FairVersion.h is not working
execute_process(COMMAND ${FAIRROOTPATH}/bin/fairroot-config --major_version
    OUTPUT_VARIABLE fairroot_major_version)
execute_process(COMMAND ${FAIRROOTPATH}/bin/fairroot-config --minor_version
    OUTPUT_VARIABLE fairroot_minor_version) 

if((${fairroot_major_version} EQUAL 17 AND ${fairroot_minor_version} EQUAL 10) OR ${fairroot_major_version} GREATER 17)
message("using new FairPrimaryGenerator interface")
add_definitions(-DFairPrimaryGeneratorAddTrackNewInterface)
endif((${fairroot_major_version} EQUAL 17 AND ${fairroot_minor_version} EQUAL 10) OR ${fairroot_major_version} GREATER 17)
#########################################################

Set(SYSTEM_INCLUDE_DIRECTORIES 
${SYSTEM_INCLUDE_DIRECTORIES}
${BASE_INCLUDE_DIRECTORIES}
#${PLUTO_INCLUDE_DIR}
)

set(INCLUDE_DIRECTORIES
${R3BROOT_SOURCE_DIR}/field 
${R3BROOT_SOURCE_DIR}/generators 
${R3BROOT_SOURCE_DIR}/r3bgen 
${R3BROOT_SOURCE_DIR}/r3bdata
)

include_directories( ${INCLUDE_DIRECTORIES})
include_directories(SYSTEM ${SYSTEM_INCLUDE_DIRECTORIES})

set(LINK_DIRECTORIES
${ROOT_LIBRARY_DIR}
${FairLogger_LIBDIR}
#${PLUTO_LIBRARY_DIR}
${FAIRROOT_LIBRARY_DIR}
)
 
link_directories( ${LINK_DIRECTORIES})

set(SRCS
R3BSpecificGenerator.cxx
R3BReadKinematics.cxx
R3BCDGenerator.cxx
R3BBeamInfo.cxx
R3BBackTracking.cxx
R3BBackTrackingStorageState.cxx
R3BAsciiGenerator.cxx
R3BCosmicGenerator.cxx
R3Bp2pGenerator.cxx
R3BLandGenerator.cxx
R3BCALIFATestGenerator.cxx
R3BIonGenerator.cxx
R3BGammaGenerator.cxx
R3BPhaseSpaceGenerator.cxx
R3BDistribution1D.cxx
R3BDistribution2D.cxx
R3BDistribution3D.cxx
R3Bp2pevtGenerator.cxx
)

# fill list of header files from list of source files
# by exchanging the file extension
CHANGE_FILE_EXTENSION(*.cxx *.h HEADERS "${SRCS}")

set(LINKDEF R3BGenLinkDef.h)
set(LIBRARY_NAME R3BGen)
set(DEPENDENCIES
    FairTools Field Gen R3BData)

GENERATE_LIBRARY()

# Testing
enable_testing()
Set(GTEST_ROOT ${SIMPATH})
find_package(GTest REQUIRED)
include_directories(${GTEST_INCLUDE_DIRS})
