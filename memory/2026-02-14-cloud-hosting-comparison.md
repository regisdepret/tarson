# Cloud Hosting Cost Comparison (2026-02-14)

## OpenClaw Gateway REAL Requirements

**Compute:**
- **4GB RAM minimum** (for model context, caching, multiple sessions)
- 2 vCPU recommended
- 50GB storage
- 24/7 always-on service
- WebSocket support (persistent connections to nodes)

**Traffic Estimate (moderate usage):**
- Node WebSocket: ~5GB/month
- Dashboard access: ~2GB/month
- Model API calls: ~10-20GB/month
- Webhooks/callbacks: ~5GB/month
- File transfers/media: ~5-10GB/month
- **Total: ~30-50GB outbound/month**

---

## Oracle Cloud Free Tier ✅ STILL THE WINNER

**What's Free (Always):**
- **2x ARM Ampere A1 instances** (4 OCPUs, 24GB RAM total)
  - Can split into: **4 VMs × 1 OCPU × 6GB RAM**
  - Or: **2 VMs × 2 OCPU × 12GB RAM**
  - Or: **1 VM × 4 OCPU × 24GB RAM**
- **200GB total storage**
- **10TB outbound traffic/month**

**Recommended Config for OpenClaw:**
- **1x ARM VM: 2 OCPU, 12GB RAM**
- 50GB storage
- **Monthly Cost: $0**

**Caveats:**
- Hard to get (capacity limits, waitlists in popular regions)
- Account verification required
- Can be reclaimed if inactive
- Best availability: US, UK, Canada regions

**Verdict:** Free 12GB RAM ARM instance beats EVERYTHING. No contest.

---

## DigitalOcean

### Basic Droplet (4GB RAM, 2 vCPU)
- **Compute:** $24/month
- **Storage:** 80GB SSD included
- **Transfer:** 4TB/month included
- **Overage:** $0.01/GB (only after 4TB)
- **Total: $24/month**

### General Purpose (8GB RAM, 2 vCPU)
- **Compute:** $63/month
- **Storage:** 25GB SSD + NVMe
- **Transfer:** 4TB included
- **Total: $63/month** (overkill)

**Features:**
- Simple, predictable pricing
- Excellent uptime (99.99% SLA)
- Good global locations
- Automatic backups: +$4.80/month (20%)
- Weekly snapshots: ~$0.30/month (5GB)

**Verdict:** $24/month for reliable 4GB instance. Clean, simple.

---

## Fly.io

### Shared CPU (4GB RAM, 2 shared vCPU) - North America
- **Compute:** $22.22/month (shared-cpu-2x with 4GB)
- **Storage:** 3GB free, then $0.15/GB → +$7.05 for 50GB total
- **Transfer (50GB):** $1.00/month ($0.02/GB, no free tier)
- **Total: ~$30.27/month**

### Performance CPU (4GB RAM, 2 performance vCPU)
- **Compute:** $64.39/month
- **Storage:** +$7.05
- **Transfer:** $1.00
- **Total: ~$72.44/month**

**With Reservation Block (40% discount):**
- Buy $360/year block → get $50/month credits
- Effective cost: $30/year or $2.50/month
- **Total with reservation: ~$5.27/month** (if you prepay $360)

**Features:**
- Global edge deployment
- Auto-scaling
- Good for distributed apps
- Reservation blocks = big discount IF you prepay

**Verdict:** $30/month without commitment, OR $2.50/month if you prepay $360/year.

---

## AWS Lightsail

### 4GB RAM, 2 vCPU
- **Compute + Storage (80GB SSD):** $20/month
- **Transfer:** 4TB included
- **Overage:** $0.09/GB (after 4TB)
- **Total: $20/month**

### 8GB RAM, 2 vCPU
- **Compute + Storage (160GB SSD):** $40/month
- **Transfer:** 5TB included
- **Total: $40/month**

**Features:**
- AWS ecosystem integration
- Managed databases available
- Static IPs included
- Snapshots: $0.05/GB/month

**Verdict:** $20/month, slightly cheaper than DigitalOcean.

---

## Railway.app

**Developer Plan:**
- $5/month subscription
- **Usage charges:**
  - 4GB RAM instance: ~$15-20/month
  - Storage: included
  - Transfer (100GB free): $0
- **Total: ~$20-25/month**

**Features:**
- GitHub auto-deploy
- Simple interface
- Database add-ons
- No commitment

**Verdict:** Similar to DigitalOcean pricing, easier setup.

---

## Hetzner Cloud (Europe) 🆕

### CX21 (4GB RAM, 2 vCPU)
- **Compute:** €5.83/month (~$6.20/month)
- **Storage:** 40GB SSD
- **Transfer:** 20TB included
- **Total: ~$6.20/month** 🔥

