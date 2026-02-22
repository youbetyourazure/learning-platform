# You Bet Your Azure - Deployment Strategy & Setup Guide

**Date:** February 22, 2026  
**Purpose:** Launch youbetyourazure.com learning platform with public access for students

---

## 🎯 Recommended Strategy: PUBLIC + NO REGISTRATION (Initially)

### Why Public & Open Access?

**Benefits for CBC & High School Outreach:**
- ✅ **Zero Friction:** Students can access immediately (no login walls)
- ✅ **Social Proof:** GitHub stars/forks show credibility
- ✅ **Contribution Friendly:** Students can submit corrections via Pull Requests
- ✅ **SEO Optimized:** Public content ranks in Google (drives organic traffic)
- ✅ **Thought Leadership:** Open-source education builds Grace & Company brand
- ✅ **Fable Partnership Ready:** Accessibility testing on public site shows transparency

**When to Add Registration (Phase 2):**
- Track learning progress (% modules completed)
- Issue certificates of completion
- Personalized recommendations
- Discussion forums/community
- Premium content (advanced modules)

**Estimated Timeline:** Launch public now → Add registration in 3-6 months after initial traction

---

## 📂 Repository Structure

### Option 1: New Standalone Repository (RECOMMENDED)

**Repo Name:** `youbetyourazure/learning-platform`  
**URL:** `https://github.com/youbetyourazure/learning-platform`

