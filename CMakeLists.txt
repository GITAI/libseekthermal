cmake_minimum_required(VERSION 2.6)

include(ReMake)

remake_project(
  libseekthermal
  VERSION 0.1
  RELEASE beta
  SUMMARY "Driver library for Seek Thermal imaging devices."
  AUTHOR "Ralf Kaestner"
  CONTACT "ralf.kaestner@gmail.com"
  HOME "http://github.com/ethz-asl/libseekthermal"
  LICENSE "GNU Lesser General Public License (LGPL)"
  README README.md
  PREFIX seekthermal-
  INSTALL /usr
  HEADER_DESTINATION include/seekthermal
  EXTRA_CXX_FLAGS -std=c++0x
)

remake_component(
  udev
  CONFIGURATION_DESTINATION /etc/udev
)
remake_add_directories(
  rules
  COMPONENT udev
)
remake_doc(
  html
)

remake_pack_deb(
  DEPENDS libudev0|libudev1 libusb-1.0-0 libboost-chrono[0-9.]*
)

find_program(LSB_RELEASE_EXEC lsb_release)
execute_process(COMMAND ${LSB_RELEASE_EXEC} -cs
    OUTPUT_VARIABLE LSB_RELEASE_ID_SHORT
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
if (${LSB_RELEASE_ID_SHORT} STREQUAL "bionic")
  set(PNG_PACKAGE "libpng16-16")
  set(PNG_DEV_PACKAGE "libpng-dev")
else(${LSB_RELEASE_ID_SHORT} STREQUAL "bionic")
  set(PNG_PACKAGE "libpng12-0")
  set(PNG_DEV_PACKAGE "libpng12-dev")
endif(${LSB_RELEASE_ID_SHORT} STREQUAL "bionic")

remake_pack_deb(
  COMPONENT utils
  DESCRIPTION "utilities"
  DEPENDS libseekthermal ${PNG_PACKAGE}
)
remake_pack_deb(
  COMPONENT gui
  DESCRIPTION "GUI"
  DEPENDS libseekthermal libqtgui4
)
remake_pack_deb(
  COMPONENT dev
  DESCRIPTION "development headers"
  DEPENDS libseekthermal
)
remake_pack_deb(
  COMPONENT udev
  DESCRIPTION "udev rules"
  DEPENDS udev
  EXTRA debian/rules/*
)
remake_pack_deb(
  ARCH all
  COMPONENT doc
  DESCRIPTION "documentation"
)
remake_distribute_deb(
  DISTRIBUTION precise
  SECTION libs
  UPLOAD ppa:kralf/asl
  DEPENDS libboost-chrono1.48-dev libudev-dev libusb-1.0-0-dev
    libpng12-dev libqt4-dev remake doxygen pkg-config
  PASS CMAKE_BUILD_TYPE LIBSEEKTHERMAL_GIT_REVISION
)
remake_distribute_deb(
  DISTRIBUTION trusty
  SECTION libs
  UPLOAD ppa:kralf/asl
  DEPENDS libboost-chrono-dev libudev-dev libusb-1.0-0-dev
    libpng12-dev libqt4-dev remake doxygen pkg-config
  PASS CMAKE_BUILD_TYPE LIBSEEKTHERMAL_GIT_REVISION
)
remake_distribute_deb(
  DISTRIBUTION bionic
  SECTION libs
  UPLOAD ppa:kralf/asl
  DEPENDS libboost-chrono-dev libudev-dev libusb-1.0-0-dev
    libpng-dev libqt4-dev remake doxygen pkg-config
  PASS CMAKE_BUILD_TYPE LIBSEEKTHERMAL_GIT_REVISION
)
