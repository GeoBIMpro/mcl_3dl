cmake_minimum_required(VERSION 2.8.3)

find_package(catkin REQUIRED COMPONENTS roscpp rostest nav_msgs tf)

catkin_add_gtest(test_vec3 src/test_vec3.cpp)
target_link_libraries(test_vec3 ${catkin_LIBRARIES})
catkin_add_gtest(test_quat src/test_quat.cpp)
target_link_libraries(test_quat ${catkin_LIBRARIES})
catkin_add_gtest(test_nd src/test_nd.cpp)
target_link_libraries(test_nd ${catkin_LIBRARIES})
catkin_add_gtest(test_filter src/test_filter.cpp)
target_link_libraries(test_filter ${catkin_LIBRARIES})
catkin_add_gtest(test_pf src/test_pf.cpp)
target_link_libraries(test_pf ${catkin_LIBRARIES})

if(MCL_3DL_EXTRA_TESTS)

  catkin_download_test_data(
      ${PROJECT_NAME}_short_test.bag
      https://openspur.org/~atsushi.w/dataset/mcl_3dl/short_test.bag
      MD5 664ecb2824d81247ce4b26c599d99230
      FILENAME test/short_test.bag)

  catkin_download_test_data(
      ${PROJECT_NAME}_short_test_ref.topic
      https://openspur.org/~atsushi.w/dataset/mcl_3dl/short_test_ref.topic
      MD5 1087915e65da06b55e0cc5e4377b485d
      FILENAME test/short_test_ref.topic)

  configure_file(tests/localization_rostest.test.in
      ${PROJECT_BINARY_DIR}/test/tests/localization_rostest.test)
  configure_file(tests/tf_rostest.test.in
      ${PROJECT_BINARY_DIR}/test/tests/tf_rostest.test)

  add_rostest_gtest(compare_pose ${PROJECT_BINARY_DIR}/test/tests/localization_rostest.test
      src/compare_pose.cpp)
  target_link_libraries(compare_pose ${catkin_LIBRARIES})
  add_dependencies(compare_pose 
        ${PROJECT_NAME}_short_test.bag
        ${PROJECT_NAME}_short_test_ref.topic
        mcl_3dl
      )
  add_rostest_gtest(compare_tf ${PROJECT_BINARY_DIR}/test/tests/tf_rostest.test
      src/compare_tf.cpp)
  target_link_libraries(compare_tf ${catkin_LIBRARIES})
  add_dependencies(compare_tf 
        ${PROJECT_NAME}_short_test.bag
        ${PROJECT_NAME}_short_test_ref.topic
        mcl_3dl
      )

else(MCL_3DL_EXTRA_TESTS)

  message(WARNING "To perform extra heavy tests for mcl_3dl, use -DMCL_3DL_EXTRA_TESTS:=ON")

endif(MCL_3DL_EXTRA_TESTS)

