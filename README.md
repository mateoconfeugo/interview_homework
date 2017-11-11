Introduction:
=============

This is a homework assignment for an interview candidate to
complete as part of initial screening. This is a full vagrant
workspace that will execute the ansible provisioner.  To setup
you environment to run this first execute in this directory:

	. setup_env

Then execute:

	vagrant up

You should get a message:

	'complete me'

If you prefer another config management tool such as puppet
or chef, that is up to the interviewee to setup the provisioner
portion in the Vagrantfile and either provide documentation on
environment setup or automate that process as well.

Key Objectives:
===============

* Install lighttpd
* Configure a lighttpd named vhost via a jinja2 template
* S3 artifact deployment into virtual host document root
  /srv/bogusapp/
* Full automation ("vagrant up" to working site with no
  manual intervention)
* Idempotency

Artifact deployment:
====================

The following access and secret keys have read only access
to the 'bogus-app-homework' bucket in the us-west-2 region:

Access key: AKIAJH2C7CAWKL7BLTBA
Secret key: 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

The artifact to deploy is 'homework.tar.gz'.

Bonus:
======

* Secure the s3 access and secret key, even if it means a
  password is supplied at "vagrant up".  For example if using
  ansible-vault it is acceptable for the user to be required
  to type in a vault password on 'vagrant up'.
* Present some output at the end of the vagrant run with
  local host setup to test the named virtual host.
* Be mindful of how roles or any config files are organized.
