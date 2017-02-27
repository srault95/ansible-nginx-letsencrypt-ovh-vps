# Déploiement Ansible Nginx/LetsEncrypt sur VPS OVH

Ce playbook a été testé avec succès sur des VPS OVH [SSD1](https://www.ovh.com/fr/vps/vps-ssd.xml)

## Validé avec:
- Ubuntu 16.04 64bits
- Une clé SSH était configuré et déployé par OVH sur tous les serveurs.
- Ansible a été exécuté à partir de l'image docker [williamyeh/ansible:alpine3](https://hub.docker.com/r/williamyeh/ansible)

## Installation

	$ git clone https://github.com/srault95/ansible-nginx-letsencrypt-ovh-vps /opt/web-servers

## Configuration

- Remplacez l'adresse IP du VPS dans /etc/hosts, /root/.ssh/config et host_vars/vps1.yml

- Remplacez l'email **letsencrypt_email** 

- Ajouter autant de host_vars/*.yml que vous avez de serveurs VPS à déployer

> Attention, sur les VPS, la première interface réseau est **ens3** et l'utilisateur est **ubuntu**

	#/opt/web-servers/group_vars/all.yml
	letsencrypt_email: admin@domain.net
	
	#/opt/web-servers/host_vars/vps1.yml
	timezone: Europe/Paris
	failover_address:  1.1.1.1
	failover_name: ens3:0
	domains:
	  - domain.net
	  - www.domain.net 
	hostname: vps1	

	#/etc/ansible.cfg (Les valeurs par défaut)
	
	#/etc/hosts
	[webservers]
	vps1
	
	#~/.ssh/id-rsa (clé privée ssh)
	
	#/root/.ssh/config	
	Host *
	   ConnectTimeout 120
	   Compression yes
	   CompressionLevel 6
	   LogLevel FATAL
	   IdentityFile ~/.ssh/id-rsa
	   User ubuntu
	   PasswordAuthentication no
	   StrictHostKeyChecking yes
	
	Host vps1
	   Hostname 1.1.1.1
	   
## Exécution

	$ cd /opt/web-servers/
	
	$ docker run -it --rm -w /tmp -v /etc/ansible:/etc/ansible \
		-v $PWD:/tmp -v /root:/root williamyeh/ansible:alpine3 \
		/tmp/site.yml -l 'vps1'

