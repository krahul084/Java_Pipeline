pipeline {
	agent none

	environment {
		MAJOR_VERSION = 1
	}
 
	stages {
	    stage('Unit Tests') {
	      agent {
	        label 'apache'
	      }
	      steps {

	         sh 'ant -f test.xml -v'
	         junit 'reports/result.xml'

	      }

	    }

	    stage('build') {
	      agent {
	        label 'apache'
	      }
	      steps {
	        sh 'ant -f build.xml -v'

	      }
	      post { 
	        success {
	          archiveArtifacts artifacts:'dist/*jar, fingerprint:true'
	        }
	      }
	    }

	    stage('deploy') {
	      agent{
	        label 'apache'
	      }
	      steps {
	        sh "if [ ! -d '/var/www/html/rectangles/all' ]; then mkdir -p /var/www/html/rectangles/all/green; fi"

	        sh "cp -rp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
	      }
	    }

	    stage("Testing the Built Jar") {
	      agent {
	        label 'centos'
	      }
	      steps {
	        sh "wget http://krahulchowdary6.mylabserver.com/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
	        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 5 6"
		  }

		}

		stage('Promote to Green') {
		  agent {
		    label 'apache'
		  }
		  steps{
		    sh "cp /var/www/html/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/green/"
		  }
		}
    }
}
