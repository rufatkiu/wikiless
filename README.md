<!-- 
  MAIN HEADER SECTION 
  Includes logo placeholder, badges, and project tagline
-->
![Wikiless Logo](https://example.com/path/to/your/logo.png "Logo placeholder - replace with your 1920x1080 image")

# Wikiless - Privacy-Focused Wikipedia Frontend

[![AGPL-3.0 License](https://img.shields.io/badge/License-AGPL%203.0-blue.svg)](https://opensource.org/licenses/AGPL-3.0)
[![GitHub Version](https://img.shields.io/github/package-json/v/Metastem/wikiless)](https://github.com/Metastem/wikiless)
[![Docker Pulls](https://img.shields.io/docker/pulls/metastem/wikiless)](https://hub.docker.com/r/metastem/wikiless)
[![Node.js Version](https://img.shields.io/badge/Node-%3E%3D16.0.0-brightgreen)](https://nodejs.org/)
[![Redis Requirement](https://img.shields.io/badge/Redis-%3E%3D6.0.0-red)](https://redis.io/)

**Wikiless** is a privacy-respecting, open-source alternative frontend for Wikipedia that eliminates tracking, minimizes data collection, and provides a clean reading experience. Built with Node.js and Express, it serves as a secure proxy between users and Wikipedia content while preserving all essential functionality.

## ✨ Comprehensive Feature Breakdown

### Privacy Protection System
- **Zero Tracking Architecture**:
  - No Google Analytics, ads, or user profiling
  - Server-side request anonymization
  - Stripped referral headers and metadata
- **Minimal Logging**:
  - Only essential operational logs (errors, system events)
  - No IP address storage
  - Configurable log retention period

### Performance Optimization
- **Multi-layer Caching**:
  - Redis-based page caching (default 1 hour TTL)
  - Media asset caching (images, videos - 24 hour TTL)
  - In-memory hot cache for frequent requests
- **Efficient Parsing Engine**:
  - Node-html-parser for fast DOM manipulation
  - Selective content extraction (removes tracking scripts)
  - Compressed response handling

### Customization Framework
- **Visual Themes**:
  - Dark mode (default)
  - Light mode
  - High contrast accessibility theme
- **Language Support**:
  - 300+ Wikipedia language editions
  - Automatic language detection
  - Manual language override
- **UI Customization**:
  - Adjustable font sizes
  - Article width control
  - Custom CSS injection points

### Technical Architecture
- **Core Stack**:
  - Node.js runtime (ES modules)
  - Express web server
  - Redis caching layer
  - Got HTTP client
- **Modular Design**:
  - Plugin-based middleware system
  - Decoupled parsing logic
  - Extensible API endpoints

## 🚀 Detailed Installation Guide

### System Requirements
- Node.js 16.x or higher
- Redis 6.0 or higher
- 1GB RAM minimum (2GB recommended)
- 100MB disk space (plus cache storage)

### Docker Deployment (Production)
```bash
# Basic deployment with volume for Redis persistence
docker run -d \
  --name wikiless \
  -p 8180:8180 \
  -v wikiless-data:/var/lib/redis \
  -e REDIS_URL=redis://localhost:6379 \
  -e PAGE_CACHE_EXPIRE=3600 \
  -e THEME=dark \
  --restart unless-stopped \
  metastem/wikiless:latest

# Cluster deployment with Redis Sentinel
docker run -d \
  --name wikiless \
  -p 8180:8180 \
  -e REDIS_URL=sentinel://sentinel-host:26379/mymaster \
  -e REDIS_SENTINEL_PASSWORD=yourpassword \
  metastem/wikiless:latest
```

### Manual Installation (Node.js)
```bash
# Clone repository
git clone https://github.com/Metastem/wikiless.git
cd wikiless

# Install dependencies
npm install --production

# Configure environment
cp wikiless.config.example wikiless.config
nano wikiless.config  # Edit configuration

# Start Redis service
redis-server --daemonize yes

# Launch application
npm start

# Verify running status
curl -I http://localhost:8180
```

### Development Environment Setup
```bash
# Clone and install
git clone https://github.com/Metastem/wikiless.git
cd wikiless
npm install

# Start development server with hot-reload
npm run dev

# Run test suite
npm test

# Build production assets
npm run build

# Lint code
npm run lint
```

## ⚙️ Complete Configuration Reference

### Environment Variables Table
| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `PORT` | integer | 8180 | HTTP server port |
| `BIND_ADDRESS` | string | 0.0.0.0 | Network interface binding |
| `REDIS_URL` | string | redis://localhost:6379 | Redis connection URL |
| `REDIS_PASSWORD` | string | (none) | Redis AUTH password |
| `REDIS_TLS` | boolean | false | Enable TLS for Redis |
| `PAGE_CACHE_EXPIRE` | integer | 3600 | Article cache TTL (seconds) |
| `IMAGE_CACHE_EXPIRE` | integer | 86400 | Media cache TTL (seconds) |
| `THEME` | string | dark | UI theme (dark/white) |
| `DEFAULT_LANGUAGE` | string | en | Default Wikipedia language |
| `USER_AGENT` | string | Wikiless/0.1.3 | Custom user agent string |
| `RATE_LIMIT` | integer | 100 | Requests per minute per IP |
| `TRUST_PROXY` | boolean | false | Enable reverse proxy support |

### Configuration File Specification (`wikiless.config`)
```javascript
{
  // Network settings
  "domain": "wikiless.org",
  "port": 8180,
  "bind": "0.0.0.0",
  
  // Content settings
  "language": "en",
  "theme": "dark",
  "content": {
    "cleanSocial": true,     // Remove social media links
    "cleanPrint": true,      // Remove print-specific elements
    "cleanReferences": false // Keep reference links
  },
  
  // Redis configuration
  "redis": {
    "url": "redis://localhost:6379",
    "password": "",
    "tls": false,
    "pool": {
      "min": 2,
      "max": 10
    }
  },
  
  // Cache settings
  "cache": {
    "pageExpire": 3600,      // 1 hour
    "imageExpire": 86400,    // 24 hours
    "searchExpire": 300,     // 5 minutes
    "maxSize": "500mb"       // Disk cache limit
  },
  
  // Security settings
  "security": {
    "rateLimit": 100,
    "trustProxy": false,
    "blockedPrefixes": ["Special:", "User:"]
  }
}
```

## 🖥️ Comprehensive Usage Documentation

### Core Functionality
```
http://your-instance.com/w/Article_Title
http://your-instance.com/w/Article_Title?lang=fr
http://your-instance.com/w/Article_Title?theme=white
```

### API Endpoints
| Endpoint | Method | Parameters | Description |
|----------|--------|------------|-------------|
| `/w/:title` | GET | `lang`, `theme` | Get Wikipedia article |
| `/api/search` | GET | `q`, `limit` | Search Wikipedia |
| `/api/random` | GET | `lang` | Get random article |
| `/api/languages` | GET | (none) | List supported languages |

### Advanced Query Parameters
| Parameter | Values | Example | Description |
|-----------|--------|---------|-------------|
| `lang` | ISO language code | `?lang=de` | Content language |
| `theme` | dark/white | `?theme=white` | UI color scheme |
| `width` | 600-1200 | `?width=800` | Article content width |
| `fontsize` | small/medium/large | `?fontsize=large` | Text size |
| `nocache` | 1 | `?nocache=1` | Bypass cache |
| `raw` | 1 | `?raw=1` | Get unprocessed HTML |

### Response Headers
| Header | Example | Description |
|--------|---------|-------------|
| `X-Wikiless-Cached` | true/false | Cache hit status |
| `X-Wikiless-TTL` | 3600 | Remaining cache time |
| `X-Wikiless-Origin` | en.wikipedia.org | Source Wikipedia |
| `X-Wikiless-Time` | 142ms | Processing time |

## 🛠️ Development & Contribution Guide

### Architecture Overview
```
src/
├── app.js              # Express application setup
├── wikiless.js         # Core middleware
├── parsers/            # Content processing
│   ├── article.js      # Article parser
│   ├── search.js      # Search results
│   └── utils.js       # DOM utilities
├── routes/             # API endpoints
├── cache/              # Redis integration
├── config/             # Configuration loader
└── static/             # Frontend assets
```

### Building from Source
```bash
# Install development dependencies
npm install --include=dev

# Transpile TypeScript
npm run build

# Run production build
NODE_ENV=production node dist/app.js
```

### Testing Methodology
```bash
# Unit tests
npm test:unit

# Integration tests
npm test:integration

# End-to-end tests
npm test:e2e

# Coverage report
npm run coverage
```

### Contribution Process
1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open pull request

### Code Style Requirements
- 2 space indentation
- ES module syntax
- Async/await preferred over callbacks
- JSDoc documentation for all functions
- TypeScript type definitions
- Prettier code formatting

## 📜 License Compliance

### AGPL-3.0 Requirements
1. **Source Availability**: Any modified version must provide complete source
2. **Network Interaction**: SaaS deployments must provide source to users
3. **License Preservation**: All copies must include license and copyright notices
4. **Patent Protection**: Grants explicit patent rights to all users

### Commercial Use Considerations
- Can be used commercially
- Modifications must be open-sourced
- Network services must provide user access to source
- No warranty or liability protection

Full license text: [LICENSE.md](LICENSE.md)

## 🔒 Security & Privacy

### Security Policy
- Vulnerability disclosure process in [SECURITY.md](SECURITY.md)
- Regular dependency updates
- Automated security scanning

### Privacy Protections
- No persistent user identifiers
- No third-party requests
- All external requests anonymized
- Optional Tor hidden service support

### Best Practices
```bash
# Recommended security flags for production
docker run -d \
  --read-only \
  --cap-drop=ALL \
  --security-opt no-new-privileges \
  --pids-limit 100 \
  metastem/wikiless
```

## 📈 Monitoring & Maintenance

### Health Check Endpoint
```
GET /healthz
Response: {"status":"ok","redis":true,"cacheHitRatio":0.97}
```

### Logging Configuration
```javascript
// Available log levels:
// error, warn, info, verbose, debug, silly
{
  "level": "info",
  "format": "json",
  "rotation": {
    "size": "10m",
    "keep": 7
  }
}
```

### Performance Metrics
```bash
# Prometheus metrics endpoint
GET /metrics

# Example metrics:
wikiless_requests_total 1423
wikiless_cache_hits 923
wikiless_response_time_ms_bucket{le="100"} 843
```

## 🤝 Community & Support

### Official Channels
- GitHub Discussions: Feature requests and Q&A
- Matrix Chat: #wikiless:matrix.org
- IRC: #wikiless on Libera.Chat

### Enterprise Support
- Available through Metastem
- Custom deployment consulting
- White-label solutions
- High-availability configurations

### Sponsorship
- GitHub Sponsors program
- OpenCollective funding
- Bitcoin/Ethereum donations accepted
