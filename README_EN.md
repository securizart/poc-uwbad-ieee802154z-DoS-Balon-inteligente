## PoC UWBAD: Reactive Interference and Physical-Layer Resilience in IEEE 802.15.4z RTLS

This repository contains a technical Proof of Concept (PoC) and controlled laboratory simulation environments designed to evaluate potential UWB Activation Delay (UWBAD) vulnerabilities affecting IEEE 802.15.4z Real-Time Location Systems (RTLS).

The research focuses on high-availability RTLS environments, using professional sports smart-ball infrastructure as a theoretical case study.

The project is intended exclusively for defensive security research, controlled laboratory testing, and the development of mitigation strategies.

### Analysis Scenario (Case Study)

The target ecosystem considered in this research is a closed-loop infrastructure representative of professional sports smart-ball systems.

- **Infrastructure:** Multiple fixed, time-synchronized receiving antennas.
- **Mobile Tag:** A sensor embedded in the ball transmitting UWB signals within applicable regulatory constraints.
- **Potentially Exposed Component:** Although modern systems may employ cryptographic mechanisms such as STS to protect ranging operations, receivers must still perform physical-layer synchronization and signal acquisition. This research investigates the resilience of those mechanisms against abnormal or adversarial RF conditions.

### Hardware and Laboratory Materials

For controlled implementation of this PoC, researchers should use standard UWB development equipment and RF laboratory instrumentation appropriate for IEEE 802.15.4z research.

Implementation-specific offensive hardware configurations are intentionally omitted from this public repository.

Detailed research materials may be shared with verified academic, industrial, or security collaborators when appropriate.

### Safe Laboratory Configuration (Closed Loop)

Experiments involving interference must be performed exclusively in an isolated and authorized RF environment.

Suitable environments may include:

- Conducted RF test setups.
- Shielded coaxial connections.
- Passive RF attenuation.
- Certified RF shielding facilities.
- Anechoic chambers or Faraday enclosures where appropriate.

The purpose of the closed-loop environment is to prevent experimental signals from affecting external or operational radio systems.

### Defensive Firmware Logic (Physical Layer)

One potential mitigation strategy is to analyze Channel Impulse Response (CIR) characteristics over time.

Receivers may correlate physical-layer measurements with spatial and temporal behavior to identify persistent anomalous sources.

Conceptually:

```c
// Conceptual defensive CIR profile analysis

void evaluate_uwb_frame(frame_t *frame, node_id_t node_id)
{
    cir_profile_t current_profile = capture_cir_profile(frame);

    if (profile_is_anomalous(current_profile, node_id)) {

        increase_anomaly_score(node_id);

        if (anomaly_score_exceeds_threshold(node_id)) {
            discard_frame(frame);
            notify_security_monitor(node_id);
            return;
        }
    }

    process_valid_rtls_frame(frame);
}
```

This code is intentionally conceptual. Device-specific thresholds, timing parameters, RF configurations, and implementation details are excluded from the public version.

### Recommended Countermeasures

- **Transmission-Time Randomization:** Introduce cryptographically derived timing variability where permitted by the protocol and system architecture. Predictability reduction may improve resilience against interference correlated with predictable transmission events.
- **CIR Anomaly Detection:** Monitor Channel Impulse Response characteristics and identify persistent or unusual physical-layer signatures.
- **Receiver Diversity:** Compare observations from multiple spatially separated receivers to distinguish legitimate mobile transmitters from anomalous RF sources.
- **TDOA-Based Anomaly Localization:** Where technically and legally appropriate, observations from multiple synchronized receivers may be used to estimate the approximate origin of persistent anomalous signals.
- **RF Spectrum Monitoring:** Combine protocol-level telemetry with independent spectrum monitoring to identify abnormal changes in RF activity.
- **Rate and Traffic Validation:** Validate identifiers, timing behavior, traffic patterns, and other protocol characteristics before committing significant server-side processing resources.
- **Defense in Depth:** Physical-layer anomaly detection should complement cryptographic protection, network monitoring, access controls, and operational security procedures rather than replace them.

### Responsible Research Principles

All experiments associated with this repository must follow the principles below:

1. Testing must be conducted only on systems owned by the researcher or systems for which explicit authorization has been obtained.
2. RF experiments must remain confined to controlled laboratory environments.
3. Experimental signals must not interfere with public, private, commercial, or safety-critical communications.
4. Operational vulnerabilities discovered during research should be handled through responsible disclosure processes.
5. Offensive implementation details that could unnecessarily facilitate real-world disruption should not be published without appropriate safeguards.

### Disclaimer and Legal Notice

#### 1. Technological Improvement and Theoretical-Experimental Purpose

This project is formally established as a Proof of Concept (PoC) whose sole purpose is defensive auditing and the early identification of potential risk vectors.

The research is exclusively intended for proactive analysis of possible deficiencies and weaknesses that could affect high-availability environments under theoretical models and controlled simulations.

The ultimate objective of this repository is to support the development of countermeasures, firmware improvements, monitoring mechanisms, and advanced network strategies capable of mitigating such vulnerabilities before they can be exploited, thereby improving the resilience and robustness of commercial telecommunications systems.

#### 2. Exclusively Academic and Educational Purpose

This repository, including its source code, conceptual firmware, risk matrices, mathematical models, and accompanying technical documentation, has been developed exclusively for academic research, defensive system auditing, and scientific dissemination in the fields of cybersecurity and telecommunications engineering.

Its objective is to identify potential structural weaknesses in the physical layer (PHY) of wireless communication systems and to investigate effective mitigation and resilience mechanisms for technological infrastructures.

#### 3. Prohibition of Use in Real-World or Unauthorized Environments

The concepts, algorithms, code, experimental configurations, and documentation contained in this project must not be used to perform interference, denial-of-service attacks, unauthorized electromagnetic disruption, or any other attack against operational public or private communication networks, official sporting competitions, or third-party commercial systems.

Testing against infrastructure not owned by the researcher requires explicit, written, and legally valid authorization from the legitimate owner or operator of that infrastructure.

#### 4. Limitation of Liability

The author or authors of this repository accept no responsibility for any direct, indirect, incidental, special, or consequential damage resulting from the use, misuse, interpretation, or practical application of the technical information described herein.

This includes, but is not limited to, damage to laboratory equipment, regulatory penalties resulting from unauthorized use of the radio spectrum, disruption of third-party communication systems, or legal consequences resulting from unauthorized access to or interference with computer or telecommunications networks.

#### 5. Regulatory Compliance (Telecommunications and Cybersecurity)

Any practical reproduction of this Proof of Concept must be conducted in an appropriately controlled and isolated environment and in compliance with applicable telecommunications and cybersecurity regulations.

Researchers are responsible for ensuring that experimental RF signals do not escape the authorized test environment or interfere with operational radio services.

Ultra-Wideband transmissions must comply with the applicable power, spectral-density, frequency-allocation, and equipment requirements established by the competent telecommunications authorities.

Downloading, cloning, or using files from this repository implies acceptance of the terms and restrictions described in this legal notice.

Additional PoC implementation details may be made available to verified researchers, organizations, or collaborators when appropriate and subject to suitable access controls.
