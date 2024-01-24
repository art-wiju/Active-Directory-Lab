# Active Directory Lab with Security Implementation.

<h2>Description</h2>
This project is a walkthrough of how I created an Active Directory home lab Environment using VMWare Virtualbox. I set up a Microsoft Server to run Active Directory on it. I then configure a Domain Controller that will allow me to run a domain. After that I executed a Powershell script to pull information from a list of names in a text note, create 1000 users in Active Directory and proceed to log into those newly created accounts on another client that uses the domain I set up to connect to the internet. This lab simulates a corporate environment. Lastly, I will configure a couple basic security measures such as password strenght and account lockout policies. In this lab I'll need a Microsoft Server 2019 ISO, A Windows 10 Enterprise ISO, VMWare and a Powershell script.

<h2>Languages and Utilities Used </h2>
Active Directory
PowerShell
CMD

<h2>Environments Used</h2>
VMWare VirtualBox
Microsoft Server 2019
Windows 10

<h2>Links</h2>
<p>VMWare: https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html
Microsoft Server 2019: https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019
Windows 10 ISO: https://www.microsoft.com/en-us/software-download/windows10</p>

<h2>Program walk-through</h2>

The network diagram I'll be using for this project

![1](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/8fbd7515-b65f-49b9-a955-8e77eb7e2fe1)


For the Virtual Machine that will be hosting my Domain Controller, I need two network adapters. I need a NAT (Network Address Translation) that will use my host IP address from my home router and an Internal Network Adapter so that my DC can communicate with other Virtual Machines. Refer to the diagram at the beginning. I renamed the adapters so it is easier for me to tell which is which and it is important later on when setting up the DC and DHCP. Now I configure the Internal network adapter and assign it an IP address based on the diagram above (172.16.0.1) and I do not need to give it a default gateway because the Domain Controller is the gateway. As for the DNS server I assign it an IP based on the diagram because when we install Active Directory it will install DNS. I set it as a loopback address (127.0.0.1) so it pings itself.

![2](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/58b65cba-45f8-495a-a351-2ecb26f70463)

After changing the name of the domain controller to something more simple (DC), I proceeded to install Active Directory. 

![3](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/ecaeacfe-bd00-4ed4-b7b7-b13e7e72c3da)


When the server is promoted to a domain, it forces a restart. When I log back in you can see that the domain was created successfully. 

![4](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/74c94a1b-5f39-47f3-ac5f-7e7752a9d35e)

Now instead of using the built in Admin account, I will create a dedicated domain Admin account, and of course, I am the administrator. I go in active directory to promote this account to Domain Administrator, and then log in with this account to continue working the lab.

![5](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/6c5336fb-09d0-428c-b1ca-1aec247dc34b)

Now I need to install and configure the RAS/NAT so that my Windows 10 client computer will be able to access the internet through the internal network via the Domain Controller

https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/10b12c25-11aa-4ab4-b504-1628aa1ef17f


After NAT is set up, I need to configure Routing and Remote Access

![7](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/2c50718d-0814-4650-a6d1-c6ecad4b0dd1)


Now that Remote Access is installed and configured, it is now time to Install a DHCP Server. This will allow our Windows 10 clients to be assigned an IP address and allow them to browse the internet.

![8](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/e392962e-cc5c-4803-98f9-d786b9f6522e)


Now to configure the DHCP and setup a scope. The whole purpose of DHCP is to allows computers on the network to automatically be assigned an IP address. The scope I will be creating will give assign IP addresses in a range, the range being 172.16.0.100-200 so the DHCP will effectively be able to give out 100 IP addresses. I also set the amount of time the IP addresses can be leased out to 8 days. The reason for the lease is when an IP address is assigned, it can't be used by other devices. So if I only have 100 IP addresses and 100 are used, new devices can't be assigned an IP address on the network meaning they can't connect to the internet. A lease is just an amount of time an IP address can be owned (leased) by a device before being recycled. If this was for example a Café that offers wifi and the average time a person spent inside said Café was 2 hours, it would make no sense to lease an IP address to them for 20 days. That would effectively lock up that IP address for that amount of time and no one else could use it. If this were a Café I would recommend setting the lease duration to under 4 hours and give a bigger range. Since this is only VM, the lease duration doesn't matter.

