# On-Premise AI Document Processor

A completely self-hosted AI-powered document processing pipeline that runs entirely within your own infrastructure. Process PDF documents, extract text, and apply AI intelligence using locally hosted models—no external API calls, no cloud dependencies.

## Overview

This pattern demonstrates a production-ready prototype for PDF document processing using open-source tools orchestrated with n8n. Upload PDFs to a local MinIO bucket, automatically extract text using Docling, process with locally hosted LLMs via Ollama and LangChain, and receive structured markdown output back in your storage.

**Default behavior:** Summarizes PDF documents, but easily customizable for any document processing task.

## Architecture

```
PDF Upload → MinIO Bucket → n8n Workflow
                              ↓
                          Docling (Text Extraction)
                              ↓
                          LangChain + Ollama (AI Processing)
                              ↓
                          Markdown Output → MinIO Bucket
```

### Components

- **n8n** - Workflow orchestration and automation
- **Ollama** - Local LLM hosting (supports Llama, Mistral, and other models)
- **Docling** - Advanced PDF text extraction
- **MinIO** - S3-compatible object storage
- **LangChain** - LLM interaction framework

All components run as Docker containers on your local machine or server.

## Features

- ✅ **100% Self-hosted** - No external API calls or cloud dependencies
- ✅ **Privacy-first** - Your documents never leave your infrastructure
- ✅ **Customizable prompts** - Easily modify for different document processing tasks
- ✅ **S3-compatible** - Swap MinIO for any S3-compatible storage service
- ✅ **Production-ready architecture** - Scalable foundation for enterprise use
- ✅ **Open-source stack** - Full transparency and extensibility

## Prerequisites

- Docker Engine 20.10+ and Docker Compose 2.0+
- Minimum 8GB RAM (16GB recommended for larger models)
- 20GB free disk space
- Linux, macOS, or Windows with WSL2

## Quick Start

### 1. Clone and Navigate

```bash
git clone https://github.com/yourusername/data-platform-patterns.git
cd data-platform-patterns/onprem-ai-document-processor
```

### 2. Start Services

```bash
docker compose up -d
```

⏱️ **First run will take 5-10 minutes** as Ollama downloads the LLM model in the background.

### 3. Create n8n User

1. Open n8n at http://localhost:5678/home/workflows
2. Create your default user account on first access

### 4. Upload Documents

1. Open MinIO Console at http://localhost:9001/browser/pdf
   - Username: `minioadmin`
   - Password: `minioadmin`
2. Upload your PDF files
3. Monitor processing in n8n workflow executions

### 5. Access Results

Check the `pdf-summarized` bucket in MinIO for processed markdown files.

### Service URLs

- **n8n Workflow UI**: http://localhost:5678/home/workflows
- **MinIO Upload**: http://localhost:9001/browser/pdf
- **MinIO Console**: http://localhost:9001
- **Ollama API**: http://localhost:11434

### Customizing the Processing Prompt

The default prompt summarizes documents. To customize:

1. Open the n8n workflow
2. Navigate to the "AI Processing" node
3. Modify the prompt template

**Example prompts:**
- Summarization: `"Provide a concise summary of the following document:\n\n{text}"`
- Key extraction: `"Extract key points and action items from:\n\n{text}"`
- Translation: `"Translate the following text to Spanish:\n\n{text}"`
- Data extraction: `"Extract all dates, names, and monetary values from:\n\n{text}"`
- Q&A: `"Based on this document, answer: {question}\n\nDocument:\n{text}"`

## Usage Examples

### Basic Document Summarization

```bash
# Upload a PDF via MinIO Console or CLI
mc cp document.pdf minio/pdf/

# Check results bucket for output
mc ls minio/pdf-summarized/
```

### Batch Processing

Upload multiple PDFs to the `documents` bucket. The workflow processes them sequentially and outputs results with the same filename structure.

### Integration with Your Applications

Use MinIO's S3-compatible API to integrate with your existing applications:

```python
import boto3

s3 = boto3.client('s3',
    endpoint_url='http://localhost:9000',
    aws_access_key_id='minioadmin',
    aws_secret_access_key='minioadmin'
)

# Upload document
s3.upload_file('report.pdf', 'documents', 'report.pdf')

# Download result
s3.download_file('results', 'report.md', 'report-summary.md')
```

## Storage Alternatives

MinIO can be replaced with any S3-compatible storage service:

- **AWS S3** - Update endpoints and credentials
- **Cloudflare R2** - Zero egress costs
- **Wasabi** - Cost-effective cloud storage
- **Backblaze B2** - Budget-friendly alternative
- **Self-hosted SeaweedFS** - Distributed storage

