Setting up DNSLink on DigitalOcean to serve your IPFS-hosted demo page involves using DigitalOcean’s DNS management to add a TXT record that links your domain to an IPFS CID or IPNS key. This assumes you’ve already uploaded your `demos` folder to IPFS (as per the previous instructions) and have a domain registered elsewhere (e.g., Namecheap, GoDaddy) that you’ll manage via DigitalOcean’s nameservers. Below are step-by-step instructions tailored to your demo project.

### Prerequisites
- **IPFS CID or IPNS Key**: You’ve uploaded your `demos` folder to IPFS and have a CID (e.g., `QmDemoCID`) or an IPNS key (e.g., `QmYourIPNSKey`) from the earlier steps.
- **Domain**: You own a domain (e.g., `yourdemos.com`) registered with a registrar.
- **DigitalOcean Account**: You have an account at [DigitalOcean](https://www.digitalocean.com).

---

### Step-by-Step Instructions to Set Up DNSLink on DigitalOcean

#### 1. Add Your Domain to DigitalOcean
- **Log In**: Sign into your DigitalOcean account at [cloud.digitalocean.com](https://cloud.digitalocean.com).
- **Navigate to Networking**: Click “Networking” in the top menu.
- **Add Domain**: In the “Domains” section, enter your domain (e.g., `yourdemos.com`) in the “Add a domain” field and click “Add Domain”. Leave the “Select a Droplet” dropdown empty since you’re not linking to a Droplet yet (we’re using IPFS instead).

#### 2. Delegate Your Domain to DigitalOcean’s Nameservers
- **Get Nameservers**: After adding the domain, DigitalOcean provides its nameservers (`ns1.digitalocean.com`, `ns2.digitalocean.com`, `ns3.digitalocean.com`) under the “NS Records” section.
- **Update Registrar**:
  - Log into your domain registrar (e.g., Namecheap).
  - Find the DNS settings or nameserver management section.
  - Replace the existing nameservers with:
    ```
    ns1.digitalocean.com
    ns2.digitalocean.com
    ns3.digitalocean.com
    ```
  - Save changes. Propagation may take 30 minutes to 48 hours.

#### 3. Add the DNSLink TXT Record
- **Return to DigitalOcean**: Go back to the “Networking” > “Domains” section and click your domain (e.g., `yourdemos.com`).
- **Add TXT Record**:
  - Click “Add Record” and select “TXT” from the dropdown.
  - **Hostname**: Enter `_dnslink` (this creates `_dnslink.yourdemos.com`). For subdomains (e.g., `demo.yourdemos.com`), use `_dnslink.demo`.
  - **Value**: Enter one of the following, depending on your preference:
    - For static CID: `dnslink=/ipfs/QmDemoCID`
    - For IPNS (recommended for updates): `dnslink=/ipns/QmYourIPNSKey`
    - Example: `dnslink=/ipfs/QmDemoCID`
  - **TTL**: Set to 300 (5 minutes) or leave default (e.g., 3600).
  - Click “Create Record”.

#### 4. Verify DNSLink Setup
- **Check Propagation**: Wait a few minutes to hours for DNS changes to propagate.
- **Test with Command**:
  - Install IPFS CLI if not already done (`ipfs init`, etc.).
  - Run: `ipfs resolve -r /dnslink/yourdemos.com`
  - Expected output: `/ipfs/QmDemoCID` or `/ipns/QmYourIPNSKey`.
- **Test in Browser**:
  - Via gateway: `https://ipfs.io/dnslink/yourdemos.com/index.html`
  - Via Cloudflare: `https://cloudflare-ipfs.com/dnslink/yourdemos.com/index.html`
  - Ensure your demo page loads with all subdirectories (e.g., `Prototypes/Base/original.html`).

#### 5. Pin Your Content
- **Local Pinning**: If running `ipfs daemon`, your `demos` folder is pinned locally.
- **Pinning Service**: For persistence:
  - **Pinata**: Sign up at [pinata.cloud](https://pinata.cloud), upload `demos` folder, pin the CID.
  - **Web3.Storage**: Use [web3.storage](https://web3.storage), upload, and pin.

#### 6. Update Content (Optional)
- **With CID**:
  - Modify a demo file, re-upload `demos`:
    ```
    ipfs add -r demos
    ```
  - Get new CID (e.g., `QmNewCID`), update TXT record to `dnslink=/ipfs/QmNewCID`.
- **With IPNS**:
  - Update and re-upload `demos`, get new CID, publish:
    ```
    ipfs name publish --key=demos-key QmNewCID
    ```
  - No DNS change needed; the IPNS key resolves to the new CID.

#### Final URL
- **DNSLink**: `https://yourdemos.com` (via a gateway like Cloudflare) or `ipfs://yourdemos.com/index.html` (native IPFS).

---

### Notes
- **Propagation Delay**: DNS changes may take time; use `dig _dnslink.yourdemos.com TXT` to check if the TXT record is live.
- **Gateway Dependency**: `https://yourdemos.com` relies on a gateway supporting DNSLink (e.g., Cloudflare’s IPFS gateway). Native `ipfs://` requires IPFS-aware browsers or extensions (e.g., Brave).
- **IPNS Advantage**: Pairing DNSLink with IPNS (`dnslink=/ipns/QmYourIPNSKey`) simplifies updates without DNS edits.

This setup integrates your IPFS-hosted demos with a custom domain on DigitalOcean’s DNS, leveraging DNSLink for a stable, branded URL. Test it out and let me know if you hit any snags—I can guide you through troubleshooting!