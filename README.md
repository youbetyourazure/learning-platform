# YouBetYourAzure Learning Platform

**Master Azure certifications through real-world healthcare analytics projects**

Azure certification learning platform with hands-on experience in Django, Python, Power BI, and Census API integration. Build production-grade skills through practical modules featuring geographic visualizations, catchment area analysis, and "Know Your Patients, Know Your Neighborhood" analytics.

## 🎯 What You'll Learn

This platform teaches Azure cloud architecture and development through a complete healthcare analytics application. You'll gain practical experience with:

- **Django Framework**: Models, views, templates, ORM, migrations, and admin customization
- **Python Development**: Data structures, functions, classes, file I/O, and API integration
- **Azure Services**: App Service, PostgreSQL, Static Web Apps, Blob Storage, and AI services
- **Power BI**: DAX formulas, relationships, calculated columns, and interactive visualizations
- **Census API**: Geographic analytics, demographic analysis, and catchment area mapping
- **Healthcare Analytics**: Patient demographics, appointment systems, and neighborhood profiling

## 📚 Course Modules

### Module 1: Django Fundamentals
**6 Lessons** - Build your first Django application with models, views, and templates

Learn Django basics through practical healthcare application development. Master the MVC pattern, URL routing, template rendering, and database migrations.

**Topics Covered:**
- Setting up Django projects and applications
- Creating models (Patient, Demographics, Insurance)
- Building views and templates
- URL configuration and routing
- Django Admin customization
- Database migrations and ORM

### Module 2: Healthcare Models & Relationships
**6 Lessons** - Advanced Django models with real-world relationships

Deep dive into Django's powerful ORM with healthcare-specific examples. Master foreign keys, many-to-many relationships, and complex querysets.

**Topics Covered:**
- Foreign key relationships (Patient → Demographics)
- Insurance model design and validation
- Appointment scheduling system
- Many-to-many relationships (Specialists ↔ Patients)
- Advanced QuerySets and filtering
- Aggregate functions and annotations

### Module 3: Census Geographic Analytics
**6 Lessons** - Integrate Census Bureau API for neighborhood demographics

Build a complete geographic analytics system using the U.S. Census Bureau API. Learn to map patient addresses, analyze catchment areas, and create Power BI dashboards.

**Topics Covered:**
- Census API integration (42 demographic variables)
- GPS-based catchment area analysis
- Power BI dashboard creation
- Venn diagram analytics (Census vs Patient demographics)
- Gap analysis (WHO's missing?)
- Patient routing and neighborhood profiling

## 🎥 Video Content

Each module includes professionally produced YouTube tutorials with:
- Step-by-step coding demonstrations
- Real-world problem-solving scenarios
- Azure deployment walkthroughs
- Interactive Power BI dashboard building
- Q&A sessions and best practices

**Module 1 YouTube Script**: Complete production script for 45-60 minute tutorial covering Django fundamentals with healthcare application examples.

## 🚀 Deployment

The platform uses **GitHub Pages** for hosting with optional custom domain configuration.

### Quick Start
1. Fork this repository
2. Enable GitHub Pages in Settings → Pages
3. Select `main` branch as source
4. Visit `https://yourusername.github.io/learning-platform/`

### Custom Domain
See [Deployment Guide](docs/deployment-guide.md) for detailed instructions on:
- DNS configuration (A records, CNAME)
- SSL certificate setup
- GitHub Pages custom domain
- GoDaddy/Azure DNS integration

## 📖 Documentation

- **[Deployment Guide](docs/deployment-guide.md)**: Step-by-step GitHub Pages setup with custom domain
- **[Build Summary](docs/build-summary.md)**: Technical deep-dive into Census API integration architecture

## 🏗️ Technical Architecture

Built on modern Azure cloud infrastructure:

```
Frontend: HTML, CSS, JavaScript (Static Web App)
Backend: Django 4.x + Python 3.11+ (Azure App Service)
Database: PostgreSQL (Azure Database for PostgreSQL)
Analytics: Power BI (Azure Power BI Embedded)
APIs: U.S. Census Bureau API, Azure Maps
Storage: Azure Blob Storage (videos, images)
```

## 🎓 Learning Outcomes

After completing this course, you'll be prepared for:

- **AZ-204**: Developing Solutions for Microsoft Azure
- **AZ-104**: Microsoft Azure Administrator
- **DP-203**: Data Engineering on Microsoft Azure
- **PL-300**: Microsoft Power BI Data Analyst

You'll have a portfolio project demonstrating:
- Full-stack web development
- Cloud architecture design
- API integration and data analysis
- Healthcare IT compliance knowledge
- Geographic analytics and visualization

## 📊 Real-World Application

The course is based on a production healthcare transitional care application serving hospital discharge planning teams. The "Know Your Patients, Know Your Neighborhood" system combines patient demographics with Census Bureau data to:

- Identify gaps between patient population and surrounding community
- Route patients to appropriate care facilities
- Analyze catchment areas for service planning
- Create PowerBI dashboards for leadership

This isn't just a tutorial—it's production code adapted for educational use.

## 🤝 Contributing

This is a personal educational business project. Content is provided for individual learning. If you build upon these materials, please provide attribution.

## 📜 License

Content licensed under MIT License - see [LICENSE](LICENSE) file for details.

## 🔗 Links

- **Website**: [youbetyourazure.com](https://youbetyourazure.com)
- **GitHub**: [github.com/youbetyourazure/learning-platform](https://github.com/youbetyourazure/learning-platform)
- **YouTube**: Coming soon - Module 1 tutorial
- **Support**: Contact via GitHub Issues

---

**YouBetYourAzure** - Real projects. Real skills. Real Azure certification success.
