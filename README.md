# 🌐 Portfolio Website Architecture                  🌐 Live Portfolio: praveen.live 

## Overview
This portfolio website is hosted on **AWS S3** with **CloudFront CDN** for global delivery, secured by **ACM SSL certificates**, and fronted by **Cloudflare** for DNS management and additional caching/security. The domain is registered in **GoDaddy**, with nameservers delegated to Cloudflare.

---

## 🔑 Components

| Layer | Service | Purpose |
|-------|---------|---------|
| **Domain Registrar** | GoDaddy | Domain ownership and initial nameserver configuration |
| **DNS + CDN Edge** | Cloudflare | DNS resolution, CNAME flattening for apex domain, optional caching/WAF |
| **Content Storage** | AWS S3 (Static Website Hosting) | Stores static portfolio files (HTML, CSS, JS, images) |
| **Global Distribution** | AWS CloudFront | Distributes content globally, handles HTTPS requests |
| **SSL/TLS Certificates** | AWS ACM | Provides SSL certificates for custom domain (`example.com`, `www.example.com`) |
| **Validation** | Cloudflare DNS | Hosts ACM validation CNAME records to issue SSL certificates |

---

## 🔄 Request Flow

1. **User enters domain** → `example.com` or `www.example.com`.
2. **DNS resolution** → GoDaddy delegates nameservers to Cloudflare.  
   - Cloudflare resolves DNS using **CNAME flattening** at apex (`@`) → CloudFront distribution domain (`d123abc.cloudfront.net`).
3. **Cloudflare → CloudFront**  
   - Cloudflare forwards request to CloudFront.  
   - SSL/TLS mode set to **Full (Strict)** to trust ACM certificate.
4. **CloudFront → S3**  
   - CloudFront fetches static content from S3 bucket origin.  
   - Default root object (`index.html`) serves homepage.
5. **Response back to user**  
   - Content delivered via CloudFront edge location, optionally cached by Cloudflare.

---

## ⚙️ Configuration Steps

1. **GoDaddy**
   - Change nameservers → Cloudflare’s assigned pair.
2. **Cloudflare**
   - Add DNS records:
     - `@` → CNAME → `d123abc.cloudfront.net` (flattened).
     - `www` → CNAME → `d123abc.cloudfront.net`.
   - Add ACM validation CNAMEs (DNS only, grey cloud).
   - Set SSL/TLS mode → Full (Strict).
3. **AWS ACM**
   - Request certificate for `example.com` + `www.example.com`.
   - Validate via Cloudflare DNS CNAMEs.
   - Status → Issued.
4. **AWS CloudFront**
   - Create distribution with S3 bucket origin.
   - Add Alternate Domain Names → `example.com`, `www.example.com`.
   - Attach ACM certificate.
   - Set Default Root Object → `index.html`.
5. **AWS S3**
   - Enable static website hosting.
   - Upload portfolio files.

---

## 📊 Architecture Diagram (Textual)

```
[User Browser]
      |
      v
   GoDaddy (Registrar)
      |
      v
   Cloudflare (DNS + Proxy)
      |
      v
   CloudFront (CDN + SSL via ACM)
      |
      v
   S3 Bucket (Static Website Hosting)
```

---

## ✅ Benefits
- **Scalable & Secure**: CloudFront + ACM ensures HTTPS everywhere.
- **Global Performance**: CloudFront edge locations + Cloudflare caching.
- **Simple Management**: Cloudflare DNS with CNAME flattening handles apex domain.
- **Cost‑effective**: S3 static hosting with pay‑as‑you‑go CDN.

---
by Praveen
