Below is an **ASCII-based** diagram illustrating the 5G protocol stacks side-by-side for **User Plane** and **Control Plane**, showing how they align between the **UE** and **gNodeB**. This version aims to be clearer and more structured than a single combined diagram. 

---

## **User-Plane Protocol Stack**

```
          +---------------------------------------------------+
          |                   Applications                    |
          |                  (User / Data)                    |
          +---------------------------------------------------+
          |                   IP / Transport                  |
          +-------------------+--------------+----------------+
UE (UP)   |        SDAP      |              |    gNB (UP)    |
          +-------------------+              +----------------+
          |        PDCP      | <----------> |     PDCP       |
          +-------------------+              +----------------+
          |        RLC       | <----------> |     RLC        |
          +-------------------+              +----------------+
          |        MAC       | <----------> |     MAC        |
          +-------------------+              +----------------+
          |        PHY       | <----------> |     PHY        |
          +-------------------+--------------+----------------+
```

- **SDAP (Service Data Adaptation Protocol)**: Maps 5G QoS flows to Data Radio Bearers, enforcing QoS rules.  
- **PDCP (Packet Data Convergence Protocol)**: Handles ciphering, integrity protection (if configured), header compression, and data reordering.  
- **RLC (Radio Link Control)**: Provides segmentation/reassembly and ARQ (acknowledged or unacknowledged modes).  
- **MAC (Medium Access Control)**: Multiplexes/demultiplexes data between RLC and PHY, scheduling, and HARQ.  
- **PHY (Physical)**: Actual radio transmission and reception using OFDM, channel coding, MIMO, etc.

---

## **Control-Plane Protocol Stack**

```
          +---------------------------------------------------+
          |                       NAS                          |
          |    (Registration, Authentication, Security,       |
          |     PDU Session Management, Mobility, etc.)       |
          +---------------------------------------------------+
          |                       RRC                          |
          +-------------------+--------------+----------------+
UE (CP)   |        PDCP      |              |    gNB (CP)    |
          +-------------------+              +----------------+
          |        RLC       | <----------> |     RLC        |
          +-------------------+              +----------------+
          |        MAC       | <----------> |     MAC        |
          +-------------------+              +----------------+
          |        PHY       | <----------> |     PHY        |
          +-------------------+--------------+----------------+
```

- **NAS (Non-Access Stratum)**: Runs between UE and the 5G Core (AMF/SMF). Handles registration, authentication, and session management. Although shown in the diagram, NAS actually terminates in the core, not in the gNB.  
- **RRC (Radio Resource Control)**: Manages radio resources (connection establishment/release, configuration, mobility, etc.).  
- **PDCP/RLC/MAC/PHY**: The same layers described above but dedicated to carrying control messages instead of user data.

---

### **Key Points:**

1. **Separation of User Plane vs. Control Plane**  
   - User-plane stack carries end-user data (IP traffic).  
   - Control-plane stack carries signaling for mobility, connection management, and session control.

2. **NAS Termination**  
   - Although depicted in the control-plane diagram, NAS does **not** terminate in the gNB. It goes transparently through the RRC layer to the 5G Core (AMF/SMF).

3. **SDAP is New in 5G**  
   - It is responsible for applying QoS flows (QFI) at the radio level, bridging the 5G Core QoS model and the radio bearers.

4. **PDCP, RLC, MAC, PHY**  
   - Exist in both UE and gNodeB with similar names but opposite roles in sending/receiving data. They handle everything from security and segmentation to scheduling and physical signal transmission.

This layered approach ensures **flexibility**, **security**, and **end-to-end QoS** in 5G networks, adapting to different types of services (eMBB, URLLC, mMTC) with different requirements.
