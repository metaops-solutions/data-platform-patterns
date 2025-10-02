# Data Platform Patterns

A curated library of production-ready data platform patterns by [MetaOps](https://metaops.solutions/), designed to help you quickly deploy and run open-source, self-hosted data products.

## Overview

This repository provides battle-tested Docker Compose configurations and deployment patterns for various data processing, analytics, and AI workloads. Each pattern is designed to accomplish specific data engineering tasks using best-in-class open-source tools.

**All patterns are free to use** under the MIT License.

## Quick Start

1. Clone this repository
2. Navigate to the pattern you want to use
3. Follow the pattern-specific README instructions
4. Run `docker-compose up -d`

That's it! Each pattern is ready to run locally or in test environments within minutes.

## 🏢 Production Deployments

While these patterns are tested and can run in production, **we strongly recommend consulting with us for production-grade deployments**. Our team can help you with:

- Security hardening and compliance requirements
- High availability and disaster recovery configurations
- Performance optimization and scaling strategies
- Integration with your existing infrastructure
- Cloud deployment (AWS, Azure, GCP, or hybrid)
- Ongoing support and maintenance

[**Get in touch for production deployment support →**](https://metaops.solutions/)

## Available Patterns

Each pattern lives in its own directory with comprehensive documentation:

- **[`onprem-ai-document-processor/`](./onprem-ai-document-processor/)** - AI-powered document processing pipeline using n8n, Ollama, Docling, and MinIO for automated document ingestion, analysis, and storage

*More patterns coming soon...*

## Pattern Structure

Each pattern directory contains:
- `README.md` - Detailed setup and usage instructions
- `docker-compose.yml` - Complete service definitions
- Configuration files and environment templates
- Architecture diagrams and usage examples

## 📬 Stay Connected

Want to be notified about new patterns and updates? Have questions about production deployments?

- **Visit our website:** [metaops.solutions](https://metaops.solutions/)
- **Star this repository** to stay updated with new patterns
- **Watch releases** for notifications on major updates

## Use Cases

These patterns are ideal for:
- Rapid prototyping and proof-of-concepts
- Development and testing environments
- Learning and experimentation with data platforms
- Small-scale production workloads (with appropriate safeguards)
- Foundation for custom enterprise deployments

## Contributing

Found an issue or have suggestions? Feel free to open an issue or submit a pull request. We welcome community contributions!

## About MetaOps

MetaOps specializes in data platform engineering and deployment. We help organizations design, build, and operate modern data infrastructure - whether self-hosted, cloud-based, or hybrid.

Learn more at [metaops.solutions](https://metaops.solutions/)

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Ready to deploy?** Navigate to a pattern directory and get started in minutes.  
**Need production support?** [Contact MetaOps](https://metaops.solutions/) for expert consultation.