# PACS/DICOM Lab

Hands-on personal project focused on PACS administration, DICOM workflows,
DICOMweb integration, and medical-image viewing.

## Objective

Build and validate a local medical-imaging environment to gain practical
experience with PACS deployment, DICOM metadata, DICOMweb queries, and
clinical image viewing.

## Architecture

```text
Public de-identified DICOM data
            ↓
     Orthanc PACS
            ↓
        DICOMweb
            ↓
       OHIF Viewer
```

## Technologies

- Docker Desktop
- Orthanc PACS
- DICOM
- DICOMweb
- OHIF Viewer
- The Cancer Imaging Archive (TCIA)

## Reproducible local deployment

The repository includes a complete Docker Compose environment. Orthanc provides
PACS, REST and DICOMweb services and loads its official OHIF plugin with the
DICOMweb data source. The service exposes no ports to the local network: they
are explicitly bound to `127.0.0.1`. HTTP authentication is required, and its
local password is supplied outside version control.

Before starting the stack, create a local secret file from the template and
replace its placeholder with a long, unique password:

```sh
cp .env.example .env
```

```sh
docker compose up -d
docker compose ps
```

Open Orthanc at <http://localhost:8043/ui/> and the integrated OHIF viewer at
<http://localhost:8043/ohif/>; sign in as `pacs_lab` with the password in your
local `.env`. The Orthanc database is persisted in `orthanc-db/` and ignored by
Git. It starts empty; use only public, de-identified DICOM data to reproduce
the study-loading exercise.

To stop the environment while retaining local data:

```sh
docker compose down
```

## Key activities

- Deployed a local Orthanc PACS using Docker.
- Configured persistent storage for Orthanc data.
- Loaded and inspected a public, de-identified chest CT study.
- Reviewed DICOM metadata at study and series level.
- Verified the DICOM hierarchy: Patient → Study → Series → Instance.
- Queried studies through the DICOMweb API using QIDO-RS.
- Integrated Orthanc with OHIF Viewer to navigate CT slices in a web-based
  clinical viewer.
- Simulated a PACS outage, identified the data-source connection error in
  OHIF, and recovered the service.

## Validation

The loaded CT study contained:

- Modality: CT
- Body part examined: CHEST
- Series: `A1_Harmonized_3mGy_IR`
- Instances: 343

DICOMweb availability was verified with:

```bash
curl --fail -u pacs_lab:YOUR_LOCAL_PASSWORD http://localhost:8043/dicom-web/studies
```

## Simulated incident

| Stage | Result |
|---|---|
| PACS service stopped | OHIF displayed a Data Source Connection Error |
| Diagnosis | Orthanc was not running in Docker Compose |
| Recovery | Orthanc was restarted with `docker compose start orthanc` |
| Verification | The CT study opened correctly in OHIF |

The expanded [incident and recovery record](docs/incident-recovery.md)
separates the observed symptom, root cause, test, remediation and verification.

## Screenshots

![Orthanc with a loaded CT study](screenshots/orthanc-ct-chest.png)

![CT study displayed in OHIF Viewer](screenshots/ohif-ct-chest.png)

## Data privacy

Only public, de-identified DICOM data were used. No patient data, DICOM files,
credentials, or PACS databases are included in this repository.

## Data source and references

- [The Cancer Imaging Archive (TCIA)](https://www.cancerimagingarchive.net/)
- [Orthanc](https://www.orthanc-server.com/)
- [OHIF Viewer](https://ohif.org/)

## Scope

This project is a local learning lab and is not intended for clinical or
production use.
