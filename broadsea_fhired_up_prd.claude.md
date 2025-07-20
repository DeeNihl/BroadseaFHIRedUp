# 📘 BroadseaFHIRedUp Product Requirements Document (PRD)

## 📌 Purpose

The **BroadseaFHIRedUp** repository extends the [OHDSI Broadsea](https://github.com/OHDSI/Broadsea) platform with the ability to ingest and export FHIR R4 resources into/from the OMOP CDM.

It introduces a new Docker Compose profile (`fhir-ingestor`) containing the **omopfhirmap** service, which enables conversion between FHIR Bundles and OMOP CDM tables. This repository serves healthcare organizations, researchers, and developers needing seamless interoperability between FHIR-based systems and OMOP.

---

## 🎯 Goals

- Provide a fork of Broadsea with a preconfigured Docker Compose profile for **omopfhirmap**.
- Enable ingestion of `FHIR R4 Bundle` files into the OMOP CDM (**demo\_cdm**) database running in Broadsea’s `atlasdb` Postgres container.
- Allow export of OMOP cohorts to FHIR R4 Bundles.
- Deliver a user-friendly experience with CLI scripts, REST API endpoints, and Swagger UI.
- Maintain compatibility with Broadsea’s `.env` and `secrets/` configuration patterns.

---

## 📦 Approach

1. **Fork Broadsea** to create a derivative repository: `BroadseaFHIRedUp`.
2. Add a new Docker Compose profile called `fhir-ingestor`:
   - Spins up an **omopfhirmap** container (Debian + OpenJDK)
   - Prebuilt JAR of omopfhirmap by default, with option to build from source.
3. Integrate REST API with Swagger UI in omopfhirmap for FHIR import/export.
4. Provide helper scripts for simplified CLI usage.
5. Include example FHIR R4 Bundle files for testing.
6. Use `.env` and `secrets/` folders for configuration consistency with Broadsea.
7. Publish the omopfhirmap Docker image to GitHub Container Registry (GHCR).

---

## 📋 Step-by-Step Setup Guide

### 🏁 Prerequisites

- Docker and Docker Compose installed.
- Access to a running Broadsea stack (Atlas, WebAPI, Postgres).
- A GitHub account with access to `BroadseaFHIRedUp`.

---

### 🔥 Fork and Clone Repository

```bash
git clone https://github.com/DeeNihl/BroadseaFHIRedUp.git
cd BroadseaFHIRedUp
```

---

### ⚙️ Configure Environment

1. Edit the `.env` file (or use provided defaults):

```dotenv
DB_HOST=atlasdb
DB_PORT=5432
DB_NAME=demo_cdm
DB_USER=ohdsi_admin
DB_PASSWORD=yourpassword
OMOPFHIRMAP_IMAGE_FROM_GIT=false
```

2. Place any sensitive credentials in `/secrets/` if needed.

---

### 🐳 Start Broadsea Stack with FHIR Ingestor

Start Broadsea with the new profile:

```bash
docker compose --profile fhir-ingestor up -d
```

This will:

- Start Atlas, WebAPI, Postgres (`atlasdb`)
- Start omopfhirmap REST API container

---

### 🌐 Access Swagger UI

Open your browser to:

```
http://localhost:8080/swagger-ui
```

Explore API endpoints:

- `POST /tofhirbundle` – Export OMOP cohort to FHIR Bundle
- `POST /toomop` – Import FHIR Bundle to OMOP CDM
- `GET /health` – Check service health

---

### 📥 Ingest Example FHIR R4 Bundle

An example bundle is provided:

```bash
./docker/run-fhir-ingestor.sh toomop /data/fhir/examples/lab-result-bundle.json 5678
```

This imports the FHIR Bundle into OMOP’s `demo_cdm` database and creates cohort `5678` in Atlas.

---

### 📤 Export OMOP Cohort to FHIR Bundle

```bash
./docker/run-fhir-ingestor.sh tofhirbundle 101 /data/fhir/output-bundle.json
```

This exports cohort `101` from Atlas to a FHIR Bundle.

---

### 🗂 Folder Structure

```
BroadseaFHIRedUp/
├── docker/
│   └── omopfhirmap/
│       ├── Dockerfile
│       ├── application.properties
│       └── docker-entrypoint.sh
├── data/
│   └── fhir/
│       └── examples/
│           └── lab-result-bundle.json
├── secrets/
│   └── README.md
├── .env
├── README.md
└── docker-compose.yml
```

---

## ✅ Deliverables

- Broadsea fork with `fhir-ingestor` profile
- REST API + Swagger UI for FHIR import/export
- Helper scripts for CLI usage
- Example FHIR Bundles for testing
- Preconfigured `.env` and `secrets/` support
- GHCR auto-build for omopfhirmap container

---

## 📌 Notes

- The omopfhirmap container connects directly to Broadsea’s `atlasdb` Postgres.
- Uses OMOP CDM database: `demo_cdm` by default.
- Supports FHIR R4 Bundles only.

---

## 🔗 References

- [OHDSI Broadsea](https://github.com/OHDSI/Broadsea)
- [omopfhirmap](https://github.com/dermatologist/omopfhirmap)
- [HL7 FHIR to OMOP IG](https://build.fhir.org/ig/HL7/fhir-omop-ig/)

