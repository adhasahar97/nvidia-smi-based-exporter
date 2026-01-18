# NVIDIA GPU Monitoring Stack

A Docker Compose setup for monitoring NVIDIA GPUs using Prometheus and Grafana.

## Components

- **[nvidia_gpu_exporter](https://github.com/utkuozdemir/nvidia_gpu_exporter)** - Exports NVIDIA GPU metrics via `nvidia-smi`
- **Prometheus** - Collects and stores metrics
- **Grafana** - Visualizes metrics with a pre-configured dashboard

## Prerequisites

- Linux host with NVIDIA GPU(s)
- Docker and Docker Compose
- NVIDIA drivers installed on the host

## Quick Start

```bash
git clone <repo-url>
cd nvidia-smi-based-exporter
docker-compose up -d
```

## Access

| Service | URL | Credentials |
|---------|-----|-------------|
| Grafana | http://localhost:3000 | admin / admin |
| Prometheus | http://localhost:9090 | - |
| GPU Metrics | http://localhost:9835/metrics | - |

The NVIDIA GPU Metrics dashboard is automatically provisioned and ready to use.

## Configuration

### Multiple GPUs

Edit `docker-compose.yml` to add additional GPU devices:

```yaml
nvidia_gpu_exporter:
  devices:
    - /dev/nvidiactl:/dev/nvidiactl
    - /dev/nvidia0:/dev/nvidia0
    - /dev/nvidia1:/dev/nvidia1
    - /dev/nvidia2:/dev/nvidia2
```

### Library Paths

The default volume mounts assume Ubuntu/Debian paths. Adjust if your system uses different locations:

```yaml
volumes:
  # Ubuntu/Debian
  - /usr/lib/x86_64-linux-gnu/libnvidia-ml.so:/usr/lib/x86_64-linux-gnu/libnvidia-ml.so:ro

  # RHEL/CentOS/Fedora (alternative)
  # - /usr/lib64/libnvidia-ml.so:/usr/lib/x86_64-linux-gnu/libnvidia-ml.so:ro
```

### Prometheus Scrape Interval

Edit `prometheus/prometheus.yml` to change the scrape interval:

```yaml
global:
  scrape_interval: 15s  # Default: 15 seconds
```

### Grafana Credentials

Change the default admin password in `docker-compose.yml`:

```yaml
environment:
  - GF_SECURITY_ADMIN_PASSWORD=your_secure_password
```

## Project Structure

```
.
├── docker-compose.yml
├── grafana/
│   ├── dashboards/
│   │   └── nvidia-gpu-metrics.json
│   └── provisioning/
│       ├── dashboards/
│       │   └── dashboards.yml
│       └── datasources/
│           └── datasource.yml
└── prometheus/
    └── prometheus.yml
```

## Dashboard Metrics

The included Grafana dashboard (ID: 14574) displays:

- GPU utilization percentage
- Memory usage and allocation
- Temperature
- Power consumption
- Fan speed
- Clock speeds (graphics, memory, SM, video)
- Throttle reasons
- P-State information

## Troubleshooting

### Exporter not starting

Verify NVIDIA drivers are installed:
```bash
nvidia-smi
```

Check if devices exist:
```bash
ls -la /dev/nvidia*
```

### No metrics in Prometheus

Check if the exporter is accessible:
```bash
curl http://localhost:9835/metrics
```

Verify Prometheus targets at http://localhost:9090/targets

### Permission errors

Ensure Docker has access to GPU devices. You may need to run:
```bash
sudo usermod -aG video $USER
```

## License

MIT
