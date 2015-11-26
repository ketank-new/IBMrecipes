@@ -0,0 +1,113 @@
# Building Logstash
[Logstash](https://www.elastic.co/products/logstash) is written in Ruby and it has a build-in Jruby (running on JVM) that needs a native library jffi-1.2.so for s390x platform.
This recipe is for building Logstash (2.1.0) for Linux on z Systems 

### Build from Release

1. Obtain pre-built dependencies

*	RHEL7:
	```
		yum install -y java-1.7.0-openjdk \
		               ant \
		               make \
		               wget \
		               unzip \
		               tar \
		               gcc
	```
*	RHEL6:
	```
		yum install -y java-1.7.1-ibm-devel.s390x \
		               wget \
		               make \
		               unzip \
		               gcc \
		               tar
	```

*	SLES12:
	```
		zypper install -y --type pattern Basis-Devel
		zypper install -y java-1_7_0-openjdk \
		                  ant \
		                  make \
		                  wget \
		                  unzip \
		                  gcc
	```
	
*	SLES11:
	```
		zypper install -y --type pattern Basis-Devel
        zypper install -y java-1_7_0-ibm-devel \
                          wget \
                          unzip \
                          tar \
                          make \
                          gcc
	```
* Install appropriate version of Ant 

  For SLES 11 and RHEL 6 
 
  ```
       export WORK_DIR=`pwd`
       wget http://archive.apache.org/dist/ant/binaries/apache-ant-1.9.4-bin.tar.gz
       tar -zxvf apache-ant-1.9.4-bin.tar.gz
    ```
* Set environment variable 

  Set JAVA_HOME

    ```
       export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk (for rhel7)
       export JAVA_HOME=/usr/lib64/jvm/java (for sles12)
       export JAVA_HOME=/usr/lib64/jvm/java-1.7.0-ibm (for sles11)
       export JAVA_HOME=/usr/lib/jvm/java-1.7.1-ibm.s390x (for rhel6)
    ```
    
    Set ANT PATH for RHEL6 and SLES11
    ```
       export PATH=$WORK_DIR/apache-ant-1.9.4/bin:$PATH
    ```
2. Get Logstash latest release package and unzip it

   ```
       wget https://download.elastic.co/logstash/logstash/logstash-2.1.0.zip
       unzip -u logstash-2.1.0.zip
   ```
3. Jruby runs on JVM and needs a native library (libjffi-1.2.so: java foreign language interface). Get jffi source code and build with ant

	```
       wget https://github.com/jnr/jffi/archive/master.zip
       mv master master.zip (Only for SLES11)
       unzip  master.zip 
       cd jffi-master
       ant
	```
	libjffi-1.2.so is generated in folder: jffi-master/build/jni/libjffi-1.2.so
4. Copy libjffi-1.2.so to Logstash folder
    ```
        cd ../
       mkdir logstash-2.1.0/vendor/jruby/lib/jni/s390x-Linux
       cp jffi-master/build/jni/libjffi-1.2.so logstash-2.1.0/vendor/jruby/lib/jni/s390x-Linux/libjffi-1.2.so
    ```

5. Since version 2.1.0 the Logstash package introduces a ruby function to check user environment JRE version, it gives warning message if the JRE version is too low, however one of ruby method "captures" that needs JRE seems not work with IBM JRE 7 & 8. ( No problem found when using Open JRE 7 or later). As a workaround, in file:

    ```
       logstash-2.1.0/vendor/bundle/jruby/1.9/gems/logstash-core-2.1.0-java/lib/logstash/runner.rb
       comment out line 32, like:
       #    LogStash::Util::JavaVersion.warn_on_bad_java_version
    ```

6. Run Logstash
   ```
      cd logstash-2.1.0
      bin/logstash version
   ```
   Logstash should run on all supported OSes: SESL12/SESL11/RHEL6/RHEL7
## References:

* [logstash](https://www.elastic.co/products/logstash)
