# Clone the repo (replace with your own token or use SSH)
git clone https://github.com/rigvedrs/serving-monitoring.git

# Pull the model from git lfs
sudo apt-get install git-lfs -y
git lfs install
cd serving-monitoring
git lfs pull
cd ..

# Run docker compose
docker compose -f serving-monitoring/serving/serving_workflow/docker-compose.yml up -d