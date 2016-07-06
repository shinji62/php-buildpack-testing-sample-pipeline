            ** Sample of unit-testing using the PHP-buildpack
            
            *** Description
            This use the image build with cflinuxfs2 and the php_buildpack.
            We do the staging phase in order to have a proper environment as 
            clause as possible from the real PCF staging phase.
            Including supporting .bp-config.

            We ensure that we test with the same stack (rootfs), library and binary that a normal deployment.



            *** Pipeline

            This pipeline is just a sample pipeline, but this show how to simulate the staging phase.
            The only things that the developper need to change is the "Testing part"


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





