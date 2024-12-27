
###  RSTP and Spanning Tree Configuration

#### Overview
This project demonstrates the configuration of **Rapid Spanning Tree Protocol (RSTP)** in a network topology to ensure loop-free redundancy, enhance network stability, and enforce security measures such as BPDU Guard and Root Guard.

---

### Steps to Configure

#### 1. Enable RSTP
RSTP (Rapid Spanning Tree Protocol) is activated on all switches to ensure faster convergence:
```plaintext
Switch(config)#spanning-tree mode rapid-pvst
```

#### 2. Configure Access Ports with PortFast and BPDU Guard
PortFast and BPDU Guard are configured on access ports to optimize performance and prevent accidental loops:
```plaintext
Switch(config)#interface FastEthernet 0/1
Switch(config-if)#spanning-tree portfast
Switch(config-if)#spanning-tree bpduguard enable
```
> **Note**: These commands are applied to ports connecting end devices (e.g., PCs).

---

#### 3. Configure Trunk Ports
Enable trunking for inter-switch communication:
```plaintext
Switch(config)#interface FastEthernet 0/24
Switch(config-if)#switchport mode trunk
```

---

#### 4. Set Root Bridge
Assign **Switch1** as the Root Bridge for VLAN 1 (default VLAN):
```plaintext
Switch(config)#spanning-tree vlan 1 root primary
```

---

#### 5. Enable Root Guard
Activate Root Guard on upstream ports of **Switch0** and **Switch1** (facing access switches) to prevent them from becoming the root:
```plaintext
Switch(config)#interface range FastEthernet 0/23-24
Switch(config-if-range)#spanning-tree guard root
```

---

### Testing Procedures

#### Test 1: BPDU Guard
1. Add a new switch to the topology and connect it to **Switch3**, port FastEthernet 0/1 (replacing PC A).
2. Observe that the link becomes blocked.
3. Verify the status of the port using:
   ```plaintext
   Switch#show interfaces FastEthernet 0/1
   ```
   > **Expected Output**: "err-disabled" status indicates BPDU Guard activation.

4. Restore the original setup by reconnecting PC A to **Switch3**. The port remains inactive.

5. Reactivate the port using the following commands:
   ```plaintext
   Switch(config-if)#shutdown
   Switch(config-if)#no shutdown
   ```

#### Test 2: Root Guard
1. Attempt to make **Switch3** the Root Bridge by checking the current Root Bridge priority:
   ```plaintext
   Switch#show spanning-tree detail
   ```
   > **Expected Output**: The current Root Bridge priority is **24577**.

2. Configure a lower priority on **Switch3**:
   ```plaintext
   Switch(config)#spanning-tree vlan 1 priority 20480
   ```

3. Observe the following message on **Switch1**:
   ```plaintext
   %SPANTREE-2-ROOTGUARDBLOCK: Port 0/23 tried to become non-designated in VLAN 1.
   ```

4. Confirm that **Switch1** remains the Root Bridge and **FastEthernet 0/23** is in the **BLK (Blocked)** state:
   ```plaintext
   Switch#show spanning-tree active
   ```

---

### Notes
- **Root Guard** ensures unauthorized switches cannot take over as Root Bridge.
- **BPDU Guard** protects edge ports from malicious or accidental BPDU frames.
- Always verify configurations with `show spanning-tree` commands to confirm the active topology and port states.

--- 

Let me know if you'd like to expand or adjust the content!
