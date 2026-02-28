# ─────────────────────────────────────────────────────────────────────────────
# Ilija Full_Autonomy_Edition – Docker Image
# ─────────────────────────────────────────────────────────────────────────────
FROM python:3.11-slim

LABEL maintainer="Ilija Full_Autonomy_Edition"
LABEL description="Autonomer KI-Agent – läuft isoliert und verwaltet sich selbst"

# System-Abhängigkeiten
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    curl \
    wget \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /ilija

# Requirements zuerst (Layer-Caching)
COPY requirements.txt requirements_autonomy.txt ./
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt && \
    pip install --no-cache-dir -r requirements_autonomy.txt

# Quell-Code
COPY . .

# Verzeichnisse für persistente Daten
RUN mkdir -p skills memory/ilija_db logs data

# Berechtigungen
RUN chmod +x full_autonomy_main.py

# Volumes für persistente Daten
VOLUME ["/ilija/skills", "/ilija/memory", "/ilija/logs", "/ilija/data"]

# Web-Dashboard Port (optional)
EXPOSE 5000

# Health-Check
HEALTHCHECK --interval=60s --timeout=10s --start-period=30s --retries=3 \
    CMD python -c "import os; assert os.path.exists('logs/ilija_full_autonomy.log')" || exit 1

# Umgebungsvariablen (Defaults – überschreibe mit docker-compose oder -e)
ENV AUTONOMY_MODE=full \
    GOAL_BATCH_SIZE=3 \
    CYCLE_PAUSE_SECONDS=30 \
    MAX_ITERATIONS=50 \
    EVOLUTION_SNAPSHOT_HOURS=24 \
    WEB_INTERFACE=true \
    PYTHONUNBUFFERED=1 \
    ANONYMIZED_TELEMETRY=False \
    CHROMA_TELEMETRY=False

ENTRYPOINT ["python", "full_autonomy_main.py"]
CMD ["--provider", "auto", "--web"]
