
node ('atlasnode') {
  // Cleanup previous runs
  stage('Cleanup') {
    deleteDir()
  } // end Cleanup

  // Checkout git repositories
  stage('Checkout') {
  	dir('llvm-reference') {
      git url: 'https://github.com/PaoloS02/llvm-reference.git', branch: 'master'
    }
    
    dir('llvm-reference/tools/clang') {
      git url: 'https://github.com/PaoloS02/clang-reference.git', branch: 'master'
    }
  
  }
  
   stage('Build') {
    docker.image('embecosm/builder:ubuntu-16.04').inside {
      sh script: '''THREADS=`cat /proc/cpuinfo | grep -c processor`
      mkdir build-reference && cd build-reference && cmake -DCMAKE_BUILD_TYPE=Debug -DLLVM_ENABLE_ASSERTIONS=ON -DBUILD_SHARED_LIBS=ON  ../llvm-reference && make -j $THREADS'''
      
    	}
 	}
 	
 	stage('LLVM Regression') {
    docker.image('embecosm/builder:ubuntu-16.04').inside {
      sh script: '''cd build-reference && make check > "${WORKSPACE}/llvm-reference-tests.log" || true'''
      archiveArtifacts allowEmptyArchive: true, artifacts: 'llvm-reference-tests.log'
    }
  }
 
 }