### CPX21 (4GB RAM, 3 dedicated vCPU)
- **Compute:** €9.90/month (~$10.50/month)
- **Storage:** 80GB SSD
- **Transfer:** 20TB included
- **Total: ~$10.50/month**

**Features:**
- German data centers (excellent infrastructure)
- Insanely cheap
- 20TB traffic included
- Good performance

**Caveats:**
- EU-based (higher latency from US)
- Less global presence than DO/AWS
- Support in German/English

**Verdict:** CHEAPEST paid option at $6-10/month for 4GB!

---

## Linode (Akamai)

### Dedicated 4GB (2 vCPU)
- **Compute:** $24/month
- **Storage:** 80GB SSD
- **Transfer:** 4TB included
- **Total: $24/month**

**Features:**
- Recently acquired by Akamai
- Good global network
- Similar to DigitalOcean

**Verdict:** Same price as DigitalOcean, no advantage.

---

## Vultr

### Regular Cloud (4GB RAM, 2 vCPU)
- **Compute:** $24/month
- **Storage:** 80GB SSD
- **Transfer:** 3TB included
- **Total: $24/month**

**Features:**
- Many global locations
- Good performance
- Similar to DO/Linode

**Verdict:** Same tier as DigitalOcean.

---

## Traffic Cost Comparison (50GB outbound)

| Provider | Included Free | Overage Rate | Cost for 50GB |
|----------|---------------|--------------|---------------|
| Oracle Free | 10TB | N/A | **$0** |
| DigitalOcean | 4TB | $0.01/GB | **$0** |
| Hetzner | 20TB | €1.19/TB | **$0** |
| AWS Lightsail | 4TB | $0.09/GB | **$0** |
| Fly.io | 0GB | $0.02/GB | **$1.00** |
| Railway | 100GB | ~$0.01/GB | **$0** |
| Vultr | 3TB | $0.01/GB | **$0** |

**Takeaway:** Traffic is a non-issue. 50GB is well within ALL providers' free tiers except Fly.io.

---

## Final Recommendations (4GB RAM, 2 vCPU, 50GB storage)

### 🥇 Oracle Cloud Free Tier - **$0/month**
**Best option IF you can get it:**
- 2 OCPU ARM, 12GB RAM
- 10TB traffic
- Free forever
- **Caveat:** Waitlist, hard to get, verification required

---

### 🥈 Hetzner Cloud - **$6.20/month**
**Cheapest paid option:**
- 4GB RAM, 2 vCPU
- 40GB SSD, 20TB traffic
- Excellent German infrastructure
- **Caveat:** EU-based (higher US latency)

---

### 🥉 AWS Lightsail - **$20/month**
**Best US-based option:**
- 4GB RAM, 2 vCPU
- 80GB SSD, 4TB traffic
- AWS ecosystem
- Reliable, predictable

---

### Alternative: DigitalOcean - **$24/month**
**Best for simplicity:**
- 4GB RAM, 2 vCPU
- 80GB SSD, 4TB traffic
- Excellent UX
- Great support

---

### Budget Option: Fly.io with Reservation - **$2.50/month***
**IF you prepay $360/year:**
- 4GB RAM, 2 shared vCPU
- 40% discount with reservation block
- Global edge
- **Caveat:** $360 upfront commitment

---

## Cost Summary (4GB RAM, 50GB traffic/month)

| Provider | Compute | Traffic | **Total/Month** |
|----------|---------|---------|-----------------|
| **Oracle Free Tier** | $0 | $0 | **$0** 🏆 |
| **Hetzner Cloud** | $6.20 | $0 | **$6.20** ✨ |
| **Fly.io (prepaid $360/yr)** | $2.50* | $1 | **$3.50*** |
| **AWS Lightsail** | $20 | $0 | **$20** ✅ |
| **Railway** | $20-25 | $0 | **$22** |
| **DigitalOcean** | $24 | $0 | **$24** |
| **Linode** | $24 | $0 | **$24** |
| **Vultr** | $24 | $0 | **$24** |
| **Fly.io (pay-as-you-go)** | $22 | $1 | **$30** |

*Requires $360 upfront annual payment

---

## Final Verdict

**Try this order:**

1. **Oracle Cloud Free Tier** ($0) - Worth the waitlist
2. **Hetzner Cloud CX21** ($6.20) - Best price/performance
3. **AWS Lightsail** ($20) - Reliable US-based
4. **DigitalOcean** ($24) - Best UX/support

**Skip:** Fly.io (unless prepaying), Vercel (not suitable)

**Current setup cost:** $0 (running on local hardware)
**Recommended cloud migration:** Oracle Free → Hetzner ($6) → AWS/DO ($20-24)
