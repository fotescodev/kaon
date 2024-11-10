# Slashing Incidence Process

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

1. **Incident Detection and Reporting:**
   * **Block Not Accepted:** When a block proposed by a validator is not accepted, it triggers a report node mechanism.
     * **Validator Reporting:** Validators report the invalid block (inv) or invalid transactions (inv) associated with the block.
     * **Consensus Mechanism:** The consensus mechanism processes the report and, if validated, the incident is escalated.
2. **Increasing Distrust Level:**
   * **Consensus Decision:** The consensus mechanism evaluates the report. If the block or transaction is rejected, the distrust level of the involved validator is increased.
   * **Malicious Behavior Reporting:** Validators can report any malicious actions observed from another validator.
   * **Uptime Monitoring:** Validators continuously monitor each other’s uptime. Lack of uptime or unavailability is also reported.
3. **Validator Response and Exclusion:**
   * **Ping Mechanism:** Validators and master nodes periodically ping each other to check for responsiveness.
     * **No Reaction:** If a validator does not respond or is unavailable, this is reported.
     * **Exclusion Decision:** Based on the reports, the consensus mechanism may decide to exclude the unresponsive validator from the current round.
4. **Reaching Distrust Maximum:**
   * **Distrust Level Monitoring:** The distrust level of each validator is continuously monitored.
   * **Slashing Decision:** If a validator’s distrust level reaches the maximum threshold, a slashing decision is made through the consensus mechanism.
5. **Executing Slashing:**
   * **Consensus Approval:** The consensus mechanism must approve the slashing decision. This involves validating the accumulated reports and ensuring that due process has been followed.
   * **Validator Slashing:** Once approved, the validator is slashed. This may involve:
     * **Token Penalties:** The validator’s staked tokens are partially or fully burnt.
     * **Exclusion:** The validator is excluded from the network. Every member of the chain won’t accept any information from the slashed validator. Validator’s address won’t be accepted anymore in a transaction validation process.
