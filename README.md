Webstack10
==========

* webstack10 is for the Dokuwiki stack


Install
-------

1. Configure httpd server

	`ansible-playbook -i inventory/hosts --become --ask-become-pass --tags httpd_configure webstack10_containers_manager.yml`

2. Dokuwiki base configuration (http(s)://<baseurl>/install.php)

   * Wiki Name: flem
   * Enable ACL: yes
   * Superuser: XXXXXXXX
   * Real name: XXXXXXXX
   * E-Mail: XXXXXXXX
   * Password:
   * Initial ACL policy: Public Wiki
   * Allow users to register themselves: NO
   * Licence: CC Attribution-Share Alike 3.0 Unported
   * Send anonymous usage: NO

3. Rename the install.php file (post configuration)

	`ansible-playbook -i inventory/hosts --become --ask-become-pass --tags doku_install_php webstack10_containers_manager.yml`

4. Dokuwiki configuration (logged as admin)

   * Configuration Settings
     * baseurl: https://www.flem.fr/
     * securecookie: coché (car https)
     * mailfrom: "flem dot fr wiki admin" <franck.lemoine@flem.fr>
   * Manage Plugins
     * Compatibility problems between Dokuwiki plugins and Php7
       * ```sed -i 's/&$handler/Doku_Handler $handler/g' syntax.php```
       * ```sed -i 's/&$renderer/Doku_Renderer $renderer/g' syntax.php```
       * ```sed -i 's/, $handler/, Doku_Handler $handler/g' syntax.php```
       * ```sed -i 's/, $renderer/, Doku_Renderer $renderer/g' syntax.php```
       * ```sed -i 's/&$controller/Doku_Event_Handler $controller/g' action.php```
       * ```sed -i 's/&$R/Doku_Renderer $R/g' syntax.php```
   * User Manager

5. Dokuwiki datas

   * Export datas
	`ansible-playbook -i inventory/hosts --become --ask-become-pass --tags doku_export webstack10_containers_manager.yml`

   * Import datas
	`ansible-playbook -i inventory/hosts --become --ask-become-pass --tags doku_import_datas webstack10_containers_manager.yml`

6. 'Directory List' datas

   * Export datas
	`ansible-playbook -i inventory/hosts --become --ask-become-pass --tags dlst_export webstack10_containers_manager.yml`

   * Import datas
	`ansible-playbook -i inventory/hosts --become --ask-become-pass --tags dlst_import webstack10_containers_manager.yml`



Divers
------

* Install a Debian package
	`ansible -i inventory/hosts webstack10 -u franck --become --ask-become-pass -m apt -a "name=python-pip update_cache=yes state=present"`

* And to avoid error (after installing 'docker pip' ... pip install docker-pip)
   * 'pip list' command :
     * return => ImportError: cannot import name IncompleteRead
     * solution :
       * `pip --upgrade --user pip`
       * `pip --user docker-py`
   * `ansible -i inventory/hosts webstack10 -u franck -m pip -a "name=pip extra_args='--upgrade --user'"`
   * `ansible -i inventory/hosts webstack10 -u franck -m pip -a "name=docker-py extra_args='--user'"`

* Remove a Docker image
   * `ansible -i inventory/hosts webstack10 -u franck -m docker_image -a "name=flem/php7-fpm state=absent"`

* Docker containers state
   * `ansible -i inventory/hosts webstack10 -m command -a "docker ps -a"`
