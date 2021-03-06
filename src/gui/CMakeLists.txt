set(QT_USE_QTNETWORK TRUE)

set(tagainijisho_gui_SRCS
TJSpinBox.cc
TJLineEdit.cc
EntryFormatter.cc
RelativeDateEdit.cc
ProxyPaintEngine.cc
BookletPrintEngine.cc
BookletPrinter.cc
EntriesPrinter.cc
KanjiValidator.cc
TagsDialogs.cc
FlowLayout.cc
MultiStackedWidget.cc
EntryMenu.cc
EditEntryNotesDialog.cc
SavedSearchesOrganizer.cc
UpdateChecker.cc
SingleEntryView.cc
SearchFilterWidget.cc
EntryTypeFilterWidget.cc
TextFilterWidget.cc
StudyFilterWidget.cc
JLPTFilterWidget.cc
TagsFilterWidget.cc
NotesFilterWidget.cc
SearchBuilder.cc
SmoothScroller.cc
ScrollBarSmoothScroller.cc
EntryDelegate.cc
EntryListModel.cc
ResultsList.cc
BatchHandler.cc
EntriesViewHelper.cc
ResultsView.cc
ResultsViewWidget.cc
SearchWidget.cc
TemplateFiller.cc
DetailedView.cc
EntryListView.cc
EntryListWidget.cc
ToolBarDetailedView.cc
TrainSettings.cc
YesNoTrainer.cc
ReadingTrainer.cc
PreferencesWindow.cc
MainWindow.cc
)

set(tagainijisho_gui_UIS
ReadingTrainer.ui
SavedSearchesOrganizer.ui
PreferencesWindow.ui
TrainSettings.ui
AboutDialog.ui
ResultsViewWidget.ui
SearchWidget.ui
EntryListWidget.ui
EntryDelegatePreferences.ui
DetailedViewPreferences.ui
ResultsViewPreferences.ui
ListsViewPreferences.ui
GeneralPreferences.ui
DataPreferences.ui
MainWindow.ui
)

# Add rules for jmdict and kanjidic components
add_subdirectory(jmdict)
add_subdirectory(kanjidic2)

qt5_wrap_ui(tagainijisho_gui_UI_SRCS ${tagainijisho_gui_UIS})
qt5_add_resources(tagainijisho_gui_RC ${CMAKE_SOURCE_DIR}/tagainijisho.qrc)

add_library(tagaini_gui STATIC ${tagainijisho_gui_UI_SRCS} ${tagainijisho_gui_SRCS})
target_link_libraries(tagaini_gui tagaini_core tagaini_sqlite Qt5::Core Qt5::Widgets Qt5::PrintSupport Qt5::Network ${extra_link_flags})

# Build the main program
SET(ICON "")
if(APPLE)
	SET_SOURCE_FILES_PROPERTIES(appicon.icns PROPERTIES MACOSX_PACKAGE_LOCATION Resources)
	set(ICON ${CMAKE_CURRENT_SOURCE_DIR}/appicon.icns)
endif(APPLE)

set(tagainijisho_SRCS main.cc ${tagainijisho_gui_RC} ${ICON})

if (WIN32)
	if (MSVC)
		add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/tagainijisho_rc.obj COMMAND ${CMAKE_RC_COMPILER} -I${CMAKE_SOURCE_DIR}/src/gui -i${CMAKE_SOURCE_DIR}/src/gui/tagainijisho.rc  ${CMAKE_CURRENT_BINARY_DIR}/tagainijisho_rc.res)
	else()
		add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/tagainijisho_rc.obj COMMAND ${CMAKE_RC_COMPILER} -I${CMAKE_SOURCE_DIR}/src/gui -i${CMAKE_SOURCE_DIR}/src/gui/tagainijisho.rc -o ${CMAKE_CURRENT_BINARY_DIR}/tagainijisho_rc.obj)
		set(tagainijisho_SRCS ${tagainijisho_SRCS} ${CMAKE_CURRENT_BINARY_DIR}/tagainijisho_rc.obj)
	endif()
endif()

if (APPLE)
	set(tagainijisho_SRCS ${tagainijisho_SRCS} ${MACOSX_BUNDLE_ICON_FILE})
endif()

add_executable(${tagaini_binary} MACOSX_BUNDLE WIN32 ${tagainijisho_SRCS})
target_link_libraries(${tagaini_binary} tagaini_gui_jmdict tagaini_gui_kanjidic2 tagaini_gui tagaini_core_jmdict tagaini_core_kanjidic2 tagaini_core tagaini_sqlite ${extra_link_flags})

# TODO not clean!
if(NOT WIN32)
install(TARGETS ${tagaini_binary} BUNDLE DESTINATION . RUNTIME DESTINATION bin)
else(NOT WIN32)
install(TARGETS ${tagaini_binary} BUNDLE DESTINATION . RUNTIME DESTINATION ".")
endif(NOT WIN32)

# If needed, add standard dependency to have identical build on all platforms
if (NOT ${tagaini_binary} MATCHES "tagainijisho")
	add_custom_target(tagainijisho DEPENDS ${tagaini_binary})
endif()
