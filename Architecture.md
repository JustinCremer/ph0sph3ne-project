# Infrastructure & System Architecture Manifest
Last Updated: September 2026

## 1. Ecosystem Overview
* **ph0sph3ne.com**
  * **Purpose:** Digital Graphic Novel Platform & Webcomic
  * **CMS / Application:** Ghost CMS (Self-Hosted)
  * **Server Host:** DigitalOcean Droplet
  * **Domain & DNS Registrar:** Sav.com
  * **Email Services:** Purelymail (`smtp.purelymail.com`)
  * **Payment Processor:** Stripe (Native Integration)
  * **Automation / Orchestration:** n8n (Server-side webhooks)

---

## 2. Server & Environment Topology
* **DigitalOcean Droplet Details:**
  * **OS / Stack:** Ubuntu / Node.js / Nginx
  * **Process Manager:** `systemd` / Ghost-CLI (`ghost-cli`)
  * **Application Path:** `/var/www/ghost` (or custom active directory)
  * **Database:** MySQL (`ghost_production`)
* **Key Commands for Maintenance:**
  * View real-time application logs: `ghost log -n 50`
  * Restart application instance: `ghost restart`
  * Check server process status: `ghost status`

---

## 3. Native Payment & Subscription Architecture
* **Integration Model:** Direct Ghost <-> Stripe OAuth Connect
* **Payment Flow:**
  1. Reader navigates to a static Page or Episode (e.g., `ph0sph3ne.com/episode-1`).
  2. Unauthenticated/Free users hit Ghost's built-in Portal paywall.
  3. Clicking **Subscribe** triggers Ghost’s native Stripe Checkout modal.
  4. Upon successful payment, Stripe fires webhooks directly to Ghost's API to update member status to `Paid`.
* **Resolved Integration Edge Case:**
  * *Note:* Stripe account was originally created with an unalterable "Managed Payments" account-level restriction, which caused payload schema rejections (`Ineligible for Managed Payments`). 
  * *Fix:* Resolved directly via Stripe Support by clearing the Managed Payments restriction on the Stripe backend. Ghost native checkout functions without workaround scripts.

---

## 4. Advanced Feature Pipeline (n8n & Admin API)
* **Ghost Admin API Connection:** NOT YET CREATED AS OF 9/5/26
  * **Purpose:** Query member subscription tenure, signup timestamps, and tier activity for custom frontend unlocks or rewards.
  * **Automation Engine:** n8n instance listening for Ghost/Stripe webhooks to handle event triggers.

---

## 5. Security & Secret Key Map
*Do not store raw keys here. Reference where environment secrets reside:*
* **Ghost Config:** Secrets stored in `/var/www/ghost/config.production.json`
* **Purelymail SMTP:** Credentials stored in Ghost Admin -> Settings -> Email newsletter settings.
* **Stripe Keys:** Standard Publishable & Secret keys generated via Ghost Admin -> Settings -> Integrations -> Stripe.
