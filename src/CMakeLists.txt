# Build for LuaBind
# Ryan Pavlik <rpavlik@iastate.edu>
# http://academic.cleardefinition.com/
# Iowa State University HCI Graduate Program/VRAC

file(GLOB LUABIND_SRCS *.cpp)

file(GLOB LUABIND_API ../luabind/*.hpp)
source_group(API FILES ${LUABIND_API})

file(GLOB LUABIND_DETAIL_API ../luabind/detail/*.hpp)
source_group(Internal FILES ${LUABIND_DETAIL_API})

set(LUABIND_EXTRA_LIBS)
if(MSVC)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4251")
elseif(CMAKE_COMPILER_IS_GNUCXX)
	list(APPEND LUABIND_EXTRA_LIBS m)
endif()

add_library(luabind ${LUABIND_SRCS} ${LUABIND_API} ${LUABIND_DETAIL_API})
target_link_libraries(luabind ${LUA_LIBRARIES} ${LUABIND_EXTRA_LIBS})
set_property(TARGET
	luabind PROPERTY
	COMPILE_FLAGS
	${CMAKE_SHARED_LIBRARY_C_FLAGS})

set_property(TARGET
	luabind
	PROPERTY
	PROJECT_LABEL
	"LuaBind Library")

set(ALLHEADERS ${LUABIND_API} ${LUABIND_DETAIL_API} PARENT_SCOPE)
set(APIHEADERS ${LUABIND_API} PARENT_SCOPE)

if(INSTALL_LUABIND)
	if(NOT INCLUDE_DIR)
		set(INCLUDE_DIR include)
	endif()
	if(NOT LIB_DIR)
		set(LIB_DIR lib)
	endif()
	if(NOT ARCH_DIR)
		set(ARCH_DIR lib)
	endif()
	if(NOT BIN_DIR)
		set(BIN_DIR bin)
	endif()
	install(FILES ${LUABIND_API}
		DESTINATION ${INCLUDE_DIR}/luabind
		COMPONENT sdk)
	install(FILES ${LUABIND_DETAIL_API}
		DESTINATION ${INCLUDE_DIR}/luabind/detail
		COMPONENT sdk)
	install(TARGETS luabind
		EXPORT luabind
		RUNTIME DESTINATION ${BIN_DIR} COMPONENT runtime
		LIBRARY DESTINATION ${LIB_DIR} COMPONENT runtime
		ARCHIVE DESTINATION ${ARCH_DIR} COMPONENT sdk)
endif()
