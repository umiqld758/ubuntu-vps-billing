# Buy VPS Ubuntu: what €5 a month buys, which LTS to install, and what the second invoice costs (plan ranges, billing terms and the checks worth doing first)

A usable Ubuntu VPS starts at roughly $6.50 to $8 a month at the headline level, and most of those numbers are prepay artefacts. Hostinger's $6.49/mo requires $155.76 paid today for 24 months, and it renews at $11.99. BuyVPS's $6.79/mo is a two-year rate. Ava Hosting publishes a €5.00/mo entry point for its Ubuntu line and states that billing is monthly by default, so the commitment you are comparing is one month rather than two years.

Three things decide whether the box you pick is still the right one in eighteen months: which LTS you install, how the vCPU behind the label is allocated, and what the renewal line says. All three below, with the dates and the arithmetic.

## The short version

> **One site, a small API, a staging box:** the entry tier of Ava's Ubuntu range, published at €5.00/mo for single-service deployments, with monthly billing as the default. 👉 [check Ava Hosting's Ubuntu VPS plans and the current EUR pricing](https://my.ava.hosting/aff.php?aff=15562)
>
> **An app plus a database you actually tune:** 2–4 GB RAM, NVMe storage, and a look at disk I/O before you commit.
>
> **Docker, k3s or a CI runner:** 4 GB minimum. Ava states that cores are not shared between guests, which is the spec that decides whether a "2 vCPU" machine behaves like two cores or like one.

## What Ava Hosting's Ubuntu VPS includes

Ava sells this as an unmanaged KVM server with root access, not a managed app platform. The details that matter for an Ubuntu workload are published on the product pages rather than buried in a sales deck.

| Spec | What Ava publishes | What it changes for you |
| --- | --- | --- |
| Virtualisation | KVM, hardware-enforced isolation | Docker, Podman and k3s run natively; you get your own kernel, not a shared one |
| vCPU | Cores stated as not shared across guest instances | A 2 vCPU plan should hold its clock under load, unlike the 1:2–1:4 overcommit some budget hosts publish |
| Storage | NVMe on every plan | Random I/O is what Postgres and MySQL fsync wait on; capacity is the less interesting number |
| IPv4 | One dedicated IPv4, PTR record editable | Outbound mail and API callbacks don't inherit a shared IP's reputation |
| Billing | Prices in EUR, monthly by default, term selected at checkout | No multi-year prepay needed to reach the listed rate |
| Price band | €5.00/mo for single-service deployments up to about €40.00/mo for multi-service stacks | Roughly the difference between one Node process and a stack with a database, a queue and a cache |
| SLA | 99.9% uptime covering power, network and DDoS; customer-side errors and misconfiguration excluded | It covers the rack, not your broken deploy |
| Panels | Plesk, cPanel or DirectAdmin available as paid add-ons, or none at all | You don't pay for a control panel you'll never open |
| Company | AvaHost, trading since 2002; AVA HOST S.R.L., registered in Chișinău, Moldova | Data residency and jurisdiction, if either is a compliance question for you |

DDoS protection is included on all plans, support runs 24/7 by ticket and chat, and payment covers cards, PayPal, bank transfer, WebMoney and a long list of cryptocurrencies.

One caveat on the numbers: those product pages carry an early-2024 date stamp, so the €5.00–€40.00 band is the shape of the range rather than a promise about today's figure. Confirm the amount on the order form before you pay. 👉 [start an Ava Hosting Ubuntu VPS on monthly billing](https://my.ava.hosting/aff.php?aff=15562)

## 22.04, 24.04 or 26.04: pick by end-of-life date

Version numbers tell you almost nothing. The date standard security maintenance stops tells you everything.

| Release | Codename | Standard security maintenance | ESM until | Kernel baseline |
| --- | --- | --- | --- | --- |
| 22.04 LTS | Jammy Jellyfish | April 2027 | April 2032 | 5.15 (HWE 6.x optional) |
| 24.04 LTS | Noble Numbat | April 2029 | April 2034 | 6.8 |
| 26.04 LTS | Resolute Raccoon | April 2031 | not published | — |

22.04 has about five months of standard support left. Installing it today means planning an upgrade inside the first year of the server's life, which is the sort of task that gets postponed until it's urgent. 24.04 gives you until April 2029; 26.04, released in April 2026, until April 2031.

Default to 24.04 unless something in your stack needs the newer kernel or newer PHP and Python defaults, in which case go straight to 26.04. The in-place path works: `do-release-upgrade` moves 24.04 to 26.04, and the standard advice is to take a panel snapshot first and keep the rollback point for a week after the upgrade, not a day.

Image libraries are the practical constraint. OperaVPS lists all three LTS releases; BuyVPS's panel commits to 26.04 and 24.04; HOSTKEY's documented compatibility stops at 20.04/22.04. If you specifically need 26.04 on day one, confirm it's in the panel's image list before you pay rather than planning to upgrade a fresh box.

## What the second invoice costs

Hostinger's KVM 1 is the cleanest example of how the headline number works. You pay $155.76 today for 24 months, which is where the $6.49/mo comes from. At renewal the same plan is $11.99/mo for the same 24-month term, or $287.76. Over four years that's $443.52, an average of $9.24/mo. The KVM 8 goes from $25.99/mo to $49.99/mo, roughly double, on the same terms.

None of that is dishonest, and the 24-month price is genuinely what you pay for those 24 months. It just means three numbers belong in the same sentence: cash today, lock-in length, renewal rate.

Ava's structure is different in a way that matters if you're testing rather than committing. The Ubuntu pages give €5.00/mo as the monthly rate and state that billing is monthly by default, with the period chosen at checkout. You're comparing one month of spend against $155.76, and the second month costs the same as the first unless you deliberately chose a longer term for a discount. Prices are in EUR, so a non-euro card converts at whatever your bank charges.

## How much RAM each job needs

- **Static site, blog, small API:** 1 vCPU and 1 GB is enough for Nginx or Caddy plus PHP-FPM, or a compiled Go/Rust binary. Add a swap file as insurance, not as a strategy.
- **WordPress with a page cache:** 1–2 vCPU and 2 GB. Below 2 GB, PHP-FPM workers and MySQL start competing and the cache stops earning its keep.
- **One app plus Postgres or MySQL:** 2–4 GB, NVMe, and check IOPS. Database performance on a VPS is a storage question far more often than a CPU question.
- **Docker Compose stack, k3s, GitLab Runner:** 4 GB minimum. Confirm nested virtualisation is available if you want VMs inside the VPS, and confirm the vCPU allocation ratio before the term starts.
- **Remote desktop or a GUI:** possible via custom ISO upload, but GNOME or XFCE wants 2 GB just to be pleasant. Server plus SSH is the cheaper habit.

## Before you buy VPS Ubuntu hosting, confirm these six things

1. **`unattended-upgrades` is not preinstalled on minimal images.** Some hosts say so outright; others leave you a bare install and a root prompt. "Supported until 2029" describes Canonical's patch stream, not your server's patch level. Install `unattended-upgrades` and `fail2ban`, enable `ufw`, then check the reboot-required flag weekly.
2. **Ubuntu Pro and ESM are not bundled at this price level.** If you need FIPS modules or ESM coverage past the standard window, that's a Canonical subscription on top, and it's cheaper to know now than at the end of the LTS cycle.
3. **The vCPU ratio decides real performance.** One host publishes 1:2 to 1:4 sharing between guests, which means a "2 vCPU" plan can land on roughly one core's worth of time during contention. Ava states dedicated allocation instead. Same label, different machine.
4. **"Unmetered" and "unlimited" mean different things.** One provider gives 4–32 TB monthly quotas depending on plan; another advertises unmetered bandwidth; a third makes inbound traffic free and meters outbound by the plan you bought. Backups and rsync jobs are outbound. Read the definition before you schedule them.
5. **Plan upgrades may require a reboot.** One host documents upgrades completing in seconds but needing one restart, with data and settings preserved; another claims CPU, RAM and SSD upgrades finish in minutes with no reboot at all. Plan a maintenance window either way.
6. **Check the refund window and the SLA exclusions in writing.** Refund guarantees of 30 days exist at this price point, and so do SLAs that explicitly exclude application errors and misconfiguration. 👉 [see what Ava Hosting lists as included on every Ubuntu VPS plan](https://my.ava.hosting/aff.php?aff=15562)

## How Ava compares with three plans worth pricing

| Provider | Headline | What that price requires | Other term |
| --- | --- | --- | --- |
| Hostinger | $6.49/mo, KVM 1: 1 vCPU, 4 GB, 50 GB NVMe, 4 TB traffic | $155.76 today for 24 months | Renews at $11.99/mo |
| BuyVPS | From $6.79/mo | Two-year term | S-2 (1 vCPU, 2 GB, 30 GB NVMe) lists at $7.99/mo monthly |
| OperaVPS | $6.39/mo, Ubuntu 512: 1 vCPU, 0.5 GB, 10 GB NVMe | Annual prepay (20% off monthly) | $7.99/mo month to month |

The three are not the same product. OperaVPS's $6.39 buys half a gigabyte of RAM; Hostinger's $6.49 buys four, on a two-year commitment. Hostinger's advantage is memory per euro at the low end plus weekly backups and snapshots; BuyVPS's is EPYC Genoa hardware with NVMe RAID10 and a one-year option that trims about 10%; OperaVPS's is monthly billing on VMware with a choice of 21+ locations.

Ava sits in a different slot: monthly billing by default, a dedicated IPv4 and non-shared cores at the entry price, EUR-denominated, in Moldova rather than a Western European or US region. If your workload needs to be in Frankfurt or Ashburn for latency or compliance reasons, that's a reason to look elsewhere. If it doesn't, the fixed spec and the one-month commitment are worth more than a few cents of sticker price.

## FAQ

**Can I upgrade to a bigger plan later without rebuilding?**
On KVM, resizing usually keeps the disk and the OS. Whether it needs a reboot varies by provider, and one documents a single restart while another claims none at all. If you plan to start on the entry tier as a stepping stone, ask support what the resize does to your disk before you buy.

**How fast is provisioning?**
KVM hosts typically deliver a login within a minute to a few minutes, often with your SSH key injected at first boot. If you're migrating at 2 a.m., test the provisioning on a cheap plan first.

**Does Docker and Kubernetes work?**
Yes on KVM, because each VPS has its own kernel. Docker Compose on 2 GB is comfortable; k3s wants 2 GB and is happier on 4. If you need to run VMs inside the VPS, ask whether nested virtualisation can be enabled.

**Do I need Ubuntu Pro?**
Only if you need ESM after standard maintenance ends, FIPS-validated crypto, or Landscape management. For a web server on 24.04 inside its standard window, the free archive is enough.

**Can I install a desktop environment?**
Yes, via a custom ISO if the panel accepts uploads, or by installing a desktop package on Server. Budget 2 GB of RAM or more, and expect a slower experience than a laptop.

**Will reinstalling the OS wipe my data?**
Every reinstall is a full wipe. Take a provider snapshot or push a backup off-server first, and keep the snapshot until the new install has served real traffic for a few days.

## Which one to pick

One site, one API or a test environment: take the smallest tier Ava publishes, keep billing monthly, and spend the saved cash on backups you control. An app with a database you tune: step up to 2–4 GB and check the disk before you check the CPU. Docker, k3s or a CI runner: 4 GB or more, and verify the vCPU allocation and nested virtualisation before the term starts.

Two things to confirm on the order form before you pay: the billing period and the currency, and the refund window in writing. Everything else can be fixed after deployment. 👉 [pick your Ava Hosting Ubuntu VPS tier and confirm the billing term at checkout](https://my.ava.hosting/aff.php?aff=15562)
