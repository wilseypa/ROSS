INCLUDE_DIRECTORIES(${ROSSNET_SOURCE_DIR}/kernel)
INCLUDE_DIRECTORIES(${ROSS_TOP_SOURCE_DIR}/ross)
SET(rn_srcs
rn-link.c		rn-timer.c
rn-lp.c 		rn-machine.c
hash-quadratic.c	rn-message.c
rn-area.c 		rn-models.c
rn-as.c 		rn-reverse.c
rn-event.c 		rn-routing.c
rn-setup.c 		rn-global.c 
rn-stream.c 		rn-init.c 
rn-subnet.c 		xml-libxml.c)

INCLUDE(CheckIncludeFiles)
INCLUDE(CheckFunctionExists)
INCLUDE(CheckLibraryExists)
CHECK_FUNCTION_EXISTS(bzero HAVE_BZERO)
CHECK_INCLUDE_FILES(dlfcn.h HAVE_DLFCN_H)
CHECK_INCLUDE_FILES(errno.h HAVE_ERRNO_H)
CHECK_INCLUDE_FILES(fcntl.h HAVE_FCNTL_H)
CHECK_FUNCTION_EXISTS(floor HAVE_FLOOR)
CHECK_FUNCTION_EXISTS(gettimeofday HAVE_GETTIMEOFDAY)
CHECK_INCLUDE_FILES(inttypes.h HAVE_INTTYPES_H)
CHECK_LIBRARY_EXISTS(m sin /usr/lib HAVE_LIBM)
CHECK_INCLUDE_FILES(pthread.h HAVE_PTHREAD_H)
CHECK_LIBRARY_EXISTS(pthread pthread_create /usr/lib HAVE_LIBPTHREAD)
CHECK_LIBRARY_EXISTS(z deflateReset /usr/lib HAVE_LIBZ)
CHECK_INCLUDE_FILES(limits.h HAVE_LIMITS_H)
CHECK_FUNCTION_EXISTS(malloc HAVE_MALLOC)
CHECK_INCLUDE_FILES(math.h HAVE_MATH_H)
CHECK_FUNCTION_EXISTS(memcpy HAVE_MEMCPY)
CHECK_INCLUDE_FILES(memory.h HAVE_MEMORY_H)
CHECK_FUNCTION_EXISTS(pow HAVE_POW)
CHECK_FUNCTION_EXISTS(sigaction HAVE_SIGACTION)
CHECK_INCLUDE_FILES(signal.h HAVE_SIGNAL_H)
CHECK_INCLUDE_FILES(stdarg.h HAVE_STDARG_H)
#/* Define to 1 if stdbool.h conforms to C99. */
#undef HAVE_STDBOOL_H
CHECK_INCLUDE_FILES(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILES(stdio.h HAVE_STDIO_H)
CHECK_INCLUDE_FILES(stdlib.h HAVE_STDLIB_H)
CHECK_INCLUDE_FILES(strings.h HAVE_STRINGS_H)
CHECK_INCLUDE_FILES(string.h HAVE_STRING_H)
CHECK_INCLUDE_FILES(sys/stat.h HAVE_SYS_STAT_H)
CHECK_INCLUDE_FILES(sys/time.h HAVE_SYS_TIME_H)
CHECK_INCLUDE_FILES(sys/types.h HAVE_SYS_TYPES_H)
CHECK_INCLUDE_FILES(unistd.h HAVE_UNISTD_H)
CHECK_FUNCTION_EXISTS(vprintf HAVE_VPRINTF)
#/* Define to 1 if the system has the type `_Bool'. */
#undef HAVE__BOOL
CONFIGURE_FILE(config.h.cmake ${CMAKE_CURRENT_SOURCE_DIR}/rossnet_config.h)

FIND_PACKAGE(LibXml2 REQUIRED)
#SET(MPI_COMPILER mpicc)
#FIND_PACKAGE(MPI REQUIRED)
#LINK_DIRECTORIES(${ROSSNET_SOURCE_DIR}/../ross)
#LINK_DIRECTORIES(/Users/laprej/pdes/trunk/ross/)
INCLUDE_DIRECTORIES(${LIBXML2_INCLUDE_DIR})

IF(WITH_NETDMF)
	# Must Have NetDMF
	FIND_PACKAGE(NetDMF NO_MODULE REQUIRED)
	IF(NetDMF_FOUND)
		INCLUDE_DIRECTORIES(${NETDMF_INCLUDE_DIRS})

#		IF(NETDMF_BUILD_MPI)
#		INCLUDE_DIRECTORIES(
#		${NETDMF_MPI_INCLUDE_PATH})
#		ENDIF(NETDMF_BUILD_MPI)

		LINK_DIRECTORIES(${NETDMF_LIBRARY_DIRS})
		LINK_DIRECTORIES(${NETDMF_LIBRARY_DIRS}/paraview-3.8)

#		SET( NetDmf_SRCS
#		     vtkNetDmfReader.cxx
#		     )

#	TARGET_LINK_LIBRARIES(NetDMF)

	ENDIF(NetDMF_FOUND)

	SET(rn_srcs ${rn_srcs} rn-netdmf.c rn-netdmf-wrapper.cpp)

	FIND_PACKAGE(HDF5)
	FIND_PACKAGE(XDMF NO_MODULE REQUIRED)

	INCLUDE_DIRECTORIES(${HDF5_INCLUDE_DIR})
	INCLUDE_DIRECTORIES(${XDMF_INCLUDE_DIRS})

	LINK_DIRECTORIES(${XDMF_LIBRARY_DIRS})
	LINK_DIRECTORIES(${HDF5_LIBRARY_DIRS})
	SET(RN_LIBS ${RN_LIBS} Xdmf NetDMF)

ENDIF(WITH_NETDMF)


ADD_LIBRARY(ROSSNET ${rn_srcs})
ADD_EXECUTABLE(rn rn.c)


IF(WITH_IP)
	SET(RN_LIBS ${RN_LIBS} ip)
ENDIF(WITH_IP)

IF(WITH_TCP)
	SET(RN_LIBS ${RN_LIBS} tcp)
ENDIF(WITH_TCP)

IF(WITH_EPI)
	SET(RN_LIBS ${RN_LIBS} epi)
ENDIF(WITH_EPI)

IF(WITH_OSPF)
	SET(RN_LIBS ${RN_LIBS} ospf)
ENDIF(WITH_OSPF)

IF(WITH_BGP)
	SET(RN_LIBS ${RN_LIBS} bgp)
ENDIF(WITH_BGP)

IF(WITH_FTP)
	SET(RN_LIBS ${RN_LIBS} ftp)
ENDIF(WITH_FTP)

IF(WITH_PHOLD)
	SET(RN_LIBS ${RN_LIBS} phold)
ENDIF(WITH_PHOLD)

IF(WITH_MULTICAST)
	SET(RN_LIBS ${RN_LIBS} multicast)
ENDIF(WITH_MULTICAST)

IF(WITH_TLM)
	SET(RN_LIBS ${RN_LIBS} tlm)
ENDIF(WITH_TLM)

IF(WITH_RANDOM_WALK)
	SET(RN_LIBS ${RN_LIBS} rw)
ENDIF(WITH_RANDOM_WALK)

IF(WITH_ROBOT_PHYSSYM)
	SET(RN_LIBS ${RN_LIBS} rp)
ENDIF(WITH_ROBOT_PHYSSYM)

TARGET_LINK_LIBRARIES(rn ${LIBXML2_LIBRARIES} ${RN_LIBS} ROSSNET ROSS ${RN_LIBS} ${LIBXML2_LIBRARIES})
