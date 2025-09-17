# 🔐 SSL Certificates & Kubernetes Ingress with MetalLB

## 1️⃣ What is SSL?
- **SSL/TLS Certificate** = A digital lock 🔒 for your website.  
- It ensures:
  - ✅ **Encryption** – Data between browser & server is safe.  
  - ✅ **Authentication** – Confirms the website is real (not fake).  
  - ✅ **Trust** – Shows the 🔒 in browser.  

---

## 2️⃣ Types of SSL Certificates
| Type | Example | Use Case |
|------|---------|----------|
| **Single-domain** | `www.myapp.com` | Protects only **one domain** |
| **Wildcard** | `*.myapp.com` | Protects **all subdomains** under one domain (e.g., `api.myapp.com`, `web.myapp.com`) |
| **Multi-domain (SAN/UC)** | `myapp.com`, `shop.com`, `blog.net` | Protects **different domains** under one certificate |

👉 Since you bought a **Wildcard from GoDaddy**, it covers:  
- `api.myapp.com`  
- `web.myapp.com`  
- `anything.myapp.com`

---

## 3️⃣ Wildcard vs Multi-domain
- **Wildcard**: One certificate for **all subdomains** under a single domain.  
  - Example: `*.myapp.com` → `api.myapp.com`, `web.myapp.com`  
- **Multi-domain**: One certificate for **different domains**.  
  - Example: `myapp.com`, `otherapp.com`, `service.net`  

---

## 4️⃣ Your Setup (Kubernetes + Ingress + MetalLB)
- Website hosted on **Kubernetes cluster**.  
- Using **Ingress Controller** (like NGINX Ingress).  
- Using **MetalLB** as LoadBalancer to expose service externally.  

🔁 Flow:  
1. User hits `https://api.myapp.com`  
2. Request first goes to **MetalLB (LoadBalancer IP)**  
3. MetalLB forwards request to an **Ingress Controller pod**  
4. Ingress routes traffic to the correct **service/pods**  
5. SSL termination can happen at multiple layers 👇  

---

## 5️⃣ Ways to Apply SSL in Kubernetes

### **Option 1: SSL at Ingress (most common ✅)**
- Install certs as Kubernetes `Secret`.  
- Reference them in Ingress YAML.  

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
      - api.myapp.com
      - web.myapp.com
    secretName: wildcard-myapp-tls
  rules:
  - host: api.myapp.com
    http:
      paths:
      - backend:
          service:
            name: api-service
            port:
              number: 80
  - host: web.myapp.com
    http:
      paths:
      - backend:
          service:
            name: web-service
            port:
              number: 80