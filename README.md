# Flugel

This repo creates a kubernetes cluster with 2 VM's, With 1 master and 1 slave nodes, provisioned with ansible_local.

 - Start the cluster
	>vagrant up
 - Connect to the master vm
	> vagrant ssh m-1
  - Get all pods
	> python ~/tools/listPods.py  

