# Incident and recovery record

This exercise uses public, de-identified data only. It records an integration
check and should be repeated locally after changes to the environment.

## Orthanc PACS unavailable to OHIF

| Field | Record |
| --- | --- |
| Symptom | OHIF displays a data-source connection error and cannot retrieve studies. |
| Root cause | The Orthanc service is stopped, so its local DICOMweb endpoint is unavailable. |
| Test | With the stack running, stop Orthanc using `docker compose stop orthanc` and refresh OHIF. |
| Recovery | Start the service using `docker compose start orthanc`. |
| Verification | `curl --fail -u pacs_lab:YOUR_LOCAL_PASSWORD http://localhost:8043/dicom-web/studies` returns successfully and the known CT study opens in OHIF. |

The failure is an HTTP/DICOMweb reachability problem, not a DICOM association
rejection: OHIF cannot establish its REST request to Orthanc at all.
