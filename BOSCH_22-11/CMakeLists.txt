cmake_minimum_required(VERSION 3.10)
project(LaneKeepingMPC LANGUAGES CXX)

# Bắt buộc dùng chuẩn C++17 (cho std::clamp, std::filesystem...)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Mặc định build chế độ Release để tối ưu tốc độ giải ma trận MPC (hoặc đổi thành Debug khi cần)
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# 1. Tìm kiếm các thư viện cần thiết
find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OsqpEigen REQUIRED)
find_package(Threads REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBSERIAL REQUIRED libserial)

# 2. Khai báo thư mục chứa các file .hpp
include_directories(
    ${CMAKE_SOURCE_DIR}/include
    ${OpenCV_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIRS}
    ${OsqpEigen_INCLUDE_DIRS}
    ${LIBSERIAL_INCLUDE_DIRS}
)

link_directories(
    ${LIBSERIAL_LIBRARY_DIRS}
)

# 3. Gom toàn bộ 7 file mã nguồn .cpp
set(SOURCES
    src/main.cpp
    src/LaneDetector.cpp
    src/MpcController.cpp
    src/communication.cpp
    src/logger.cpp
    src/Trans_UDP.cpp
    src/logic.cpp
)

# 4. Tạo file thực thi tên là "app"
add_executable(app ${SOURCES})

# 5. Liên kết thư viện vào chương trình
target_link_libraries(app PRIVATE
    ${OpenCV_LIBS}
    Eigen3::Eigen
    OsqpEigen::OsqpEigen
    Threads::Threads
    ${LIBSERIAL_LIBRARIES}
)

# In thông tin kiểm tra cấu hình
message(STATUS "=== LaneKeepingMPC Build Configuration ===")
message(STATUS "Build Type   : ${CMAKE_BUILD_TYPE}")
message(STATUS "OpenCV       : ${OpenCV_VERSION}")
message(STATUS "Eigen3       : ${EIGEN3_INCLUDE_DIRS}")
message(STATUS "OsqpEigen    : ${OsqpEigen_INCLUDE_DIRS}")
message(STATUS "LibSerial    : ${LIBSERIAL_INCLUDE_DIRS}")