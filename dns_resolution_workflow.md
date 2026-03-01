# How DNS Resolution Works: From Domain Name to IP Address

![Why Version Control Exists](./DNS_resolution.png)

Every time you open a website like `google.com`, your browser magically knows where to send the request.

But behind this “magic” is a system called **DNS (Domain Name System)** — one of the most important pillars of the internet.

In this blog, we’ll break down how DNS resolution works, what the `dig` command shows us, and how requests move through root servers, TLD servers, and authoritative servers before reaching the final IP address.

Let’s start with the basics.

---

## What Is DNS and Why Name Resolution Exists?

DNS is often called the **internet’s phonebook**.

Humans prefer names like:

- google.com
- github.com
- api.myapp.com

But computers only understand **IP addresses**, such as:

- 142.250.183.110
- 20.207.73.82

DNS exists to solve this problem.

> DNS converts human-readable domain names into machine-readable IP addresses.

Without DNS, you would need to remember numeric IP addresses for every website — not very practical.

---

## Where DNS Fits in a Real Request Flow

When you type `google.com` in your browser:

1. Browser asks OS for IP
2. OS checks local cache
3. If not found → asks DNS resolver
4. Resolver talks to multiple DNS servers
5. Final IP is returned
6. Browser connects to server

This lookup happens in milliseconds.

Now let’s see how we can observe this process ourselves.

---

## What Is the `dig` Command and When Is It Used?

`dig` stands for **Domain Information Groper**.

It is a command-line tool used to:

- Inspect DNS records
- Debug DNS issues
- Understand resolution paths
- Check authoritative servers
- Analyze TTL and response details

### Example:

```bash
dig google.com
```

This command shows:

- Which IP address is returned
- Which server answered
- Query time
- Record type (A, AAAA, NS, etc.)

For backend engineers and DevOps teams, `dig` is a daily troubleshooting tool.

---

## Understanding DNS Resolution Layers

DNS does not work with a single server.

It works in **layers**, like asking directions step by step.

The resolution flow looks like this:

```
Root Server → TLD Server → Authoritative Server → IP Address
```

Let’s explore each layer using `dig`.

---

## Understanding `dig . NS` (Root Name Servers)

Let’s query the root of DNS:

```bash
dig . NS
```

This asks:

> Who manages the top-level DNS system?

### What You’ll See

You’ll get a list like:

- a.root-servers.net
- b.root-servers.net
- c.root-servers.net
  ...

These are called **root name servers**.

---

### What Do Root Servers Do?

Root servers:

- Do NOT store IP addresses of websites
- Only tell you where to find TLD servers

Think of root servers as:

> The receptionist of the internet.

They don’t know your final destination — but they know **who can help you next**.

---

## Understanding `dig com NS` (TLD Name Servers)

Now let’s ask about `.com`:

```bash
dig com NS
```

This queries:

> Who manages all .com domains?

### What You’ll See

You’ll get servers like:

- a.gtld-servers.net
- b.gtld-servers.net
  ...

These are **TLD (Top-Level Domain) servers**.

---

### What Do TLD Servers Do?

TLD servers:

- Know which authoritative servers manage specific domains
- Handle domains like:
    - .com
    - .org
    - .net
    - .in

They don’t know Google’s IP — but they know:

> Which name server is responsible for google.com.

---

## Understanding `dig google.com NS` (Authoritative Name Servers)

Now let’s ask:

```bash
dig google.com NS
```

This tells us:

> Which servers are authoritative for google.com?

### Example Output

You’ll see something like:

- ns1.google.com
- ns2.google.com
- ns3.google.com

These are **authoritative name servers**.

---

### What Are Authoritative Servers?

Authoritative servers:

- Store the actual DNS records
- Return final IP addresses
- Are controlled by domain owners

This is the final authority.

If Google changes its IP, the authoritative server is updated first.

---

## Understanding `dig google.com` (Full DNS Resolution)

Now the main command:

```bash
dig google.com
```

This returns:

- A record (IPv4 address)
- AAAA record (IPv6 address)
- TTL values
- Response flags

Example:

```
google.com.  300  IN  A  142.250.183.110
```

This means:

- Domain: google.com
- Record type: A
- IP address: 142.250.183.110
- Cache duration: 300 seconds

---

## What Actually Happens Behind the Scenes?

When you run `dig google.com`, your system’s **recursive resolver** does the heavy lifting.

Here’s what it does internally:

1. Ask root server → where is .com?
2. Ask TLD server → where is google.com?
3. Ask authoritative server → give IP
4. Cache result
5. Return IP to client

This avoids repeating work for future queries.

---

## Why NS Records Matter

NS (Name Server) records tell DNS:

> Who is responsible for this domain?

They enable:

- Delegation of control
- High availability
- Load distribution
- Fault tolerance

Without NS records, DNS hierarchy would break.

---

## How Browsers Use This Information

When your browser requests:

```
https://google.com
```

DNS resolution happens first.

Only after IP is resolved:

- TCP handshake starts
- TLS handshake happens
- HTTP request is sent

If DNS fails:

- Website never loads
- Server is never contacted

This is why DNS is considered **critical infrastructure**.

---

## DNS and System Design Perspective

For backend and system designers, DNS is not just theory.

It impacts:

- API latency
- Global traffic routing
- Failover strategies
- Multi-region deployments

Examples:

- Using geo-based DNS routing
- Load balancing via DNS
- Blue-green deployments using DNS switching
- Zero-downtime migrations

Big systems rely heavily on DNS strategies.

---

## Final Thoughts

DNS is not magic.

It is a **layered, distributed, fault-tolerant system** that quietly works every time you open a website.

Understanding:

- Root servers
- TLD servers
- Authoritative servers
- dig command
- Resolution flow

makes you a stronger engineer — especially when working with production systems.

Next time your website loads instantly, remember:

DNS made the first move.
