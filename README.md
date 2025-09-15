# Complete MLOps Pipeline for Chest X-ray Abnormality Detection System on Cloud

An intelligent radiological assistance system that combines YOLOv11-L object detection with comprehensive MLOps infrastructure to help radiologists identify and localize chest X-ray abnormalities with enhanced accuracy and efficiency.

## 🎯 Overview

This system provides AI-assisted chest X-ray analysis for radiology departments, offering both pathology classification and precise localization through bounding boxes. Built with the VinDr-CXR dataset and validated by multiple radiologists, the system enhances diagnostic workflow while maintaining radiologists as the ultimate decision-makers.

### Key Benefits

- **Reduced Missed Pathologies**: Identifies and highlights potential abnormalities that might be overlooked during routine reads
- **Improved Efficiency**: Pre-highlights regions of concern, allowing radiologists to focus attention on suspicious areas
- **Second Opinion Support**: Provides automated consultation that can confirm findings or prompt reconsideration
- **Scalable Infrastructure**: Cloud-native architecture with automated CI/CD pipelines

## 🏗️ Architecture

The system follows a microservices architecture with the following components:

```
┌───────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Data Pipeline   │    │  Model Training │    │  Model Serving  │
│                   │    │                 │    │                 │
│ • ETL Processing  │    │ • YOLOv11-L     │    │ • Triton Server │
│ • Data Validation │    │ • Distributed   │    │ • ONNX Runtime  │
│ • Quality Checks  │    │   Training      │    │ • TensorRT      │
└───────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Monitoring    │    │  Orchestration  │    │   Storage       │
│                 │    │                 │    │                 │
│ • Prometheus    │    │ • Kubernetes    │    │ • MinIO         │
│ • Grafana       │    │ • ArgoCD        │    │ • PostgreSQL    │
│ • Alert Manager │    │ • Argo Workflows│    │ • Swift Storage │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 🚀 Features

### Model Capabilities
- **Multi-class Detection**: Identifies 14+ chest abnormalities including pneumothorax, consolidation, and pleural effusion
- **Precise Localization**: Provides bounding boxes for abnormality regions
- **Confidence Scoring**: Outputs confidence levels for each detection
- **High Performance**: Optimized for both accuracy and inference speed

### Infrastructure Features
- **Distributed Training**: Multi-GPU training with Ray for faster model development
- **Model Versioning**: MLflow integration for experiment tracking and model registry
- **Automated Deployment**: Deployment with staging, canary, and production environments
- **Real-time Monitoring**: Comprehensive monitoring of model performance and system health
- **Data Pipeline**: Automated ETL processes with data quality validation

### Optimization Features
- **Multiple Serving Options**: CPU, GPU, and edge deployment configurations
- **Model Optimization**: ONNX conversion, quantization, and TensorRT acceleration
- **Dynamic Batching**: Optimized throughput for varying loads
- **Fault Tolerance**: Automatic recovery and failover mechanisms
- **Advanced Performance Tuning**: Comprehensive Triton server configurations for maximum efficiency

## 📊 Performance Metrics

| Metric | Target | Achieved |
|--------|--------|----------|
| Inference Latency (Single) | <200ms | ~150ms |
| Batch Throughput | 30 FPS | 45+ FPS |
| Model Accuracy (mAP@0.5) | >0.75 | 0.82 |
| Concurrent Requests | 10+ | 25+ |
| Uptime | 99.9% | 99.95% |

### 🚀 Advanced Performance Optimizations

Performance experiments have achieved following results across multiple deployment scenarios:

#### GPU Configurations (Full Model)
- **Custom TensorRT V2**: 157.8 infer/sec throughput with optimized memory allocation
- **Custom TensorRT V1**: Ultra-low latency (19.3ms) with dynamic batching
- **Default TensorRT**: 129.0 infer/sec for balanced performance
- **ONNX Runtime**: 78-79 infer/sec with CUDA acceleration

#### CPU Configurations (Nano Model)
- **OpenVINO Nano**: 14.6 infer/sec - 15x faster than standard CPU backends
- **Edge Deployment**: Optimized for resource-constrained environments
- **Memory Efficiency**: Reduced model size with compact output format

> 📈 **Detailed performance analysis, configuration files, and optimization insights are available in the [Triton Configuration Repository](https://github.com/rigvedrs/YOLO-Triton-Configs-for-ONNX-OpenVino-TensorRT)**

## 🛠️ Technology Stack

### Core ML Stack
- **Model**: YOLOv11-L (Ultralytics)
- **Training**: PyTorch with distributed training via Ray
- **Serving**: NVIDIA Triton Inference Server
- **Optimization**: ONNX Runtime, TensorRT, OpenVINO

### Infrastructure
- **Orchestration**: Kubernetes, ArgoCD, Argo Workflows
- **Storage**: MinIO (object), PostgreSQL (metadata), Swift (datasets)
- **Monitoring**: Prometheus, Grafana, Alert Manager
- **MLOps**: MLflow, Label Studio

### DevOps
- **IaC**: Terraform, Ansible
- **CI/CD**: GitOps with ArgoCD
- **Containerization**: Docker, Kubernetes
- **Cloud**: Multi-cloud compatible (tested on Chameleon Cloud)

## 🚀 Quick Start

### Prerequisites
- Kubernetes cluster (1.24+)
- NVIDIA GPUs (optional, for GPU acceleration)
- 250GB+ storage for datasets
- Docker and Terraform installed

### Installation

1. **Clone the Repository**
```bash
git clone https://github.com/your-org/chest-xray-detection.git
cd chest-xray-detection
```

2. **Set Up Infrastructure**
```bash
# Install Terraform
mkdir -p ~/.local/bin
wget https://releases.hashicorp.com/terraform/1.10.5/terraform_1.10.5_linux_amd64.zip
unzip terraform_1.10.5_linux_amd64.zip
mv terraform ~/.local/bin

