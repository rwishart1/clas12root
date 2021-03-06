set(CLAS12ROOT Clas12Root)
set(CLAS12BANKS Clas12Banks)
set(HIPO Hipo4)

#include rcdb c++ header library
IF (DEFINED ENV{RCDB_HOME})
  include_directories($ENV{RCDB_HOME}/cpp/include)
  #find path to mysql include directory
  FIND_PATH(MYSQL_INCLUDE_DIR mysql.h
    /usr/local/include/mysql
    /usr/include/mysql
    )

  #find path to mysql library
  SET(MYSQL_NAMES mysqlclient mysqlclient_r)
  FIND_LIBRARY(MYSQL_LIBRARY
    NAMES ${MYSQL_NAMES}
    PATHS /usr/lib /usr/local/lib /usr/lib64 /usr/local/lib64
    PATH_SUFFIXES mysql
    )

  #include mysql library and directory if they were found
  IF (MYSQL_INCLUDE_DIR AND MYSQL_LIBRARY)
    include_directories(${MYSQL_INCLUDE_DIR})
    link_libraries(${MYSQL_LIBRARY})
    add_definitions(-DRCDB_MYSQL)
  ENDIF (MYSQL_INCLUDE_DIR AND MYSQL_LIBRARY)

ENDIF (DEFINED ENV{RCDB_HOME})

ROOT_GENERATE_DICTIONARY(G__${CLAS12ROOT}  BankHist.h HipoHist.h HipoTreeMaker.h HipoChain.h HipoRootAction.h   HipoROOTOut.h ParticleHist.h HipoSelector.h ParticleTree.h LINKDEF Clas12RootLinkDef.h)

add_library(${CLAS12ROOT} SHARED BankHist.cpp HipoHist.cpp HipoTreeMaker.cpp HipoChain.cpp HipoRootAction.cpp   HipoROOTOut.cpp ParticleHist.cpp  HipoSelector.cpp ParticleTree.cpp G__${CLAS12ROOT}.cxx)

target_link_libraries(${CLAS12ROOT} ${ROOT_LIBRARIES} ${HIPO} ${CLAS12BANKS})


install(TARGETS ${CLAS12ROOT} 
  LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}")

FILE(GLOB ROOT_C12ROOT_PCM ${CMAKE_BINARY_DIR}/Clas12Root/*pcm)

install (FILES  ${ROOT_C12ROOT_PCM}    
  DESTINATION "${CMAKE_INSTALL_LIBDIR}")

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src)
add_subdirectory (${CMAKE_CURRENT_SOURCE_DIR}/src)
