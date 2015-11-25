### Building Fluentd

The following build instructions have been tested with Fluentd-0.12.9 on RHEL7 and SLES12 on IBM z Systems.

### Version
0.12.9
### Section 1: Install the following Dependencies
*	git (or git-core)
*	ruby
*	gcc
*	gcc-c++
*	kernel-devel
*	ruby-devel
*	openssl
*	make 
##### Additional dependencies required for SLES12
*	timezone

RHEL7:
```
yum install -y git \
	ruby \
	gcc \
	gcc-c++ \
	kernel-devel \
	ruby-devel \
	openssl \
	make 
```

SLES12:
```
zypper install -y git-core \
	make \
	ruby \
	gcc \
	gcc-c++ \
	kernel-devel \
	ruby-devel \
	timezone
```
### Section 2: Build and Install Fluentd
1. (For Rhel) Re-install the ca certificates for openssl framework.
        yum reinstall -y ca-certificates
2. Install the bundler using gem
        gem install bundler
    Can specify the version of the bundler (Optional)

    For instance,
        gem install bundler -v="1.9.4"
3. Get the version of the installed bundler.
        gem list | grep bundler

4. Set the Bundler Path as per the installed version
        Rhel7:
        export PATH=$PATH:/usr/local/share/gems/gems/bundler-<version-id>/bin
        Sles12:
        export PATH=$PATH:/usr/lib64/ruby/gems/2.1.0/gems/bundler-<version-id>/bin
5. Create a fluentd user
        useradd -d /home/fluentd -m -s /bin/bash fluentd -p fluentd
6. Get the fluentd source and clone as fluentd user
		su fluentd
        cd /home/fluentd
        git clone --branch v0.12.9 https://github.com/fluent/fluentd
        cd /home/fluentd/fluentd
		
7. Install bundle
        bundle install --path=./vendor/bundle
        bundle exec rake
		
8. Install fluentd as root user
	   su root
	(If inside the docker container, type "exit" to switch from fluentd user to root user)
        cd /home/fluentd/fluentd/pkg
	    ls | xargs -n1 gem install

	Get the version of installed fluentd
	   gem list | grep fluentd

    Set fluentd environment path
		Rhel7:
        export PATH=$PATH:/usr/local/share/gems/gems/fluentd-<version-id>/bin
        Sles12:
		export PATH=$PATH:/usr/lib64/ruby/gems/2.1.0/gems/fluentd-<version-id>/bin

    Setup Fluentd
	    fluentd --setup ./fluent 

9. By default, fluentd configuration file is created at ./fluent/fluent.conf

### References:
https://github.com/fluent/fluentd
