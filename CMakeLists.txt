cmake_minimum_required(VERSION 3.12 FATAL_ERROR)
if ( ${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR} )
    message(FATAL_ERROR "In-source builds not allowed.
    Please make a new directory (called a build directory) and run CMake from there.
    You may need to remove CMakeCache.txt." )
endif()

if(POLICY CMP0144)
    cmake_policy(SET CMP0144 OLD)
endif()

if(POLICY CMP0167)
    cmake_policy(SET CMP0167 OLD)
endif()

if(POLICY CMP0148)
    cmake_policy(SET CMP0148 OLD)
endif()

#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

project(OSGeoLiveDoc LANGUAGES NONE)
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")


set(iso_size "4.4" CACHE STRING "for osgeolive-X.X.iso")
set(vm_7z_size "7.0" CACHE STRING "for osgeo-vm-X.X.7z")
set(req_hd_size "22" CACHE STRING "required hard disk space")


#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

#---------------------------------------------
# Setting the version number
#---------------------------------------------
set(OSGeoLiveDoc_VERSION_MAJOR "17")
set(OSGeoLiveDoc_VERSION_MINOR "0")
set(OSGeoLiveDoc_VERSION_PATCH "0")
set(OSGeoLiveDoc_VERSION_DEV "")
set(OSGeoLiveDoc_VERSION "${OSGeoLiveDoc_VERSION_MAJOR}.${OSGeoLiveDoc_VERSION_MINOR}.${OSGeoLiveDoc_VERSION_MINOR}${OSGeoLiveDoc_VERSION_DEV}")
set(OSGeoLiveDoc_NAME "OSGeoLive")


#---------------------------------------------
# minimum versions
#---------------------------------------------
set(SPHINX_MINIMUM_VERSION "4.0")


#---------------------------------------------
# Fining sphinx
#---------------------------------------------
find_package(Sphinx ${SPHINX_MINIMUM_VERSION} REQUIRED)
if (NOT SPHINX_FOUND)
    message(WARNING "Sphinx not found. Cannot generate documentation!")
#elseif (SPHINX_VERSION VERSION_LESS SPHINX_MINIMUM_VERSION)
#    message(WARNING "Your Sphinx version is too old!
#    This project requires Sphinx v1.0 or above to produce
#    proper documentation (you have v${SPHINX_VERSION}).
#    You will get output but it will have errors.")
endif()

option(OSGeoLiveDoc_DEBUG
    "Set ON|OFF (default=OFF) to display basic DEBUG" OFF)

option(OSGeoLiveDoc_VERBOSE_DEBUG
    "Set ON|OFF (default=OFF) to display detailed DEBUG" OFF)

#---------------------------------------------
#---------------------------------------------
# Perl
#---------------------------------------------
#---------------------------------------------
include(FindPerl)
if(NOT PERL_EXECUTABLE)
    message(FATAL_ERROR " Please check your Perl installation.")
endif(NOT PERL_EXECUTABLE)

execute_process(
    COMMAND ${PERL_EXECUTABLE} -MDateTime -e ""
    ERROR_QUIET RESULT_VARIABLE DATETIME_STAUS
    )


#---------------------------------------------
#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

# Kind of output
# http://www.sphinx-doc.org/en/stable/builders.html

#---------------------------------------------
#---------------------------------------------
#---------------------------------------------
#---------------------------------------------
option(ALL_DOC
    "Set ON|OFF (default=OFF) to build all documentation styles (exept LOCALE)" OFF)

option(HTML
    "Set ON|OFF (default=OFF) to build Documentation library tree as HTML" OFF)

option(DIRHTML
    "Set ON|OFF (default=OFF) to build Documentation library tree as DIRHTML
    STATUS: the navigation does not work " OFF
    )

option(SINGLEHTML
    "Set ON|OFF (default=OFF) to build Documentation library tree as a single HTML file" OFF
    )

option(PICKLE
    "Set ON|OFF (default=OFF) to build Documentation library tree as PICKLE" OFF)

option(JSON
    "Set ON|OFF (default=OFF) to build Documentation library tree as JSON page" OFF)

option(HTMLHELP
    "Set ON|OFF (default=OFF) to build Documentation library tree as HTMLHELP page" OFF)

option(DUMMY
    "Set ON|OFF (default=OFF) to build Documentation library tree as DUMMY page" OFF)

option(LATEX
    "Set ON|OFF (default=OFF) to build Documentation library tree as LATEX page" OFF)

option(LINKCHECK
    "Set ON|OFF (default=OFF) to build Documentation library tree as LINKCHECK page" OFF)

option(LOCALE
    "Set ON|OFF (default=OFF) to build the locale files of all languages.
    All other options are ignored
    All supported languages are considered" OFF
    )

foreach(opt HTML DIRHTML SINGLEHTML PICKLE JSON HTMLHELP LATEX LINKCHECK)
    if (${${opt}} OR ALL_DOC)
        string(TOLOWER ${opt} val)
        list(APPEND OSGeoLiveDoc_DOC_TARGETS ${val})
    endif()
endforeach()

message(STATUS "OSGeoLiveDoc_DOC_TARGETS = ${OSGeoLiveDoc_DOC_TARGETS}")





#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

# LANGUAGES SETINGS

#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

#---------------------------------------------
# Avaliable Languages
# English is allways built

# list of sphinx languges support
# http://www.sphinx-doc.org/en/stable/config.html#confval-language
# TODO: figure out how to support this
#  "el"   "hu"  "id"  "zh")
#---------------------------------------------
set(OSGeoLiveDoc_SUPPORTED_LANGUAGES "de" "es" "fi" "fr" "hu" "it" "ja" "nl" "pt" "sv" "tr" "zh_Hans")
set(OSGeoLiveDoc_ENGLISH "en")

#---------------------------------------------
# Language options
#---------------------------------------------
option(ALL_LANG
    "Set ON|OFF (default=OFF) to build all documentation supported languages
    ${OSGeoLiveDoc_SUPPORTED_LANGUAGES}" OFF)

foreach(lang ${OSGeoLiveDoc_SUPPORTED_LANGUAGES})
    string(TOUPPER ${lang} val)
    option(${val}
        "Set ON|OFF (default=OFF) build ${lang} Documentation" OFF)
endforeach()

#---------------------------------------------
# Catching the language options to be build
#---------------------------------------------
list(APPEND OSGeoLiveDoc_BUILD_LANGUAGES  ${OSGeoLiveDoc_ENGLISH})
foreach(lang ${OSGeoLiveDoc_SUPPORTED_LANGUAGES})
    string(TOUPPER ${lang} opt)
    if (${opt} OR ALL_LANG)
        list(APPEND OSGeoLiveDoc_BUILD_LANGUAGES ${lang})
    endif()
endforeach()


#---------------------------------------------
# All languages po files are to be generated
#---------------------------------------------
set (SPHINXINTL_LANGUAGE  ${OSGeoLiveDoc_ENGLISH})
if(LOCALE)
    foreach(lang ${OSGeoLiveDoc_SUPPORTED_LANGUAGES})
        set(SPHINXINTL_LANGUAGE "${SPHINXINTL_LANGUAGE},${lang}")
    endforeach()
    set(OSGeoLiveDoc_LANGUAGES ${OSGeoLiveDoc_ENGLISH})
endif()


#------------------------------------------
# Used to generate the languages bar
#------------------------------------------
MACRO(INSERT_INTO_MAP _KEY _VALUE)
    SET("LangMap_${_KEY}" "${_VALUE}")
ENDMACRO(INSERT_INTO_MAP)

INSERT_INTO_MAP("ca" "Català")
INSERT_INTO_MAP("de" "Deutsch")
INSERT_INTO_MAP("el" "Ελληνικά")
INSERT_INTO_MAP("en" "English")
INSERT_INTO_MAP("es" "Español")
INSERT_INTO_MAP("fr" "Français")
INSERT_INTO_MAP("fi" "Suomen kieli")
INSERT_INTO_MAP("hu" "Hungarian")
INSERT_INTO_MAP("id" "Bahasa Indonesia")
INSERT_INTO_MAP("it" "Italiano")
INSERT_INTO_MAP("ja" "日本語")
INSERT_INTO_MAP("ko" "한국어")
INSERT_INTO_MAP("pl" "Polski")
INSERT_INTO_MAP("pt" "Portugus")
INSERT_INTO_MAP("ru" "Русский")
INSERT_INTO_MAP("sv" "Svenska")
INSERT_INTO_MAP("nl" "Dutch")
INSERT_INTO_MAP("tr" "Turkish")
INSERT_INTO_MAP("zh_Hans" "中文")


message(STATUS "OSGeoLiveDoc_BUILD_LANGUAGES = ${OSGeoLiveDoc_BUILD_LANGUAGES}")
message(STATUS "SPHINXINTL_LANGUAGE = ${SPHINXINTL_LANGUAGE}")

#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

# The list of projects to be documented

#---------------------------------------------
#---------------------------------------------
#---------------------------------------------

# Y = to document | name | version | q = has quickstart | o = has overview
file(STRINGS projects_info.csv OSGeoLiveDoc_PROJECTS_VERSIONS_FILE)

MACRO(INSERT_INTO_MAP_KIND _KEY _TARGET)
    if ("${_TARGET}" STREQUAL "")
      set(_TRGT  "")
    else ()
      set(_TRGT
".. image:: /images/logos/${_TARGET}.png
    :align: right
    :target: https://www.osgeo.org
")
    endif()

    SET("OSGEO_KIND_${_KEY}" "${_TRGT}")
ENDMACRO(INSERT_INTO_MAP_KIND)

MACRO(INSERT_INTO_MAP_LOGO _KEY _TARGET)
    if ("${_TARGET}" STREQUAL "")
      set(_TRGT  ${_TARGET})
    else()
      set(_TRGT ":target: ${_TARGET}")
    endif()

    SET("LOGO_${_KEY}"
 ".. image:: /images/projects/${_KEY}/logo_${_KEY}.png
    :align: right
    ${_TRGT}
")
ENDMACRO(INSERT_INTO_MAP_LOGO)

MACRO(INSERT_INTO_MAP_SCREENSHOT _KEY _TARGET)
    SET("SCREENSHOT_${_KEY}"
 ".. image:: /images/projects/${_KEY}/${_KEY}_screenshot.png
    :width: 500px
    :alt: ${_KEY} screenshot
    :align: right
")
ENDMACRO(INSERT_INTO_MAP_SCREENSHOT)

MACRO(INSERT_INTO_MAP_QUICK _KEY _TARGET)
    if ("${_TARGET}" STREQUAL "Y")
        set(_QUICK "
Quickstart
--------------------------------------------------------------------------------

* :doc:`Quickstart documentation <../quickstart/${_KEY}_quickstart>`
")
    else()
        set(_QUICK  " ")
    endif()

    SET("QUICKSTART_${_KEY}" "${_QUICK}")
ENDMACRO(INSERT_INTO_MAP_QUICK)


MACRO(INSERT_INTO_MAP_VMDK _KEY _TARGET)
    if ("${_TARGET}" STREQUAL "Y")
        set(_NOTE  " ")
    else()
	set(_NOTE ".. note:: This project is only included on the OSGeoLive virtual machine disk (VMDK)")
    endif()

    SET("VMDK_${_KEY}" "${_NOTE}")
ENDMACRO(INSERT_INTO_MAP_VMDK)


MACRO(INSERT_INTO_MAP_VERSION _KEY _TARGET)
  SET("VERSION_${_KEY}" "${_TARGET}")
ENDMACRO()

MACRO(INSERT_INTO_MAP_NAME _KEY _TARGET)
    SET("NAME_${_KEY}" "${_TARGET}")
ENDMACRO(INSERT_INTO_MAP_NAME)

MACRO(INSERT_INTO_MAP_WEB _KEY _TARGET)
	SET("WEB_${_KEY}" "${_TARGET}")
ENDMACRO()

set (OSGeoLiveDoc_PROJECTS_NAMES "")
set (OSGeoLiveDoc_PROJECTS_LOGOS "")
set (OSGeoLiveDoc_CONFIGURATION_REGEXP "^(.*)\\|(.*)\\|(.*)\\|(.*)\\|(.*)\\|.*\\|.*\\|.*\\|.*\\|(.*)\\|(.*)\\|(.*)\\|(.*)")

foreach(line ${OSGeoLiveDoc_PROJECTS_VERSIONS_FILE})
    #Skipping comments
    string(REGEX REPLACE "^(#).*" "\\1" is_comment ${line})
    string(STRIP ${is_comment} is_comment)
    if (${is_comment} MATCHES "#")
        continue()
    endif()

    # TODO define what is usefull at this moment
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\1" wantDoc ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\2" project_page ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\3" project_version ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\4" want_quickstart ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\5" want_overview ${line})

    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\6" url_target ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\7" osgeo_kind ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\8" project_name ${line})
    string(REGEX REPLACE "${OSGeoLiveDoc_CONFIGURATION_REGEXP}" "\\9" on_iso ${line})

    string(STRIP "${wantDoc}" wantDoc)
    string(STRIP "${project_page}" project_page)
    string(STRIP "${project_version}" project_version)
    if ("${project_version}" STREQUAL "")
        set(project_version "NA")
    endif()
    string(STRIP "${want_quickstart}" want_quickstart)
    string(STRIP "${want_overview}" want_overview)

    if (NOT "${wantDoc}" MATCHES "Y|y")
        continue()
    endif()


    string(STRIP "${url_target}" url_target)
    string(STRIP "${osgeo_kind}" osgeo_kind)
    string(STRIP "${project_name}" project_name)
    string(STRIP "${on_iso}" on_iso)

    if (OSGeoLiveDoc_DEBUG)
        message(STATUS "wantDoc:${wantDoc} \tpage:${project_page} \tversion:${project_version} \twant_quickstart:${want_quickstart} \twant_overview:${want_overview}\turl:${url_target} \tkind:${osgeo_kind}\tname:${project_name}")
    endif()


    list(APPEND  OSGeoLiveDoc_PROJECTS_NAMES  "${project_page}")
    string(CONCAT  OSGeoLiveDoc_PROJECTS_VERSIONS ${OSGeoLiveDoc_PROJECTS_VERSIONS} ".. |version-${project_page}| replace:: ${project_version}\n")

    INSERT_INTO_MAP_LOGO("${project_page}" "${url_target}")
    INSERT_INTO_MAP_SCREENSHOT("${project_page}" "${url_target}")
    INSERT_INTO_MAP_KIND("${project_page}" "${osgeo_kind}")
    INSERT_INTO_MAP_NAME("${project_page}" "${project_name}")
    INSERT_INTO_MAP_WEB("${project_page}" "${url_target}")
    INSERT_INTO_MAP_QUICK("${project_page}" "${want_quickstart}")
    INSERT_INTO_MAP_VMDK("${project_page}" "${on_iso}")
    INSERT_INTO_MAP_VERSION("${project_page}" "${project_version}")


    if ("${want_quickstart}" MATCHES "Y")
        list(APPEND OSGeoLiveDoc_QUICKSTART "${project_page}")
    endif()
    if ("${want_overview}" MATCHES "Y")
        list(APPEND OSGeoLiveDoc_OVERVIEW "${project_page}")
    endif()
endforeach()


if (OSGeoLiveDoc_VERBOSE_DEBUG)
    message(STATUS "OSGeoLiveDoc_OVERVIEW=${OSGeoLiveDoc_OVERVIEW}")
    message(STATUS "OSGeoLiveDoc_QUICKSTART=${OSGeoLiveDoc_QUICKSTART}")
    message(STATUS "OSGeoLiveDoc_PROJECTS_NAMES=${OSGeoLiveDoc_QUICKSTART}")
endif()

set(OSGeoLiveDoc_STANDARDS
    "csw"  "gml"  "sld"  "wcs"  "wms"
    "fe"   "kml"  "sensorml"  "sos"  "wfs"  "wps"
    )

if(FALSE)
    # Quickstart only
    set(OSGeoLiveDoc_QUICKSTART
        ${OSGeoLiveDoc_PROJECTS}
        "internationalisation"
        "osgeolive_install"
        "osgeolive"
        "usb"
        "virtualbox"
        "virtualization"
        "vmware"
        )
endif()

configure_file("licenses.csv" "licenses.csv")
configure_file("contributors.csv" "contributors.csv")
configure_file("translators.csv" "translators.csv")

set(SPHINX_BUILD_DIR "${CMAKE_CURRENT_BINARY_DIR}/_build")
add_subdirectory(doc)
add_subdirectory(presentation)