![9](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/92e8973d-37d3-4eb7-96f9-9e0820f7eb8c)


To get my powershell script from the internet I need to be able to browse the web. I have to disable the security features on the Domain Controller. If this was an actual production environment I would never do this, security risk. 

![10](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/1837df7c-2635-4941-b117-09f2986c23cd)

Now that Active Directory is configured and my Domain Controller is configured as well, I use a Powershell script to create over 1000 user accounts, which more realistically simulates a production environment.

![11](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/4dd3d252-138a-4dd7-8b6c-875067f2af1a)


The script has run successfully and the output confirmations that the user accounts has been created looks amazing. There were some duplicates that were not created, but that is easily solved by adding to the Powershell script a few lines of code that will tell it what to do in case duplicates occur. Perhaps something along the lines of "If a duplicate occurs, add a 1 to the end of the account name" for example. If you want to see the full code used, refer to the top of this repository. The script is under CREATE_USERS.ps1


It is now time to create a new Virtual Machine that will act as a user in the domain. I name this machine CLIENT1. I configure the network adapter so that it is not NAT and can't connect to the internet on my local network. The only way this Virtual Machine should be able to connect to the internet is by being assigned an IP from the DC on the Server VM. Refer to the Diagram at the beginning. I have to change the network adapter to be on the same internal network as the Domain Controller.

![12](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/d9412578-7f25-41bb-a2f7-8dd25dd061ee)


After configuring a separate virtual machine that will simulate an employee logging into the domain. I will also be renaming the computer CLIENT1 and clicking the box to become a member of the mydomain.com domain. I am prompted to give my log in credential and I chose to use the Administrator account I set up earlier.

![13](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/276d2ce6-898b-4278-937d-a20fe756b02c)

I go back into my server VM and check the DCHP to see how many addresses has been leased. We can see here that my CLIENT1 Virtual Machine has been leased an address. If this was a real company environment there would be hundreds, if not thousands of leased addresses in this folder depending on what the lease duration is of course! I set mine to 8 days in this environment.

![14](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/07707c5a-1da3-4fd9-8c41-d645b722e39d)

I access the internet from CLIENT1 to make sure that we indeed have access to internet that goes through our DC. Look at that beautiful github page. 

![15](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/90222b31-72d2-4597-9738-1653f7330aec)

After testing internet connectivity, I go back to my DC and open Group Policy Management. This is where I want to set up security policies that will protect the organization against some of the most common security issues, such as brute force attacks in weak, simple passwords. 

![16](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/a4d9eeff-27bd-4eff-90af-182e8f90fc56)

I set up a Maximum password age of 31 days (1 month), which means users have to get a new password every month, and they can't reuse any of the 24 last passwords. They also can't change passwords more than once a day, and are enforced to use a minimum lenght of 12 characters, which will also have a complexity requirement. I would also create an user awareness campaign with this to teach users best practices when creating passwords, such as not using anything that identifies them, such as name or employee ID, but this is a topic for another lab. 

![17](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/4fad5246-ef76-4656-a941-bc84f4b89b1e)

I also set up an account lockout policy to protect against brute force attacks. At this point, a password that is 12 characters in lenght with complexity requirements and only 5 attempts per lockout will leave little to no options to intruders to brute force this system. It would be take at least a couple centuries to crack the password. 

![18](https://github.com/art-wiju/Active-Directory-Lab/assets/132944565/bb2049f9-4db0-4fc3-ba94-5ecc147fa9e2)

Lastly, I proceed to push the update to Group Policy to all the computers in the domain, which in this case is just our CLIENT1 computer.

Securing your IT assets requires being proactive. I could continue securing the environment with the help of this Official Microsoft Guide https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-e--securing-enterprise-admins-groups-in-active-directory, but for the sake of demonstration I will finish here. 