# Create cloud resources
cd iac/tf/kvm
export TF_VAR_suffix=your-project
export TF_VAR_key=your-ssh-key
terraform init
terraform apply -auto-approve
```

3. **Configure Kubernetes**
```bash
# Set up Kubernetes cluster
cd ./iac/ansible
ansible-playbook -i inventory.yml pre_k8s/pre_k8s_configure.yml
cd k8s/kubespray
ansible-playbook -i ../inventory/mycluster --become --become-user=root ./cluster.yml
cd ../..
ansible-playbook -i inventory.yml post_k8s/post_k8s_configure.yml
```

4. **Deploy ML Platform**
```bash
# Deploy core services
ansible-playbook -i inventory.yml argocd/argocd_add_platform.yml

# Set up environments
ansible-playbook -i inventory.yml argocd/workflow_build_init.yml
ansible-playbook -i inventory.yml argocd/argocd_add_staging.yml
ansible-playbook -i inventory.yml argocd/argocd_add_canary.yml
ansible-playbook -i inventory.yml argocd/argocd_add_prod.yml
ansible-playbook -i inventory.yml argocd/workflow_templates_apply.yml
```

### Running the Data Pipeline

1. **Offline ETL Pipeline**
```bash
cd deployment/docker
docker-compose -f docker-compose-etl.yaml up
```

2. **Online Data Simulation**
```bash
cd ./data-pipeline/data-simulation
docker-compose -f docker-compose-data-simulation.yaml up -d
```

3. **Production Pipeline**
```bash
cd ./deployment/docker
docker-compose -f docker-compose-production.yaml up
```

### Model Training

```bash
# Start distributed training
python model_train/train_yolo.py --distributed --gpus 4
```

### Model Serving

The system automatically deploys trained models through the GitOps pipeline. Access the API endpoints:

- **Production**: `http://your-ip/predict`
- **Staging**: `http://your-ip:8081/predict`
- **Canary**: `http://your-ip:8080/predict`

## 📋 API Usage

### Prediction Endpoint

```bash
# Single image prediction
curl -X POST "http://your-ip/predict" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@chest_xray.jpg"

# Response
{
  "predictions": [
    {
      "class": "pneumothorax",
      "confidence": 0.89,
      "bbox": [0.23, 0.15, 0.45, 0.32]
    }
  ],
  "inference_time": 0.145
}
```

### Batch Processing

```python
import requests
import json

# Batch prediction
files = [('files', open(f'image_{i}.jpg', 'rb')) for i in range(5)]
response = requests.post('http://your-ip/predict/batch', files=files)
results = response.json()
```

## 📊 Monitoring and Observability

### Access Dashboards

- **Grafana**: `http://your-ip:3000` (admin/admin)
- **MLflow**: `http://your-ip:8000`
- **MinIO**: `http://your-ip:9001`
- **Label Studio**: `http://your-ip:5000`
- **Data Dashboard**: `http://your-ip:8501`

### Key Metrics Monitored

- Model performance (accuracy, precision, recall)
- Inference latency and throughput
- System resource utilization
- Data quality and drift detection
- Error rates and alert conditions

### 🔍 Advanced Monitoring & Data Drift Detection

#### Production Monitoring Features
- **Real-time Model Performance Tracking**: Continuous accuracy monitoring with automated alerts
- **Data Drift Detection**: Advanced statistical analysis to detect distribution shifts in incoming data
- **Concept Drift Monitoring**: Tracks changes in the relationship between features and predictions
- **Model Decay Detection**: Identifies when model performance degrades over time
- **A/B Testing Framework**: Compare model versions in production environments

#### Multi-Branch Monitoring Architecture
Our monitoring system supports multiple deployment scenarios with specialized branches:

