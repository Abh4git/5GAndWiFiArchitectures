Below is an overview of the 5G NR (New Radio) protocol stack as defined by 3GPP, showing both the **user plane** and **control plane** stacks across the **UE (User Equipment)** and the **gNodeB (gNB)**. We’ll discuss what each layer does and how these layers map between UE and gNB.

---

## High-Level Diagram

A simplified view of the protocol stacks on the User Equipment (UE) side and the gNodeB side can be represented as follows:

```
                  +-----------------------------------+
                  |           Application(s)          |
                  +-----------------------------------+
UE User Plane      |      IP / Transport (TCP/UDP)     |        gNB User Plane
                  +--------------------+---------------+
                  |     SDAP (5G QoS) |               |
                  +--------------------+               |
                  |        PDCP       |               |
                  +--------------------+    PDCP       |
                  |         RLC       | <---------->   |        RLC
                  +--------------------+               |
                  |         MAC       |               |        MAC
                  +--------------------+               |
                  |         PHY       |               |        PHY
                  +--------------------+---------------+

                  +-----------------------------------+
                  |             NAS                    |   (terminates in Core)
                  +-----------------------------------+
UE Control Plane   |             RRC                   |        gNB Control Plane
                  +--------------------+---------------+
                  |        PDCP       |               |
                  +--------------------+    PDCP       |
                  |         RLC       | <---------->   |        RLC
                  +--------------------+               |
                  |         MAC       |               |        MAC
                  +--------------------+               |
                  |         PHY       |               |        PHY
                  +--------------------+---------------+
```

---

## Layers and Their Responsibilities

### 1. **PHY (Physical) Layer**
- **Where**: Both UE and gNB.
- **What it does**:  
  - Handles the actual **radio transmission and reception** of signals over the air.  
  - In 5G NR, it supports advanced features such as **Massive MIMO**, **beamforming**, and **mmWave** frequencies.  
- **Key Functions**:
  - Modulation/demodulation of signals (e.g., OFDM).
  - Channel coding/decoding (e.g., LDPC for data, Polar codes for control).
  - Time/frequency synchronization.
  - HARQ (Hybrid ARQ) at the physical layer interface with MAC.

### 2. **MAC (Medium Access Control) Layer**
- **Where**: Both UE and gNB.
- **What it does**:
  - **Scheduling** of uplink (UL) and downlink (DL) data transmissions.  
  - Implements **HARQ** (in combination with PHY).  
  - **Multiplexes/demultiplexes** data from different logical channels (e.g., RLC channels) onto transport channels (PHY).  
- **Key Functions**:
  - Dynamic resource allocation.
  - Error correction via HARQ.
  - Priority handling (mapping of logical channels).

### 3. **RLC (Radio Link Control) Layer**
- **Where**: Both UE and gNB.
- **What it does**:  
  - Provides **ARQ** (Automatic Repeat reQuest) functionality for reliable data delivery (in acknowledged mode).  
  - **Segmentation** and **reassembly** of PDCP PDUs to fit MAC-layer transport constraints.  
  - Three modes:
    1. **Transparent Mode (TM)** – minimal overhead, mainly for broadcast info.  
    2. **Unacknowledged Mode (UM)** – no retransmission, used for real-time traffic where delays must be minimized.  
    3. **Acknowledged Mode (AM)** – uses ARQ for error-free delivery.  
- **Key Functions**:
  - Error correction via ARQ (AM mode).
  - In-sequence delivery and duplication detection (AM mode).
  - Segmentation and reassembly.

### 4. **PDCP (Packet Data Convergence Protocol) Layer**
- **Where**: Both UE and gNB.
- **What it does**:  
  - Handles **header compression** (ROHC) to reduce overhead over the air interface.  
  - **Ciphering** and **integrity protection** to secure user and control plane data.  
  - **In-sequence delivery** and **reordering** of packets (particularly when handovers occur).  
- **Key Functions**:
  - User-plane: Data encryption, integrity protection (as configured), header compression.  
  - Control-plane: RRC message security and integrity.  
  - Supports split/aggregation of data in case of multi-RAT or multi-carrier (e.g., Carrier Aggregation or Dual Connectivity).

### 5. **SDAP (Service Data Adaptation Protocol)**
- **Where**: *User Plane only*, both UE and gNB.
- **What it does**:  
  - **Maps 5G QoS flows** (defined in the 5G Core) to the appropriate data radio bearer on the air interface.  
  - Enforces QoS policies (priority, traffic handling) as defined by the 5G core network.  
- **Key Functions**:
  - Maintains QoS flow identifiers (QFI).
  - Ensures QoS consistency end-to-end.

### 6. **RRC (Radio Resource Control)**
- **Where**: UE and gNB (control plane).
- **What it does**:  
  - **Controls radio resources** (connection establishment, configuration, maintenance, and release).  
  - Broadcasts system information.  
  - Performs **mobility** functions (handover, cell reselection).  
- **Key Functions**:
  - Connection management (RRC Connected, RRC Idle, RRC Inactive states).
  - Measurement configuration and reporting.
  - Security mode control (key setup, algorithms selection).

### 7. **NAS (Non-Access Stratum)**
- **Where**: Runs between the UE and the 5G Core’s **AMF/SMF** (not actually terminated in the gNB).
- **What it does**:  
  - Responsible for **registration**, **authentication**, **security control**, **session management**, and **mobility management** at the core network level.  
  - In 5G, NAS messages are simply encapsulated in RRC messages for transport across the air interface.  
- **Key Functions**:
  - UE registration with the 5G core (AMF).
  - PDU session establishment (SMF).
  - Authentication and security procedures.

---

## Putting It All Together

1. **UE Side**  
   - The **application** traffic (e.g., web, video) hits the **IP layer** and is then directed through **SDAP** (for QoS flow mapping) before going to **PDCP**.  
   - **PDCP** performs ciphering, integrity protection, and possible header compression.  
   - **RLC** segments or reassembles the data for lower layers.  
   - **MAC** schedules data transmission opportunities (in uplink) and receives them in downlink.  
   - Finally, the **PHY** layer sends the data over the air interface as electromagnetic signals.  

2. **gNB Side**  
   - Receives those electromagnetic signals at the **PHY** layer.  
   - **MAC** reassembles data from transport channels and forwards it to the corresponding **RLC** instance.  
   - **PDCP** decrypts (or checks integrity), reorders packets as needed, and forwards them upward.  
   - **SDAP** ensures the data is associated with correct QoS flows toward the 5G core (via NG-U interface).  
   - On the control plane, **RRC** at the gNB communicates with RRC on the UE to set up and maintain the connection. **NAS** messages are forwarded between UE and the 5G core.  

Overall, each layer adds functionality to make the link more reliable, secure, and QoS-aware, ensuring end-to-end performance for both user and control traffic in a 5G system.

---

### Key Takeaways

- **SDAP** is new in 5G, tying together 5G Core QoS flows with the radio QoS.  
- **PDCP** is crucial for encryption, integrity, and header compression.  
- **RLC** and **MAC** provide error correction and multiplexing.  
- **RRC** and **NAS** handle control and signaling (including mobility, connections, and core network procedures).  
- Everything below RRC (PDCP/RLC/MAC/PHY) is considered the **Access Stratum (AS)**, while NAS is the **Non-Access Stratum (NAS)**.

This layered approach in 5G ensures flexibility, security, and robust Quality of Service for diverse applications ranging from mobile broadband to mission-critical IoT.
