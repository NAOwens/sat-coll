sat-coll Project
=========

Create temporary satellite collection in Satellite, populate with hosts and sync Satellite Inventory in AAP.
Delete temporary satellite collection in Satellite and sync Satellite Inventory in AAP.

Requirements
------------
addsathostcoll.yml - This playbook will create a temporary satellite host collection and add hosts to it.  This playbook should be run from Ansible Automation Platform because the hosts to be added to the host collection will be provided using answer type=Textarea in a survey.  This playbook essentially uses the survey as a sort of flat file.  This playbook can be used to create a temporary inventory that another playbook can use in a workflow.  By using satellite, the playbook can verify that the user entered valid hosts and by syncing the satellite inventory in AAP, another playbook in a workflow can use this temporary inventory.  

addhostcoll_task.yml - This is a task that is called by addsathostcoll.yml.  This task performs the checks to verify that the hosts added in the survey exist in satellite and it makes sure that the user did not duplicate hosts.  It also addes the hosts to the newly created temporary satellite host collection.

delsathostcoll.yml - This playbook will delete a satellite host collection and then sync the satellite inventory in AAP.

sat-coll Variables
--------------
coll_name - This is the name of the temporary Satellite Host Collection that will be created. Can be a survey variable.  ie: survey_coll_name

org_name - This is the name of the Satellite Organization where the Host Collection will be created.  Can be a survey variable.

survey_add_hosts - survey variable used in addsathostcoll.yml to enter hosts that will be added to the host collection

inventory_source_name - Satellite inventory source that will be sync'd

inventory_name - name of Satellite inventory

Dependencies
------------
The project uses collections:
  - ansible.builtin
  - ansible.controller 

Both playbooks require a machine credential created in AAP that has sudo privilege on the Satellite server.  

Both playbooks require an Ansible Automation Controller credential in order for ansible.controller.inventory_source_update

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