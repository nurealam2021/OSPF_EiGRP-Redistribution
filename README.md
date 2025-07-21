# OSPF & EIGRP Route Redistribution Lab

This lab demonstrates basic **route redistribution** between OSPF and EIGRP using Cisco routers, simulated in GNS3.

---

## Topology Overview
<img width="1047" height="337" alt="image" src="https://github.com/user-attachments/assets/0d859d2b-e040-4fa9-9ec4-97e542240956" />


---

##  Lab Setup Instructions

### Step 1: Open GNS#

- **R2** runs OSPF (Area 0)
- **R3** runs EIGRP (AS 10)
- **R1** is the redistribution router (OSPF ↔ EIGRP)
- **PC1** connected to R2, **PC2** connected to R3

### Step 2: Assign IP addresses to all connected interfaces according to the network topology.

| Device | Interface   | IP Address       |
|--------|-------------|------------------|
| R1     | Fa0/0       | 10.10.0.2        |
| R1     | Fa0/1       | 10.10.1.1        |
| R2     | Fa0/0       | 10.10.0.1        |
| R2     | Fa3/0       | 192.168.5.1      |
| R3     | Fa0/0       | 10.10.1.2        |
| R3     | Fa3/0       | 192.168.1.1      |
| PC1    | e0          | 192.168.5.2      |
| PC2    | e0          | 192.168.1.2      |


---

## Step 3: Assign IP to the pc 1

### PC1:
PC1> ip 192.168.5.2 255.255.255.0 192.168.5.1


### Step 4: OSPF Configuration
**R2** (config)#router ospf 1

**R2** (config-router)#network 192.168.5.0 0.0.0.255 area 0

**R2** (config-router)#network 10.10.0.0 0.0.0.255 area 0

**R1** (config)#router ospf 1

**R2** (config-router)#network 10.10.0.0 0.0.0.255 area 0

### Step 5: EiGRP configuration
**R1** (config)#router eigrp 10

**R2** (config-router)#network 10.10.1.0 0.0.0.255

**R3** (config)#router eigrp 10

**R3** (config-router)#network 10.10.1.0 0.0.0.255

**R3** (config-router)#network 192.168.1.0 0.0.0.255

## Perform Route Redistribution on R1 (OSPF ↔ EIGRP)

### Configure mutual redistribution between OSPF and EIGRP on Router R1, which connects both routing domains.
**R1** (config)#router eigrp 10

**R1** (config-router)# redistribute ospf 1 metric 10000 100 255 1 1500

**R1** (config-router)#exit

**R1** (config)#router ospf 1

**R1** (config-router)#network 10.10.1.0 0.0.0.255 area 0

**R1** (config-router)#redistribute eigrp 10 subnets



### Step 6: Final Configuration Check: R2 (OSPF Domain)
## R2 show ip route

<img width="776" height="275" alt="image" src="https://github.com/user-attachments/assets/1594f22d-310b-48b9-b01c-fd89b7ce3c14" />

## R3 show ip route

<img width="745" height="261" alt="image" src="https://github.com/user-attachments/assets/c1bcb021-6acc-47b3-ba1f-91c196b4e5ae" />

### Verification: PC1 Reaches PC2 (OSPF ↔ EIGRP)
Once redistribution is configured on R1, PC1 (in the OSPF domain) should be able to reach PC2 (in the EIGRP domain), proving that routing information is being shared properly between protocols.
<img width="1359" height="539" alt="image" src="https://github.com/user-attachments/assets/b2ca7479-db44-4e85-842a-63a6b23b989e" />

# This confirms that:
## End-to-End Connectivity Test
OSPF routes have been successfully redistributed into EIGRP.
EIGRP routes have been successfully redistributed into OSPF.
End-to-end connectivity between devices in different routing domains is established.
