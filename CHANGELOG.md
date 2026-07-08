# Changelog

## ocp-4.20-design-workshop-v2.html — 2026-07-08

### Correctness fixes

**vSphere / Platform section**
- Rewrote `install-config.yaml` sample to the 4.20 schema: `vcenters:` + `failureDomains:` with `topology:` paths, replacing the flat `platform.vsphere.*` fields deprecated since 4.13. Sample values now auto-fill live from the form fields (VIPs, vCenter, datacenter, cluster/datastore paths, network, folder, resourcePool).
- Fixed vCenter password guidance: the installer reads the password from `install-config.yaml` / the `vsphere-creds` secret — `GOVC_PASSWORD` belongs to the `govc` CLI only.
- Redrew the vSphere architecture diagram: 3 ESXi hosts with one control-plane VM per host (DRS anti-affinity), replacing the old layout that stacked all masters on one host.
- Added version requirement note: vSphere/vCenter 8.0 U1+ (7.x dropped in 4.20), VM hardware version 15 minimum.
- Added `disk.EnableUUID = TRUE` requirement (mandatory for vSphere CSI; manual on UPI).
- New conditional UPI block: guestinfo ignition properties, static-IP kargs, manual anti-affinity rule, sizing minimums. RHCOS OVA template field marked UPI-only.
- New fields: node IP allocation (DHCP/static), failure domain name, region/zone tag categories.
- Permissions table: added `Sessions.ValidateSession`, `StorageProfile.Update`, and an "abbreviated — copy full list from docs" warning.
- Design checklist: VMware Tools time-sync off (chrony owns clock), etcd datastore fsync <10 ms, DRS anti-affinity, minimum 3 ESXi hosts.

**Monitoring**
- Corrected long-term storage guidance: use `prometheusK8s.remoteWrite` to an external system (Thanos Receive, Mimir). The bundled Thanos sidecar serves in-cluster queries only and cannot be attached to object storage.

**Authentication**
- Fixed OAuth flow diagram label: namespace is `openshift-authentication` (pods are `oauth-openshift-*`).
- New OpenID Connect identity provider block: issuer URL, client ID, client-secret Secret, claims mapping (preferred_username/name/email/groups), CA ConfigMap, plus `oc edit oauth cluster` snippet.
- Added note on 4.20 GA direct external OIDC authentication (kube-apiserver validates IdP tokens natively; replaces the built-in OAuth server).

**Storage**
- New design checklist: multipathd via day-1 MachineConfig for iSCSI/FC/NVMe-oF, single default StorageClass, CSI capability checks (expansion, snapshots, cloning), RWX support per protocol.

**Certificates**
- Added notes: installer kubeconfig keeps working after API cert replacement (pinned internal CA); keep API and Ingress wildcard certificates separate.

### UX / readability

- Sticky section navigation bar: numbered chips with jump links and active-section highlight; HCP chip appears only when topology = Hosted Control Plane; Expand all / Collapse all buttons.
- Per-section completeness counters (`filled/total`) on section headers and nav chips — accent at ≥50 %, green at 100 %; fields in hidden subsections excluded.
- Autosave: any input persists to localStorage after ~1.2 s (previously only on page unload).
- Print: all details and collapsed sections auto-expand for printing and restore afterwards; sections avoid page breaks; nav hidden.
- Typography/contrast: field hints bumped to 12 px `#64748b` (WCAG AA); sub-headings consolidated into a `.ws-sub` class; `prefers-reduced-motion` respected.
- Removed CSS `scroll-behavior: smooth` — it silently cancelled anchor jumps in Chrome; navigation now jumps instantly with correct sticky-bar offset.