- **Production Branch**: Full-scale monitoring with comprehensive alerting
- **Staging Branch**: Pre-production validation with synthetic data testing
- **Canary Branch**: Gradual rollout monitoring with performance comparison
- **Data Drift Branch**: Specialized monitoring for data quality and distribution analysis

> 🔬 **Additonal serving and monitoring configuration available in the [Serving Monitoring Repository](https://github.com/rigvedrs/serving-monitoring)**

## 🔄 Continuous Integration/Deployment

The system implements automated pipelines:

1. **Code Changes** → Git repository
2. **ArgoCD Detection** → Automatic sync
3. **Staging Deployment** → Automated testing
4. **Canary Release** → Gradual traffic shift
5. **Production Rollout** → Full deployment

### Model Lifecycle

1. **Training**: Distributed training with hyperparameter optimization
2. **Validation**: Automated testing on held-out datasets
3. **Staging**: Deployment to staging environment for integration testing
4. **Canary**: Limited production traffic for performance validation
5. **Production**: Full deployment with monitoring
6. **Retraining**: Automatic retraining based on performance degradation

## 🔧 Configuration

### Environment Variables

```bash
# Model serving configuration
MODEL_PATH=/models/yolov11l.onnx
BATCH_SIZE=8
MAX_WORKERS=4
DEVICE=cuda  # or cpu

# Monitoring configuration
PROMETHEUS_PORT=9090
GRAFANA_PORT=3000
ALERT_WEBHOOK_URL=your-webhook-url

# Storage configuration
MINIO_ACCESS_KEY=your-access-key
MINIO_SECRET_KEY=your-secret-key
SWIFT_CONTAINER=your-container
```

### Model Configuration

```yaml
# model_config.yaml
model:
  name: yolov11l-chest-xray
  version: "1.0.0"
  input_size: [1024, 1024]
  classes: 14
  confidence_threshold: 0.25
  iou_threshold: 0.45

serving:
  max_batch_size: 16
  preferred_batch_size: 8
  max_queue_delay_microseconds: 100
```

## 🧪 Testing

### Unit Tests
```bash
pytest tests/unit/ -v
```

### Integration Tests
```bash
pytest tests/integration/ -v
```

### Load Testing
```bash
# Using locust
locust -f tests/load/test_api.py --host=http://your-ip
```

### Model Validation
```bash
python tests/model/validate_model.py --model-path /path/to/model
```

## 📈 Performance Optimization

### Model Optimizations

1. **ONNX Conversion**: Reduces model size and improves portability
2. **Quantization**: INT8 quantization for CPU deployment
3. **TensorRT**: GPU acceleration for NVIDIA hardware
4. **Pruning**: Removes unnecessary model parameters

### System Optimizations

1. **Dynamic Batching**: Automatically batches requests for improved throughput
2. **Multi-instance Serving**: Parallel model instances for concurrent processing
3. **Caching**: Redis-based result caching for repeat queries
4. **Load Balancing**: Distributes requests across multiple instances

## 🔒 Security and Compliance

- **Data Privacy**: PHI data handling compliance (HIPAA considerations)
- **Access Control**: Role-based access control (RBAC) for all services
- **Audit Logging**: Comprehensive logging for all system interactions
- **Secure Communication**: TLS encryption for all API endpoints
- **Vulnerability Scanning**: Regular security scans of container images

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Setup

```bash
# Set up development environment
python -m venv venv
source venv/bin/activate
pip install -r requirements-dev.txt
pre-commit install
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **VinBigData** for the chest X-ray dataset
- **Ultralytics** for the YOLOv11 implementation
- **NVIDIA** for Triton Inference Server
- **Chameleon Cloud** for infrastructure support

## 🔗 Related Projects & Resources

### Performance Optimization Research
- **[YOLO-Triton-Configs Repository](https://github.com/rigvedrs/YOLO-Triton-Configs-for-ONNX-OpenVino-TensorRT)**: Comprehensive Triton server configurations, performance benchmarks, and optimization insights for maximum inference efficiency across GPU and CPU deployments.

### Advanced Monitoring & Production Systems
- **[Serving Monitoring Repository](https://github.com/rigvedrs/serving-monitoring)**: Production-ready monitoring solutions with advanced data drift detection, model performance tracking, and multi-branch deployment monitoring capabilities.

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/your-org/chest-xray-detection/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/chest-xray-detection/discussions)
- **Documentation**: [Project Wiki](https://github.com/your-org/chest-xray-detection/wiki)

## 🗺️ Roadmap

- [ ] Edge deployment optimization for mobile devices
- [ ] Integration with PACS systems
- [ ] Multi-language support for international deployment
- [ ] Advanced explainability features (GradCAM, SHAP)
- [ ] Federated learning capabilities
- [ ] Integration with electronic health records (EHR)

---

**⚠️ Medical Disclaimer**: This system is designed to assist radiologists and should not be used as the sole basis for medical decisions. Always consult with qualified healthcare professionals for medical diagnosis and treatment.