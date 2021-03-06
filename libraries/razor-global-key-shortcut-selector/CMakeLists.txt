set(PROJECT_NAME razor-global-key-shortcut-selector)
project(${PROJECT_NAME})

cmake_minimum_required(VERSION 2.6)

if(NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE Release)
endif()



include_directories(
	"${PROJECT_SOURCE_DIR}"
	"${CMAKE_CURRENT_BINARY_DIR}"
	"${CMAKE_SOURCE_DIR}/libraries"
)



add_definitions(-DSHARED_EXPORT=Q_DECL_EXPORT)


set(${PROJECT_NAME}_SOURCES
	shortcut_selector.cpp
)

set(${PROJECT_NAME}_MAIN_HEADER
)

set(${PROJECT_NAME}_PUBLIC_CPP_HEADERS
)

set(${PROJECT_NAME}_PRIVATE_CPP_HEADERS
)

set(${PROJECT_NAME}_PUBLIC_QT_HEADERS
	shortcut_selector.h
)

set(${PROJECT_NAME}_PRIVATE_QT_HEADERS
)

set(${PROJECT_NAME}_FORMS
)

set(${PROJECT_NAME}_RESOURCES
)

set(${PROJECT_NAME}_TRANSLATIONS
)

set(${PROJECT_NAME}_DBUS_ADAPTERS
)

set(${PROJECT_NAME}_DBUS_PROXYS
)



set(${PROJECT_NAME}_PUBLIC_HEADERS
	${${PROJECT_NAME}_PUBLIC_CPP_HEADERS}
	${${PROJECT_NAME}_PUBLIC_QT_HEADERS}
)

set(${PROJECT_NAME}_PRIVATE_HEADERS
	${${PROJECT_NAME}_PRIVATE_CPP_HEADERS}
	${${PROJECT_NAME}_PRIVATE_QT_HEADERS}
)

set(${PROJECT_NAME}_CPP_HEADERS
	${${PROJECT_NAME}_PUBLIC_CPP_HEADERS}
	${${PROJECT_NAME}_PRIVATE_CPP_HEADERS}
)

set(${PROJECT_NAME}_QT_HEADERS
	${${PROJECT_NAME}_PUBLIC_QT_HEADERS}
	${${PROJECT_NAME}_PRIVATE_QT_HEADERS}
)

set(${PROJECT_NAME}_HEADERS
	${${PROJECT_NAME}_PUBLIC_HEADERS}
	${${PROJECT_NAME}_PRIVATE_HEADERS}
)

set(${PROJECT_NAME}_TRANSLATABLE
	${${PROJECT_NAME}_SOURCES}
	${${PROJECT_NAME}_HEADERS}
	${${PROJECT_NAME}_FORMS}
)



find_package(Qt4 COMPONENTS QtCore QtGui QtDBus)

include(${QT_USE_FILE})

foreach(DBUS_ADAPTOR ${${PROJECT_NAME}_DBUS_ADAPTERS})
	get_source_file_property(DBUS_ADAPTOR_INCLUDE ${DBUS_ADAPTOR} PARENT)
	get_source_file_property(DBUS_ADAPTOR_PARENT_CLASSNAME ${DBUS_ADAPTOR} PARENT_CLASSNAME)
	get_source_file_property(DBUS_ADAPTOR_BASENAME ${DBUS_ADAPTOR} BASENAME)
	get_source_file_property(DBUS_ADAPTOR_CLASSNAME ${DBUS_ADAPTOR} CLASSNAME)
	if(DBUS_ADAPTOR_BASENAME)
		if(DBUS_ADAPTOR_CLASSNAME)
			qt4_add_dbus_adaptor(${PROJECT_NAME}_DBUS_ADAPTOR_FILES ${DBUS_ADAPTOR} ${DBUS_ADAPTOR_INCLUDE} ${DBUS_ADAPTOR_PARENT_CLASSNAME} ${DBUS_ADAPTOR_BASENAME} ${DBUS_ADAPTOR_CLASSNAME})
		else()
			qt4_add_dbus_adaptor(${PROJECT_NAME}_DBUS_ADAPTOR_FILES ${DBUS_ADAPTOR} ${DBUS_ADAPTOR_INCLUDE} ${DBUS_ADAPTOR_PARENT_CLASSNAME} ${DBUS_ADAPTOR_BASENAME})
		endif()
	else()
		qt4_add_dbus_adaptor(${PROJECT_NAME}_DBUS_ADAPTOR_FILES ${DBUS_ADAPTOR} ${DBUS_ADAPTOR_INCLUDE} ${DBUS_ADAPTOR_PARENT_CLASSNAME})
	endif()
endforeach()

foreach(DBUS_INTERFACE ${${PROJECT_NAME}_DBUS_PROXYS})
	get_source_file_property(DBUS_INTERFACE_BASENAME ${DBUS_INTERFACE} BASENAME)
	qt4_add_dbus_interface(${PROJECT_NAME}_DBUS_INTERFACE_FILES ${DBUS_INTERFACE} ${DBUS_INTERFACE_BASENAME})
endforeach()

qt4_wrap_cpp(${PROJECT_NAME}_MOC_FILES ${${PROJECT_NAME}_QT_HEADERS})
qt4_add_resources(${PROJECT_NAME}_RESOURCE_FILES ${${PROJECT_NAME}_RESOURCES})
if(COMMAND qt4_wrap_ui)
	qt4_wrap_ui(${PROJECT_NAME}_FORM_FILES ${${PROJECT_NAME}_FORMS})
endif()
#if(COMMAND qt4_create_translation)
#	qt4_create_translation(${${PROJECT_NAME}_QM_FILES} ${${PROJECT_NAME}_TRANSLATABLE} ${${PROJECT_NAME}_TRANSLATIONS})
#endif()



set(${PROJECT_NAME}_GENERATED_FILES
	${${PROJECT_NAME}_MOC_FILES}
	${${PROJECT_NAME}_FORM_FILES}
	${${PROJECT_NAME}_RESOURCE_FILES}
	${${PROJECT_NAME}_QM_FILES}
	${${PROJECT_NAME}_DBUS_INTERFACE_FILES}
	${${PROJECT_NAME}_DBUS_ADAPTOR_FILES}
)

set(${PROJECT_NAME}_ALL_FILES
	${${PROJECT_NAME}_SOURCES}
	${${PROJECT_NAME}_HEADERS}
	${${PROJECT_NAME}_GENERATED_FILES}
)



add_library(${PROJECT_NAME} SHARED ${${PROJECT_NAME}_ALL_FILES})
target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} razor-global-key-shortcuts-client)
set_target_properties(${PROJECT_NAME}
	PROPERTIES
	VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}
	SOVERSION ${MAJOR_VERSION}
)

install(TARGETS ${PROJECT_NAME} DESTINATION lib${LIB_SUFFIX} COMPONENT runtime)
install(FILES ${${PROJECT_NAME}_PUBLIC_HEADERS} DESTINATION include/${PROJECT_NAME} COMPONENT development)

include(create_pkgconfig_file)
create_pkgconfig_file(${PROJECT_NAME} "Razor-qt global key shortcuts GUI support library")
