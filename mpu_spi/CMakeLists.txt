############################################################################
#
#   Copyright (C) 2015 Mark Charlebois. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in
#    the documentation and/or other materials provided with the
#    distribution.
# 3. Neither the name PX4 nor the names of its contributors may be
#    used to endorse or promote products derived from this software
#    without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
# FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
# COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
# BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
# OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
# AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
# LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#
############################################################################

string(REGEX REPLACE "/" "__" MODULE_NAME ${CMAKE_CURRENT_SOURCE_DIR})

set(TOOLS_ERROR_MSG 
		"The FC_ADDON must be installed and the environment variable FC_ADDON must be set"
		"(e.g. export FC_ADDON=${HOME}/Qualcomm/addon)")

if ("$ENV{FC_ADDON}" STREQUAL "")
	message(FATAL_ERROR ${TOOLS_ERROR_MSG})
else()
	set(FC_ADDON $ENV{FC_ADDON})
endif()

add_library(libmpu9x50 SHARED IMPORTED GLOBAL)
set_target_properties(libmpu9x50 PROPERTIES IMPORTED_LOCATION /local/mnt/workspace/wangx/flight_8096/px4_internal/src/drivers/build_adsp/mpu_spi/adsp_proc/libmpu9x50.so)

include_directories(
        ../../drivers/include
        ../../drivers/mpu_spi/include
        ../../drivers/uart_esc/adsp_proc
        ../../drivers/rc_receiver/include
	)

px4_add_module(
	MODULE ${MODULE_NAME}
	MAIN mpu9x50
	STACK_MAIN 1200
	COMPILE_FLAGS
		-Os -Weffc++
	SRCS
		mpu9x50_main.cpp
		mpu9x50_params.c
	DEPENDS
		platforms__common
	)

set(module_external_libraries 
	${module_external_libraries}
	libmpu9x50
	CACHE INTERNAL "module_external_libraries"
	)

# vim: set noet ft=cmake fenc=utf-8 ff=unix : 
