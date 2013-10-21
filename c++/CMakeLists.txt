# include(ExternalProject)
 if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/gtest/")
     message("GTEST PRESENT")
 else()
     message("GTEST ABSENT")
     EXECUTE_PROCESS(COMMAND svn checkout http://googletest.googlecode.com/svn/tags/release-1.6.0 ${CMAKE_CURRENT_SOURCE_DIR}/gtest)
      EXECUTE_PROCESS( COMMAND patch -d ${CMAKE_CURRENT_SOURCE_DIR}/gtest -p0 -i ${CMAKE_CURRENT_SOURCE_DIR}/gtest-1.6.0-c++11.patch ) 
  endif()

  add_subdirectory(gtest)
  include_directories(gtest/include)
#  ExternalProject_Add(
#      gtest
#      SVN_REPOSITORY http://googletest.googlecode.com/svn/tags/release-1.6.0
#      PATCH_COMMAND patch -t -p0 < ${CMAKE_CURRENT_SOURCE_DIR}/gtest-1.6.0-c++11.patch
#      CMAKE_ARGS -DCMAKE_CXX_FLAGS=-std=c++11 -stdlib=libc++
# )

INCLUDE_DIRECTORIES(src)

SET(KJ_SOURCES
    src/kj/common.c++
    src/kj/units.c++      
    src/kj/memory.c++     
    src/kj/refcount.c++   
    src/kj/array.c++      
    src/kj/string.c++     
    src/kj/string-tree.c++
    src/kj/exception.c++  
    src/kj/debug.c++      
    src/kj/arena.c++      
    src/kj/io.c++         
    src/kj/mutex.c++      
    src/kj/thread.c++     
    src/kj/async.c++      
    src/kj/main.c++       
    src/kj/parse/char.c++
    src/kj/common.h     
    src/kj/units.h      
    src/kj/memory.h     
    src/kj/refcount.h   
    src/kj/array.h      
    src/kj/vector.h     
    src/kj/string.h     
    src/kj/string-tree.h 
    src/kj/exception.h   
    src/kj/debug.h       
    src/kj/arena.h       
    src/kj/io.h          
    src/kj/tuple.h       
    src/kj/one-of.h      
    src/kj/function.h    
    src/kj/mutex.h       
    src/kj/thread.h      
    src/kj/async.h       
    src/kj/main.h        
    )

ADD_LIBRARY(kj SHARED ${KJ_SOURCES})

SET(CAPNP_SOURCES
    src/capnp/c++.capnp.c++        
    src/capnp/blob.c++             
    src/capnp/arena.h              
    src/capnp/arena.c++            
    src/capnp/layout.c++           
    src/capnp/list.c++             
    src/capnp/object.c++           
    src/capnp/message.c++          
    src/capnp/capability.c++       
    src/capnp/capability-context.c++
    src/capnp/schema.capnp.c++     
    src/capnp/schema.c++           
    src/capnp/schema-loader.c++    
    src/capnp/dynamic.c++          
    src/capnp/stringify.c++        
    src/capnp/serialize.c++        
    src/capnp/serialize-packed.c++
    src/capnp/c++.capnp.h               
    src/capnp/common.h                  
    src/capnp/blob.h                    
    src/capnp/endian.h                  
    src/capnp/layout.h                  
    src/capnp/orphan.h                  
    src/capnp/list.h                    
    src/capnp/object.h                  
    src/capnp/message.h                 
    src/capnp/capability.h              
    src/capnp/capability-context.h      
    src/capnp/schema.capnp.h            
    src/capnp/schema.h                  
    src/capnp/schema-loader.h           
    src/capnp/schema-parser.h           
    src/capnp/dynamic.h                 
    src/capnp/pretty-print.h            
    src/capnp/serialize.h               
    src/capnp/serialize-packed.h        
    src/capnp/pointer-helpers.h         
    src/capnp/generated-header-support.h)


ADD_LIBRARY(lcapnp SHARED ${CAPNP_SOURCES})
TARGET_LINK_LIBRARIES(lcapnp kj)

SET(CAPNPC_SOURCES
    src/capnp/compiler/md5.h               
    src/capnp/compiler/md5.c++             
    src/capnp/compiler/error-reporter.h    
    src/capnp/compiler/error-reporter.c++  
    src/capnp/compiler/lexer.capnp.h       
    src/capnp/compiler/lexer.capnp.c++     
    src/capnp/compiler/lexer.h             
    src/capnp/compiler/lexer.c++           
    src/capnp/compiler/grammar.capnp.h     
    src/capnp/compiler/grammar.capnp.c++   
    src/capnp/compiler/parser.h            
    src/capnp/compiler/parser.c++          
    src/capnp/compiler/node-translator.h   
    src/capnp/compiler/node-translator.c++ 
    src/capnp/compiler/compiler.h          
    src/capnp/compiler/compiler.c++        
    src/capnp/schema-parser.c++)
ADD_LIBRARY(lcapnpc SHARED ${CAPNPC_SOURCES})
TARGET_LINK_LIBRARIES(lcapnpc lcapnp kj)

SET(CAPNP_BIN_SOURCES 
    src/capnp/compiler/module-loader.h     
    src/capnp/compiler/module-loader.c++   
    src/capnp/compiler/capnp.c++) 
ADD_EXECUTABLE(capnp ${CAPNP_BIN_SOURCES})
TARGET_LINK_LIBRARIES(capnp kj lcapnp lcapnpc)


SET(CAPNPC_BIN_SOURCES src/capnp/compiler/capnpc-capnp.c++)
ADD_EXECUTABLE(capnpc ${CAPNPC_BIN_SOURCES})
TARGET_LINK_LIBRARIES(capnpc kj lcapnp)

SET(CAPNPC-C++_BIN_SOURCES src/capnp/compiler/capnpc-c++.c++)
ADD_EXECUTABLE(capnpc-c++  ${CAPNPC-C++_BIN_SOURCES})
TARGET_LINK_LIBRARIES(capnpc-c++ kj lcapnp)

# TESTS ##################################################################
# use capnp binary to generate
SET(TEST_CAPNPC_INPUTS
    src/capnp/test.capnp        
    src/capnp/test-import.capnp 
    src/capnp/test-import2.capnp)

SET(TEST_CAPNPC_OUTPUTS 
           ${CMAKE_CURRENT_SOURCE_DIR}/src/capnp/test.capnp.c++         
           ${CMAKE_CURRENT_SOURCE_DIR}/src/capnp/test.capnp.h           
           ${CMAKE_CURRENT_SOURCE_DIR}/src/capnp/test-import.capnp.c++  
           ${CMAKE_CURRENT_SOURCE_DIR}/src/capnp/test-import.capnp.h    
           ${CMAKE_CURRENT_SOURCE_DIR}/src/capnp/test-import2.capnp.c++ 
           ${CMAKE_CURRENT_SOURCE_DIR}/src/capnp/test-import2.capnp.h )
MESSAGE(${PROJECT_BINARY_DIR})

set(RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

if(CMAKE_GENERATOR STREQUAL Xcode)
  SET(capnpc-c++-bin ${CMAKE_CURRENT_BINARY_DIR}/$<CONFIGURATION>/capnpc-c++)
else()
  SET(capnpc-c++-bin ${CMAKE_CURRENT_BINARY_DIR}/capnpc-c++)
endif()

ADD_CUSTOM_COMMAND(
    OUTPUT ${TEST_CAPNPC_OUTPUTS}
    COMMAND capnp compile -o${capnpc-c++-bin}:. -Isrc ${TEST_CAPNPC_INPUTS}
    DEPENDS capnpc-c++ capnp
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    ) 



SET(CAPNP_TEST_SOURCES
    ${TEST_CAPNPC_OUTPUTS}
    src/kj/common-test.c++                    
    src/kj/memory-test.c++                    
    src/kj/refcount-test.c++                  
    src/kj/array-test.c++                     
    src/kj/string-test.c++                    
    src/kj/string-tree-test.c++               
    src/kj/exception-test.c++                 
    src/kj/debug-test.c++                     
    src/kj/arena-test.c++                     
    src/kj/units-test.c++                     
    src/kj/tuple-test.c++                     
    src/kj/one-of-test.c++                    
    src/kj/function-test.c++
    src/kj/mutex-test.c++                     
    src/kj/async-test.c++                     
    src/kj/parse/common-test.c++              
    src/kj/parse/char-test.c++                
    src/capnp/common-test.c++                 
    src/capnp/blob-test.c++                   
    src/capnp/endian-test.c++                 
    src/capnp/endian-fallback-test.c++        
    src/capnp/endian-reverse-test.c++         
    src/capnp/layout-test.c++                 
    src/capnp/object-test.c++                 
    src/capnp/message-test.c++                
    src/capnp/capability-test.c++             
    src/capnp/schema-test.c++                 
    src/capnp/schema-loader-test.c++          
    src/capnp/dynamic-test.c++                
    src/capnp/stringify-test.c++              
    src/capnp/encoding-test.c++               
    src/capnp/orphan-test.c++                 
    src/capnp/serialize-test.c++              
    src/capnp/serialize-packed-test.c++       
    src/capnp/test-util.c++                   
    src/capnp/test-util.h                     
    src/capnp/compiler/lexer-test.c++         
    src/capnp/compiler/md5-test.c++
    )

ADD_EXECUTABLE(capnp-test ${CAPNP_TEST_SOURCES})
TARGET_LINK_LIBRARIES(capnp-test lcapnp lcapnpc kj gtest gtest_main)
add_test(capnp-test capnp-test)


ADD_EXECUTABLE(evolution-test src/capnp/compiler/evolution-test.c++)
TARGET_LINK_LIBRARIES(evolution-test lcapnpc lcapnp kj)
add_test(evolution-test evolution-test)
