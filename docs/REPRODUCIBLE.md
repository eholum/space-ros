
# Reproducible Builds

This section describes how to rebuild Space ROS and run the unit tests in the Docker container.
Make sure the dev variant of the space ROS image is ready before proceeding.

## Preparing Space ROS sources

A manifest of the exact sources of Space ROS used to produce the current image is saved as `spaceros.repos` in the `/opt/ros/spaceros/scripts` directory.
This manifest is preserved in the `${HOME}/spaceros_ws/src` directory in the dev image.

```bash
cd ${HOME}/spaceros_ws
```

From there you can run a new debug build or and compile tests,

```bash
colcon build \
  --merge-install \
  --install-base ${SPACEROS_DIR} \
  --cmake-args \
  -DBUILD_TESTING=ON \
  -DCMAKE_BUILD_TYPE=Debug \
  -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
  --no-warn-unused-cli
```

Then execute tests with,

```bash
colcon test \
  --merge-install \
  --install-base ${SPACEROS_DIR} \
  --ctest-args -LE "(ikos|xfail)" \
  --pytest-args -m "not xfail"
```

## Running Tests

The tests include running the static analysis tools clang_tidy and cppcheck (which has the MISRA 2012 add-on enabled).

You can use colcon's `--packages-select` option to run a subset of packages.
For example, to run tests only for the rcpputils package and display the output directly to the console (as well as saving it to a log file).

```bash
colcon test \
  --merge-install \
  --install-base ${SPACEROS_DIR} \
  --event-handlers console_direct+ \
  --packages-select rcpputils
```

### Viewing Test Output

The output from the tests are stored in XUnit XML files, named *\<tool-name\>*.xunit.xml.
After running the unit tests, you can scan the build directory for the various *\*.xunit.xml* files.

Here is an example `clang_tidy.xunit.xml` file:

```xml
<xml version="1.0" encoding="UTF-8"?>
<testsuite
  name="rmw.clang_tidy"
  tests="21"
  errors="0"
  failures="0"
  time="1.248"
>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/allocators.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/convert_rcutils_ret_to_rmw_ret.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/event.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/init.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/init_options.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/message_sequence.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/names_and_types.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/network_flow_endpoint.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/network_flow_endpoint_array.c"
    classname="rmw.clang_tidy"/>
  <testcase
    name="/home/spaceros-user/spaceros/src/rmw/rmw/src/publisher_options.c"
    classname="rmw.clang_tidy"/>

<etc>
```
