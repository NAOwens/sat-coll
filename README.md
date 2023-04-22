sat-coll Project
=========

Create satellite collection and populate with hosts

Requirements
------------
addsathostcoll.yml - This playbook will create a temporary satellite host collection and add hosts to it.  This playbook should be run from Ansible Automation Platform because the hosts to be added to the host collection will be provided using answer type=Textarea in a survey.

addhostcoll_task.yml - This is a task that is called by addsathostcoll.yml

delsathostcoll.yml - This playbook will delete a satellite host collection.

sat-coll Variables
--------------
coll_name - This is the name of the temporary Satellite Host Collection that will be created. Can be a survey variable.  ie: survey_coll_name

org_name - This is the name of the Satellite Organization where the Host Collection will be created.  Can be a survey variable.

survey_add_hosts - survey variable used in addsathostcoll.yml to enter hosts that will be added to the host collection

Dependencies
------------
The project mainly uses ansible.builtin collections.

sat-coll Project Playbook
----------------
- name: Playbook to create host collection in Satellite from survey text
  hosts: sat6.shadowman.dev
  gather_facts: no

  vars:
    coll_name: "{{ survey_coll_name }}"
    org_name: "Shadow Man"

  tasks:
  - name: Create host collection "{{ coll_name }}" in Satellite
    ansible.builtin.shell:
      cmd: hammer --no-headers host-collection create --name "{{ coll_name }}" --organization "{{ org_name }}"
  
  - name: Call add hosts to collection task
    ansible.builtin.include_tasks: addhostcoll_task.yml
    loop: "{{ survey_add_hosts | split ('\n') }}"

License
-------

GPL

Author Information
------------------

Norman Owens - Norman.Owens@redhat.com