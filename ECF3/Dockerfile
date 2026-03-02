FROM python:3.11-slim

# Métadonnées
LABEL maintainer="RASMI Sofian"
LABEL description="Environnement ML pour prédiction de churn avec Scikit-learn et PySpark"

# Installation de Java Runtime (requis pour PySpark)
RUN apt-get update && apt-get install -y \
    default-jre-headless \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Variables d'environnement Java et Spark
ENV JAVA_HOME=/usr/lib/jvm/default-java
ENV PATH=$JAVA_HOME/bin:$PATH
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=jupyter
ENV PYSPARK_DRIVER_PYTHON_OPTS='notebook'

# Répertoire de travail
WORKDIR /workspace

# Copie du fichier requirements
COPY requirements.txt .

# Installation des dépendances Python
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Copie de tout le projet
COPY . .

# Exposition du port Jupyter
EXPOSE 8888

# Commande de démarrage Jupyter Notebook
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--NotebookApp.password=''"]
