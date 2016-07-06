##Sample of unit-testing using the PHP-buildpack##

***Description***

This use the image build with cflinuxfs2 and the php_buildpack. https://github.com/shinji62/cflinuxfs2-php-buildpack-image

We do the staging phase in order to have a proper environment as 
clause as possible from the real PCF staging phase.

Including supporting .bp-config.

 We ensure that we test with the same stack (rootfs), library and binary that a normal deployment.



 ***Pipeline***

 This pipeline is just a sample pipeline, but this show how to simulate the staging phase.
 The only things that the developper need to change is the "Testing part"
```yaml
---
jobs:
- name: unit-testing
  public: true
  serial: true
  plan:
  - get: app-source
    resource: my-php-application
  - task: unit-testing
    config:
      platform: linux
      image_resource:
        type: docker-image
        source:
         repository: getourneau/cflinuxfs2-php
         tag: 4.3.14
      inputs:
      - name: app-source
      run:
        path: bash
        args:
          - -exc
          - |
            #DO NOT TOUCH
            root=$PWD
            export CF_STACK=cflinuxfs2
            cp -Rr $root/app-source/. /home/vcap/app/
            export PATH=$PATH:/home/vcap/app/php/bin
            cd /stage && ../buildpack/bin/compile /home/vcap/app /tmp/cache
            export HOME=/home/vcap/app
            cd $HOME
            source $HOME/.profile.d/bp_env_vars.sh  
            sh $HOME/.profile.d/rewrite.sh
            

            #RUN OUR TEST
            #htdocs is where the public file are
            cd $HOME/htdocs
            #Run phpunit Inside the app directory
            phpunit

resources:
- name: my-php-application
  type: git
  source:
    branch: master
    private_key: {{private-key-github-concourse}}
    uri: git@github.com:shinji62/slim-unit-testing-example.git


```

 Staging part
 
 ```bash
 #DO NOT TOUCH
 root=$PWD
 export CF_STACK=cflinuxfs2
 cp -Rr $root/app-source/. /home/vcap/app/
 export PATH=$PATH:/home/vcap/app/php/bin
 cd /stage && ../buildpack/bin/compile /home/vcap/app /tmp/cache
 export HOME=/home/vcap/app
 cd $HOME
 source $HOME/.profile.d/bp_env_vars.sh  
 sh $HOME/.profile.d/rewrite.sh
 ```

Testing part

 ```bash
 #RUN OUR TEST
 #htdocs is where the public file are
 cd $HOME/htdocs
 #Run phpunit Inside the app directory
 phpunit
 ```





