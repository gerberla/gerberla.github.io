Here’s a concise, step-by-step guide to deliver your demo page (`index.html`) and its subdirectories on IPFS, using both IPNS and DNSLink to provide stable, human-readable access. This assumes your `demos` folder is structured as previously outlined, with relative paths intact (e.g., `Prototypes/Base/original.html`). I’ll summarize the process based on your current setup.

### Directory Structure Recap
```
demos/
├── index.html
├── Prototypes/
│   ├── Base/
│   │   ├── original.html
│   │   └── optimized.html
│   ├── Features/
│   │   ├── glb-upload.html
│   │   ├── realtime-slider.html
│   │   └── interpolated-glb.html
│   └── Visuals/
│       ├── heatmap.html
│       └── thickness-slider.html
├── fft/
│   └── grok_fft_6.html
```

### Prerequisites
- **IPFS Installed**: Download and install IPFS CLI or Desktop from [ipfs.io](https://ipfs.io/#install).
- **Domain (for DNSLink)**: Optional, but you’ll need one you control (e.g., `yourdemos.com`) with DNS access.
- **Terminal**: For CLI commands.

---

### Option 1: IPFS with IPNS
IPNS provides a mutable name that points to your latest IPFS CID, keeping the access URL stable even when content updates.

#### Step-by-Step Instructions
1. **Initialize IPFS (if not done)**:
   - Open a terminal and run:
     ```
     ipfs init
     ```
   - This creates a local IPFS node with a key pair in `~/.ipfs`.

2. **Start IPFS Daemon**:
   - Run in a terminal (keep it running):
     ```
     ipfs daemon
     ```
   - This connects your node to the IPFS network.

3. **Add Your Demos Folder to IPFS**:
   - Navigate to the `demos` folder:
     ```
     cd path/to/demos
     ```
   - Add the folder recursively:
     ```
     ipfs add -r .
     ```
   - Output will list CIDs for each file and end with the root CID, e.g., `added QmDemoCID demos`. Note this CID (e.g., `QmDemoCID`).

4. **Test Locally**:
   - Open in browser: `http://localhost:8080/ipfs/QmDemoCID/index.html`
   - Verify all demos load correctly in the iframe.

5. **Create an IPNS Key**:
   - Generate a named key for your demos:
     ```
     ipfs key gen --type=rsa --size=2048 demos-key
     ```
   - Output: a key hash, e.g., `QmYourIPNSKey`. Note this (e.g., `QmYourIPNSKey`).

6. **Publish to IPNS**:
   - Link your CID to the IPNS key:
     ```
     ipfs name publish --key=demos-key QmDemoCID
     ```
   - Output: e.g., `Published to QmYourIPNSKey: /ipfs/QmDemoCID`.

7. **Access via IPNS**:
   - Local: `http://localhost:8080/ipns/QmYourIPNSKey/index.html`
   - Public Gateway: `https://ipfs.io/ipns/QmYourIPNSKey/index.html`
   - Test in a browser to ensure it loads.

8. **Pin for Persistence**:
   - Locally: Already pinned by `ipfs add`. To ensure availability, use a pinning service:
     - **Pinata**: Sign up at [pinata.cloud](https://pinata.cloud), upload `demos` folder, get CID, and pin it.
     - **Filecoin**: Use a service like [web3.storage](https://web3.storage) to store and pin.

9. **Update Content (Later)**:
   - Modify a demo file (e.g., `heatmap.html`), then:
     ```
     ipfs add -r .
     ```
   - Get new CID (e.g., `QmNewCID`), update IPNS:
     ```
     ipfs name publish --key=demos-key QmNewCID
     ```
   - Same IPNS URL (`/ipns/QmYourIPNSKey/index.html`) now points to the updated content.

#### Final URL
- Share: `ipfs://QmYourIPNSKey/index.html` (native IPFS) or `https://ipfs.io/ipns/QmYourIPNSKey/index.html` (gateway).

---

### Option 2: IPFS with DNSLink
DNSLink maps a domain you own to an IPFS CID or IPNS key via a DNS TXT record, offering a custom, readable URL like `https://yourdemos.com`.

#### Step-by-Step Instructions
1. **Follow Steps 1-4 from IPNS**:
   - Initialize IPFS, start daemon, add `demos` folder, and get the CID (e.g., `QmDemoCID`).
   - Test: `http://localhost:8080/ipfs/QmDemoCID/index.html`.

2. **Own a Domain**:
   - Purchase a domain (e.g., `yourdemos.com`) via a registrar like Namecheap, GoDaddy, etc., if you don’t already have one.

3. **Set Up DNSLink with CID** (Static Option):
   - Access your domain’s DNS settings in your registrar’s dashboard.
   - Add a TXT record:
     - Name: `_dnslink.yourdemos.com` (or `_dnslink` under `yourdemos.com` depending on registrar).
     - Value: `dnslink=/ipfs/QmDemoCID`
     - TTL: Default or 300 (5 minutes).
   - Save and wait for DNS propagation (minutes to hours).
   - Test: `https://ipfs.io/dnslink/yourdemos.com/index.html` or use a DNSLink-supporting gateway like `cloudflare-ipfs.com`.

4. **Set Up DNSLink with IPNS** (Dynamic Option, Recommended):
   - Follow IPNS Steps 5-6 to create (`demos-key`) and publish (`QmYourIPNSKey`).
   - Add a TXT record:
     - Name: `_dnslink.yourdemos.com`
     - Value: `dnslink=/ipns/QmYourIPNSKey`
     - TTL: 300.
   - Save and wait for propagation.
   - Test: `https://ipfs.io/dnslink/yourdemos.com/index.html`.

5. **Access via Domain**:
   - Gateway: `https://yourdemos.com` (via Cloudflare’s `cloudflare-ipfs.com` or similar).
   - Native (if supported): `ipfs://yourdemos.com/index.html`.

6. **Pin for Persistence**:
   - Same as IPNS Step 8: Use local pinning or a service like Pinata.

7. **Update Content**:
   - With CID: Re-upload `demos`, get new CID (e.g., `QmNewCID`), update TXT record to `dnslink=/ipfs/QmNewCID`.
   - With IPNS: Re-upload, get new CID, update IPNS (`ipfs name publish --key=demos-key QmNewCID`), no DNS change needed.

#### Final URL
- Share: `https://yourdemos.com` (gateway-dependent) or `ipfs://yourdemos.com/index.html`.

---

### Summary
- **IPFS Alone**: Upload `demos`, get CID (`QmDemoCID`), share `ipfs://QmDemoCID/index.html`. CID changes with updates.
- **IPNS**: Add IPNS key (`QmYourIPNSKey`), publish CID, share `ipfs://QmYourIPNSKey/index.html`. Update IPNS for new versions.
- **DNSLink**: Use domain (`yourdemos.com`), link to CID or IPNS via TXT record, share `https://yourdemos.com`. Update record or IPNS for changes.

### Recommendation
- **Start with IPNS**: Simpler, no domain cost, stable URL. Use: `https://ipfs.io/ipns/QmYourIPNSKey/index.html`.
- **Upgrade to DNSLink**: If you want a branded URL (e.g., `yourdemos.com`), add a domain later.

### Next Steps
1. Install IPFS and try the IPNS steps locally.
2. Share the resulting URL here—I can verify it works or troubleshoot with you!

Ready to launch your demos into the decentralized web? Let me know how to assist!