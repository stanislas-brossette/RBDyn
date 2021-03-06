ENABLE_TESTING()

set(BOOST_COMPONENTS unit_test_framework timer system)
SEARCH_FOR_BOOST()
add_definitions(-DBOOST_TEST_DYN_LINK)

add_definitions(-DTESTS_DATA_DIR="${PROJECT_SOURCE_DIR}/tests")
include_directories("${PROJECT_SOURCE_DIR}/src")
include_directories(${Boost_INCLUDE_DIRS})

set(HEADERS XXXarm.h XYZarm.h XYZSarm.h Tree30Dof.h SSSarm.h)

macro(addUnitTest name)
  if(${UNIT_TESTS})
    add_executable(${name} ${name}.cpp ${HEADERS})
    target_link_libraries(${name} ${Boost_LIBRARIES} RBDyn)
    PKG_CONFIG_USE_DEPENDENCY(${name} SpaceVecAlg)
    add_test(${name}Unit ${name})
  endif()
endmacro(addUnitTest)

macro(addBenchmark name)
  if(${BENCHMARKS})
    add_executable(${name} ${name}.cpp ${HEADERS})
    target_link_libraries(${name} ${Boost_LIBRARIES} RBDyn)
    PKG_CONFIG_USE_DEPENDENCY(${name} SpaceVecAlg)
  endif()
endmacro(addBenchmark)

addUnitTest("BodyTest")
addUnitTest("JointTest")
addUnitTest("MultiBodyTest")
addUnitTest("AlgoTest")
addUnitTest("JacobianTest")
addUnitTest("DynamicsTest")
addUnitTest("CoMTest")
addUnitTest("MomentumTest")
addUnitTest("IDIMTest")
addUnitTest("InverseStaticsTest")

addBenchmark("JacobianBench")
addBenchmark("DynamicsBench")

if(${PYTHON_BINDING})
  set(PYTHON_TEST_COMMAND "${PYTHON_EXECUTABLE}")
  configure_file(rbd_pickle_test.py ${CMAKE_CURRENT_BINARY_DIR}/rbd_pickle_test.py)
  add_test(rbdPickleTestUnit ${PYTHON_TEST_COMMAND}
    "${CMAKE_CURRENT_BINARY_DIR}/rbd_pickle_test.py")
endif()
