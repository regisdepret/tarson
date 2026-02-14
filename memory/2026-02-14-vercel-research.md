# Vercel Research (2026-02-14)

## What is Vercel?

Vercel is a **serverless platform** for frontend frameworks and edge functions. It's optimized for:
- Static sites and SSR (Next.js, React, etc.)
- API routes and serverless functions
- Edge compute (CDN-based)
- Request/response workloads

## Pricing (Pro Tier)

**Base:** $20/developer/month

**Compute (Vercel Functions):**
- Active CPU: $0.128/hour (4 hrs free/month)
- Memory: $0.0106/GB-hour (360 GB-hrs free/month)
- Invocations: $0.60/1M (1M free/month)

**Other:**
- Edge Requests: 10M free, then $2/1M
- Data Transfer: 1TB free, then $0.15/GB
- Build Minutes: $0.014-$0.126/min (varies by machine)

## How it Works

- **Serverless:** Functions spin up on-demand, scale to zero when idle
- **Fluid Compute:** Optimizes concurrency (reuses instances for I/O-bound tasks)
- **Regional:** Functions run in specific regions (default: Washington DC)
- **Edge:** CDN-based routing for low latency

## Is it suitable for OpenClaw/TARSON?

### ❌ NO - Here's why:

**OpenClaw Gateway Requirements:**
1. **Always-on service** - Gateway must run 24/7
2. **WebSocket connections** - Persistent connections to nodes (MacMini, WindowsPC)
3. **Stateful sessions** - Maintains conversation history, context
4. **Long-running processes** - Can process tasks for minutes/hours
5. **Local file access** - Reads/writes workspace files, logs, configs

**Vercel Limitations:**
1. **Serverless/stateless** - Functions spin down after requests complete
2. **Max execution time** - Functions timeout (typically 10-60 seconds)
3. **No persistent connections** - WebSockets not supported for long duration
4. **Ephemeral filesystem** - No persistent local storage
5. **Cold starts** - Latency when functions wake up

### ✅ Better Options:

**For OpenClaw Gateway:**
- **VPS** (DigitalOcean, Linode, Vultr): $5-10/month, persistent service
- **Fly.io**: $0-10/month, good for always-on apps with global routing
- **Railway.app**: $5/month credit, easy deploys, persistent services
- **Oracle Cloud Free Tier**: ARM instances, always free (if you can get one)

**Where Vercel WOULD work:**
- Static dashboard/UI hosting
- Webhook receivers (if stateless)
- API endpoints (short-lived tasks)
- Edge functions for routing/middleware

## Cost Comparison (24/7 service)

**Vercel (if it worked):**
- Active CPU: 720 hrs/month × $0.128 = **$92.16/month**
- Memory (1GB): 720 GB-hrs × $0.0106 = **$7.63/month**
- **Total: ~$100/month** (just for compute, ignoring invocations/bandwidth)

**VPS (DigitalOcean):**
- 1GB RAM, 1 vCPU: **$6/month**
- 2GB RAM, 1 vCPU: **$12/month**
- Always-on, predictable pricing

## Verdict

**Vercel is NOT suitable for OpenClaw Gateway.** It's designed for frontend apps and short-lived functions, not persistent services. Stick with traditional VPS or container hosting (DigitalOcean, Fly.io, Railway) for the Gateway.

**Vercel COULD host:**
- A static web UI for OpenClaw dashboard (if built)
- Webhook endpoints for integrations
- Public-facing landing page

## Related

- Current setup: Gateway runs on local hardware (ubuntoris at 192.168.4.252)
- Nodes: MacMini, WindowsPC (persistent WebSocket connections)
- Future consideration: Fly.io or Railway for remote Gateway hosting
