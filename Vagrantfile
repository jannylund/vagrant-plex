Vagrant.configure(2) do |config|
    config.vm.box = "ubuntu/trusty64"
    config.vm.network "public_network"
    config.vm.network "forwarded_port", guest: 32400, host: 32400

    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
    end

    config.vm.synced_folder ".", "/vagrant", owner: "vagrant", group: "vagrant"

    # Generic setup
    config.vm.provision :shell, inline: <<-SCRIPT
        echo "=========================================="
        echo "Provision VM START"
        echo "=========================================="
        #apt-get update
        #apt-get -y -q upgrade

        # Fix environment settings.
        echo LC_ALL=en_US.UTF-8 | sudo tee -a /etc/environment
        echo LANG=en_US.UTF-8 | sudo tee -a /etc/environment

        echo "=========================================="
        echo "Installing basics"
        echo "=========================================="
        apt-get install -y git htop apt-show-versions zsh

        echo "=========================================="
        echo "Setting up oh-my-zsh"
        echo "=========================================="
        OHMYZSH="/home/vagrant/.oh-my-zsh";
        if [ -d "$OHMYZSH" ]; then
            cd $OHMYZSH
            git pull
            cd -
        else
            git clone git://github.com/robbyrussell/oh-my-zsh.git $OHMYZSH
        fi
        wget https://gist.githubusercontent.com/jannylund/1f2a8f6f67c74668c67f/raw/zshrc.zsh-template/ -O ~vagrant/.zshrc
        chown vagrant:vagrant -R /home/vagrant
        chsh -s /bin/zsh vagrant

        echo "=========================================="
        echo "Setting up plex"
        echo "=========================================="
        PLEXUPDATE="/vagrant/plexupdate";
        if [ -d "$PLEXUPDATE" ]; then
            cd $PLEXUPDATE
            git pull
            cd -
        else
            git clone https://github.com/mrworf/plexupdate.git $PLEXUPDATE
        fi

        # Download latest public plexserver.
        cd $PLEXUPDATE
        rm plexmediaserver_*.deb
        ./plexupdate.sh -p
        sudo dpkg -i plexmediaserver_*.deb

        echo "=========================================="
        echo "Setting up NFS"
        echo "=========================================="
        echo "10.0.1.100:/volume1/video /media nfs auto,rw 0 0" | sudo tee -a /etc/fstab
        sudo mount /media
    SCRIPT
end