Simply update the S3 connection settings in the n8n workflow.

## Monitoring and Logs

View logs for any service:

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f n8n
docker-compose logs -f ollama
docker-compose logs -f docling
```

## Troubleshooting

### Workflow not triggering
- Check MinIO bucket creation container worked correctly setting up buckets and webhook
- Verify the webhook or polling configuration in n8n
- Review n8n execution logs

### Ollama model errors
- Ensure sufficient RAM is available
- Pull the model manually: `docker exec -it ollama ollama pull llama3.2`
- Check model compatibility with your hardware

### Docling extraction issues
- Verify PDF is not password-protected
- Check PDF is not corrupted
- Review Docling container logs

### Out of memory
- Reduce model size (use smaller models)
- Increase Docker memory allocation
- Process documents sequentially rather than in parallel

## Known Issues

### Docker Desktop Memory Allocation

**Issue**: Ollama requires sufficient memory to load and run LLM models effectively.

**Solution**: If using Docker Desktop, ensure adequate memory allocation:

1. Open Docker Desktop Settings
2. Navigate to Resources → Advanced
3. Set Memory to **at least 8GB** (16GB recommended)
4. Click "Apply & Restart"

**Symptoms of insufficient memory:**
- Ollama container crashes or restarts
- Very slow model inference
- "Out of memory" errors in Ollama logs

### First Run Model Download

**Issue**: On first startup, Ollama needs to download the LLM model, which can take several minutes.

**Expected behavior:**
- First run: 5-10 minutes for model download (depending on internet speed)
- Model size: ~4-8GB depending on selected model
- Subsequent runs: Services start immediately

**How to monitor:**
```bash
# Watch Ollama download progress
docker compose logs -f ollama
```

You'll see output like:
```
pulling manifest
pulling model layers... 45% ████████░░░░░░░░
```

**Tip**: Don't upload documents until Ollama shows "model loaded successfully" in the logs.

### MinIO Bucket Creation

**Issue**: On first run, the required buckets (`pdf`, `results`) may need to be created manually.

**Solution**: Access MinIO console and create buckets if they don't exist:
1. Go to http://localhost:9001
2. Click "Create Bucket"
3. Create `pdf` and `pdf-summarized` buckets

## Performance Optimization

- **Model selection**: Smaller models (7B parameters) process faster but with potentially lower accuracy
- **GPU acceleration**: Add GPU support to the Ollama container for 10-50x speedup
- **Batch size**: Adjust concurrent document processing based on available resources
- **Storage**: Use SSD storage for MinIO data directory

## Production Deployment

This pattern is designed for local development and testing. For production use, we recommend:

- Implementing proper authentication and authorization
- Using production-grade secrets management
- Setting up high availability configurations
- Implementing monitoring and alerting
- Adding backup and disaster recovery
- Configuring SSL/TLS for all services
- Scaling considerations for high-volume processing

**Need help with production deployment?** [Contact MetaOps](https://metaops.solutions/) for expert consultation on self-hosted or cloud deployment.

## Security Considerations

⚠️ **Important for production use:**

- Change all default passwords
- Implement network isolation between services
- Use secure credential storage (e.g., Docker secrets, HashiCorp Vault)
- Enable SSL/TLS for all endpoints
- Implement rate limiting on document uploads
- Add antivirus scanning for uploaded files
- Configure proper file size limits

## Use Cases

This pattern is ideal for:

- Internal document processing and analysis
- Compliance and regulatory document review
- Contract analysis and summarization
- Research paper processing
- Meeting notes and report generation
- Knowledge base creation
- Data extraction from forms and invoices
- Multi-language document translation

## Extending the Pattern

- Add OCR for scanned documents (Tesseract)
- Implement document classification
- Add vector embeddings for semantic search
- Create document comparison workflows
- Build a UI for non-technical users
- Add support for Word, Excel, and other formats
- Implement approval workflows
- Add webhook notifications

## Contributing

Found an issue or have an improvement? Please open an issue or submit a pull request to the main repository.

## License

This pattern is licensed under the MIT License - see the [LICENSE](../LICENSE) file for details.

## Support

- **Documentation**: Check the main [Data Platform Patterns](../) README
- **Issues**: Open an issue in the main repository
- **Production Support**: [Contact MetaOps](https://metaops.solutions/)
- **Commercial Deployment**: [Request consultation](https://metaops.solutions/)

---

**Part of the [Data Platform Patterns](../)** library by [MetaOps](https://metaops.solutions/)
