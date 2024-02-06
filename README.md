# ipv4SubnetLab
![subnetting lab diagram](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/e398ae3c-4661-46be-973d-65b18399b948)

![ipv4 subnet directions](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/be925cf8-cb62-4c18-bb00-17e813150124)

So first we need to figure out how we're gonna split up the network. The current subnet is 192.168.1.0/24 meaning the subnet mask looks like this in binary 11111111.11111111.11111111.00000000
We know we need 4 resultant subnets, so we'll need 2^n=4 bits. In this case, n is 2, so we'll steal 2 bits from the host portion. Our new subnet mask will be /26 or 11111111.11111111.11111111.11000000
Now I'll quickly map out the network addresses for all 4 subnets, please note i'll be displaying the truye network portion as decimal and the stolen portion as well as host bits as binary, and then I'll convert them.
Subnet 1: 192.168.1.00000000 -> 192.168.1.0
Subnet 2: 192.168.1.01000000 -> 192.168.1.64
Subnet 3: 192.168.1.10000000 -> 192.168.1.128
Subnet 4: 192.168.1.11000000 -> 192.168.1.192

Based on the given directions, we know that R1 will use the last address in subnet 1, 192.168.1.62, for site 1 and the first address in subnet 2, 192.168.1.65, for the connection between site 1 and the modem.
R2 will use the last address in subnet 3, 192.168.1.190, for site 2 and the first address in subnet 4, 192.168.1.193, for the connection between site 2 and the modem.
All routers will use a subnet mask of 255.255.255.192. This is because we are adding 2 bits from the host portion to the inital class C mask in order to subdivide the subnet.
The internet router will also need to be configured. We'll use the last addresses of subnet 2 and subnet 4 to connect sites 1 and 2 respectively.
Now it's just a matter of using the routers' CLI to configure their IP addresses and subnet masks. I'll recreate a simple series of commands to do this for one router below:

en
conf t
int G0/0/0
no shut
ip address 192.168.1.62 255.255.255.192
end

After completing that process for each router we can run the command, sh ip int brief, to see the results:
![subnet cli](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/747993f4-fbc8-4742-b02c-267aaf0e3727)

Now that every router is configured, it's just a matter of testing the network, let's try to access facebook.com via one of the desktops in site 1.
![subnet practice proof](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/31510422-f723-4faf-b7c1-dbd2ae510438)
Success!

On to part 2 of the lab:

![subnetnewsite](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/0f5bb2a7-d513-4f95-b230-d623cde70d87)

![newsiteobjective](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/a14d54ff-e72a-4879-9dc5-30875477fe60)

So there's a new site and we have to configure the subnet a bit different. This time we need to subdivide the network 192.168.1.64/26 into as many subnets as possible that support 8 hosts. Anytime we're looking for hosts I know I can just count the powers of 2 and subtract 2 until I get as close as possible. 2^3=8 doesn't work because that would give us 6 hosts, so our next option is 2^4=16 which gives us 14 hosts per subnet. 
So we know we need 4 bits in the host portion. We currently have 32-26 = 6 bits in the host portion so we can lend 2 of those bits to the network portion to create a /28 subnet mask.

Subnet 1: 192.168.1.01 00 0000 -> 192.168.1.64
Subnet 2: 192.168.1.01 01 0000 -> 192.168.1.80
Subnet 3: 192.168.1.01 10 0000 -> 192.168.1.96
Subnet 4: 192.168.1.01 11 0000 -> 192.168.1.112

Let's configure the devices accordingly:

![sn2 site3](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/f89540b5-5b3b-427f-9e89-b2d698051e8c)

Now let's configure a /30 subnet for the serial links:

192.168.1.0111 00 00 -> 192.168.1.112
192.168.1.0111 01 00 -> 192.168.1.116
192.168.1.0111 10 00 -> 192.168.1.120
192.168.1.0111 11 00 -> 192.168.1.124

And let's configure the serial links:

![serial links](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/6b337711-593e-4d61-96b6-0097b7bcdcbb)

And finally let's verify network connectivity for all of the sites:

![connectivityverification](https://github.com/brendanreg/ipv4SubnetLab/assets/48809270/8319964d-de0f-459a-b074-f8cac05cd79a)

Boom, as you can see PCs from each site can connect to the internet successfully.