**Benefits:**
- Clean separation from Grace & Company SaaS code
- Different contributor permissions (students can't access patient data)
- Separate issue tracking (feature requests vs course feedback)
- Easier to showcase ("this is our educational project")

**Structure:**
```
learning-platform/
├── index.html                    # Landing page (already created)
├── modules/
│   ├── module-1-django-fundamentals.md
│   ├── module-2-healthcare-models.md (50% done)
│   └── module-3-authentication.md (planned)
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
├── partnerships/
│   ├── fable-case-study.md
│   ├── christian-brothers-curriculum.md
│   └── microsoft-customer-story.md
├── videos/
│   └── scripts/
│       └── module-1-script.md (already created)
└── README.md
```

### Option 2: Subdirectory in Existing Repo (Alternative)

Keep in `graceandcompanyllc/transitionalcareapp` under `/docs/learning/`

**Benefits:**
- Single repo to manage
- Direct link to production code examples

**Drawbacks:**
- Students might accidentally see production code
- Permissions harder to manage (can't give students write access to SaaS)

**Verdict:** Option 1 (new repo) is safer and cleaner.

---

## 🌐 Hosting Strategy: GitHub Pages vs Azure

### Comparison Table

| Feature | GitHub Pages | Azure Static Web Apps |
|---------|-------------|----------------------|
| **Cost** | FREE (public repos) | FREE (100GB/month) |
| **Custom Domain** | ✅ Yes (youbetyourazure.com) | ✅ Yes |
| **SSL Certificate** | ✅ Automatic (Let's Encrypt) | ✅ Automatic |
| **Build Time** | ~1 minute | ~30 seconds |
| **CDN** | ✅ Global CDN | ✅ Azure CDN |
| **Deployment** | Git push auto-deploys | GitHub Action auto-deploys |
| **Private Repos** | ❌ Paid ($4/month) | ✅ Free |
| **Custom Backend** | ❌ Static only | ✅ Azure Functions support |
| **Best For** | Simple landing pages | Full web apps with APIs |

### Recommendation: **GitHub Pages** (Simpler, Industry Standard)

**Why GitHub Pages?**
- Students know GitHub (learning tool + hosting platform combo)
- Zero configuration (enable in repo settings → done)
- Automatic deployment (push to `main` branch → live in 30 seconds)
- Community expectation (educators use GitHub Pages)
- Can migrate to Azure later if needed (same HTML/CSS/JS files)

---

## 🚀 Step-by-Step Setup Guide

### Phase 1: Create New GitHub Repository

**Actions for Woody:**

1. **Create Organization (Recommended)**
   - Go to https://github.com/organizations/new
   - Name: `youbetyourazure`
   - Email: `learn@youbetyourazure.com`
   - Plan: **Free** (public repos unlimited)
   - This separates learning platform from Grace & Company LLC

2. **Create Repository**
   - Click **New Repository**
   - Owner: `youbetyourazure`
   - Repo name: `learning-platform`
   - Description: "Enterprise Django Healthcare Development - Free Open Source Education"
   - **Public** ✅ (critical for GitHub Pages free tier)
   - Add README: ✅ Yes
   - Add .gitignore: None (static site)
   - License: **MIT License** (allows students to use/modify content)

3. **Clone Repository Locally**
   ```bash
   git clone https://github.com/youbetyourazure/learning-platform.git
   cd learning-platform
   ```

4. **Copy Existing Content**
   - Copy `docs/learning/index.html` → `index.html`
   - Copy `docs/learning/MODULE_1_Django_Fundamentals.md` → `modules/module-1.md`
   - Copy `docs/learning/MODULE_2_Healthcare_Models.md` → `modules/module-2.md`
   - Copy `docs/learning/MODULE_1_YouTube_Script.md` → `videos/scripts/module-1.md`

5. **Commit and Push**
   ```bash
   git add .
   git commit -m "Initial learning platform launch - Module 1 complete"
   git push origin main
   ```

### Phase 2: Enable GitHub Pages

**Actions for Woody:**

1. Go to repository **Settings**
2. Scroll to **Pages** (left sidebar under "Code and automation")
3. **Source:** Deploy from a branch
4. **Branch:** `main` / Root directory
5. Click **Save**

**Result:** Site live at `https://youbetyourazure.github.io/learning-platform/` within 1 minute

### Phase 3: Configure Custom Domain (youbetyourazure.com)

**DNS Setup Required:**

You own these domains:
- youbetyourazure.com (primary)
- youbetyourai.com
- youbetyourazureai.com
- youbetyouraz.com

#### DNS Records to Add (Your Domain Registrar)

**For youbetyourazure.com:**

| Type | Name | Value | TTL |
|------|------|-------|-----|
| A | @ | 185.199.108.153 | 3600 |
| A | @ | 185.199.109.153 | 3600 |
| A | @ | 185.199.110.153 | 3600 |
| A | @ | 185.199.111.153 | 3600 |
| CNAME | www | youbetyourazure.github.io | 3600 |

**What this does:**
- `youbetyourazure.com` → GitHub Pages
- `www.youbetyourazure.com` → Redirects to non-www

**Where to add these:**
- If using **GoDaddy:** DNS Management → Add Records
- If using **Cloudflare:** DNS → Add Record
- If using **Namecheap:** Advanced DNS → Add New Record

#### GitHub Pages Custom Domain Configuration

**After DNS propagates (15 minutes - 48 hours):**

1. Go to GitHub repo → **Settings** → **Pages**
2. **Custom domain:** Enter `youbetyourazure.com`
3. Click **Save**
4. Wait for DNS check (green checkmark appears)
5. ✅ **Enforce HTTPS** (checkbox) - automatically provisions SSL certificate

**Result:** 
- https://youbetyourazure.com → Your learning platform
- Auto-renewing SSL certificate
- HTTP → HTTPS redirect

---

## 🎓 Registration Strategy (Future Phase 2)

### Current: No Registration Required (Phase 1 - Launch)

**What students see:**
- Visit youbetyourazure.com
- Browse modules
- Read content
- No login required

**Benefits:**
- CBC teachers can share link in class (no signup friction)
- High school students access immediately
- SEO optimized (Google indexes all content)

### Future: Optional Registration (Phase 2 - 3-6 Months)

**When to add registration:**
- 1,000+ monthly visitors (validates demand)
- Students requesting progress tracking
- Certificate completion demand
- Community features (forums, Q&A)

**Implementation Options:**

#### Option A: GitHub OAuth (Recommended for Students)
- "Login with GitHub" button
- Students already have GitHub accounts (coding education)
- Tracks progress linked to GitHub profile
- Can showcase certificates on GitHub profile

#### Option B: Email/Password (Traditional)
- Simple registration form
- More accessible (non-coders)
- Requires password management

#### Option C: Hybrid (Best of Both)
- "Continue as Guest" (no registration)
- "Login with GitHub" (track progress)
- "Create Account" (email/password)

**Features After Registration:**
- ✅ Progress tracking (30% Module 2 complete)
- ✅ Certificate generation (PDF download)
- ✅ Discussion forums (ask questions)
- ✅ Code sandbox (run Django code in browser)
- ✅ Video watch history (resume where left off)

---

## 📧 Email Setup for learn@youbetyourazure.com

**Three Options:**

### Option 1: Microsoft 365 (Professional)
- **Cost:** $6/user/month
- **Features:** Outlook, Teams, OneDrive
- **Benefits:** Professional, integrates with existing Grace & Company M365
- **Setup:** admin.microsoft.com → Add domain → Verify DNS → Create mailbox

### Option 2: Google Workspace (Alternative)
- **Cost:** $6/user/month
- **Features:** Gmail, Drive, Meet
- **Benefits:** Students familiar with Gmail interface

### Option 3: Cloudflare Email Routing (FREE)
- **Cost:** $0
- **Features:** Forward learn@youbetyourazure.com → your existing email
- **Benefits:** Zero cost, no new inbox to check
- **Setup:** Cloudflare dashboard → Email Routing → Enable

**Recommendation for Launch:** Option 3 (Cloudflare forwarding) initially, upgrade to Option 1 (M365) if volume increases.

---

## 🤝 Partnership Outreach Strategy

### Christian Brothers College High School (CBC)

**Contact:**
- Computer Science Department Head
- STEM Coordinator
- Alumni Relations (mention you're an alumnus)

**Pitch:**
> "Grace & Company Healthcare Systems has built a 40-hour Django course teaching high school students to build real production systems. We're offering it FREE to CBC as an alumni giving-back initiative. Can we schedule 30 minutes to discuss integrating this into your Computer Science curriculum?"

**Value Proposition:**
- Free curriculum (worth $5,000+ if purchased)
- Real-world skills (Django used by Instagram, NASA, Spotify)
- Healthcare industry exposure (growing field with high demand)
- College application boost (portfolio projects)
- Azure certification pathway (Microsoft partnership angle)

**Follow-up:**
- Invite teachers to beta test (give feedback before student launch)
- Offer guest lecture (Zoom call with students about healthcare tech)
- Provide completion certificates (signed by Grace & Company)

### Local High Schools (Broader Reach)

**Target:**
- Public high schools in St. Louis area
- Schools with CS programs (AP Computer Science, coding clubs)
- Title I schools (free resources = high impact)

**Outreach Method:**
- Email superintendents / principals
- Attend school board meetings (public comment)
- Partner with STL Tech Coalition (local org connecting tech + education)

---

## 📊 Success Metrics (Phase 1 - First 6 Months)

**Baseline Targets:**
- 📈 **1,000 unique visitors** (Google Analytics)
- 🎓 **100 students** complete Module 1
- ⭐ **50 GitHub stars** (social proof)
- 🤝 **1 partnership** activated (CBC or local HS)
- 📺 **50 YouTube subscribers** (video content)
- 💬 **10 enhancement requests** (feedback via GitHub Issues)

**Measurement Tools:**
- Google Analytics (free, add tracking code to `index.html`)
- GitHub Insights (stars, forks, traffic built-in)
- YouTube Analytics (subscribers, views, watch time)

---

## 🛠️ Technical Checklist for Woody

### Immediate (Next 2 Hours):
- [ ] Create GitHub organization: `youbetyourazure`
- [ ] Create repository: `learning-platform` (public, MIT license)
- [ ] Copy content from Wellness repo → new repo
- [ ] Enable GitHub Pages in repo settings
- [ ] Test live URL: `https://youbetyourazure.github.io/learning-platform/`

### Short-term (Next 2 Days):
- [ ] Add DNS records for youbetyourazure.com (A records + CNAME)
- [ ] Configure custom domain in GitHub Pages settings
- [ ] Wait for DNS propagation (check with `dig youbetyourazure.com`)
- [ ] Enable HTTPS enforcement (auto SSL certificate)
- [ ] Test final URL: `https://youbetyourazure.com`

### Medium-term (Next 2 Weeks):
- [ ] Set up Cloudflare email forwarding: `learn@youbetyourazure.com`
- [ ] Add Google Analytics tracking code
- [ ] Create social sharing images (Open Graph tags)
- [ ] Write partnership outreach emails (CBC + 3 local HSs)
- [ ] Finish Module 2 (remaining 50%)
- [ ] Record Module 1 YouTube video

### Long-term (Next 3 Months):
- [ ] Launch Module 3: Authentication & Security
- [ ] Activate 1 partnership (guest lecture or curriculum adoption)
- [ ] Hit 1,000 visitors milestone
- [ ] Evaluate adding registration system (if demand exists)

---

## 💡 Quick Answers to Your Questions

**Q: Have you created a folder or do I need to create a repository?**
**A:** I created content in `docs/learning/` folder. You need to create a NEW repository (`youbetyourazure/learning-platform`) and copy this content there. New repo gives cleaner structure and student permissions.

**Q: Public or private repository?**
**A:** **PUBLIC** - Critical for GitHub Pages free tier, GitHub stars, student contributions, SEO, and partnership credibility. No downside since content is educational.

**Q: Hosting on Azure or GitHub?**
**A:** **GitHub Pages** (simpler for static content + students know GitHub). You can migrate to Azure Static Web Apps later if you add backend features (user registration, progress tracking).

**Q: DNS setup needed?**
**A:** **Yes** - Add 4 A records + 1 CNAME record (instructions above). Takes 15 minutes to configure, 24-48 hours to fully propagate globally.

**Q: Registration or open access?**
**A:** **Open access initially** (no login required) - Maximizes CBC/high school adoption. Add registration in Phase 2 (3-6 months) if students request progress tracking or certificates.

---

## 🎉 What Happens After Launch?

**Week 1:**
- Email CBC alumni relations + CS department
- Post on LinkedIn: "Launching free Django healthcare education"
- Share in Django community forums

**Week 2-4:**
- Schedule 3 partnership meetings (CBC, local HS, Fable)
- Record Module 1 YouTube video
- Finish Module 2 remaining lessons

**Month 2-3:**
- Guest lecture at CBC (if partnership activated)
- Launch Module 3
- Hit 500 visitors milestone

**Month 4-6:**
- Evaluate registration system (track progress, certificates)
- Apply for Microsoft Learn integration
- Submit for Django community spotlight

---

**Ready to launch?** Next step: Create GitHub organization and repository. Want me to provide the exact git commands to copy content from Wellness repo to new learning-platform repo?
