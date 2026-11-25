# Mental Health Care System: Requirements and Design

The requirements, architecture, data model and interface design for a digital mental health support system for an NHS Trust. It has a patient app, a medical professional dashboard and a secure backend API, designed around health data protection and clinical safety.

Built as coursework for the Foundations of Computer and IT Systems module (BSc Computer Science, University of Derby). This is a design project: it contains diagrams and wireframes, not running code.

## The system

Patients can view their records and request corrections, book appointments, complete digital forms, track mental health exercises and message their care team. Clinicians can search patients, manage records, review engagement, schedule appointments and update exercise plans.

## Architecture

| Component | Role |
| --- | --- |
| Patient app | Responsive mobile and web app |
| Professional dashboard | Browser-based dashboard for NHS desktops and tablets |
| Backend API | REST/JSON API for records, appointments, forms, messages and exercises |
| Relational database | Patients, appointments, records, forms and audit logs |
| File storage | Images, PDF scans and audio, kept separate from the database |
| Authentication service | Login, multi-factor authentication, sessions and role checks |
| Notification service | Reminders by app notification, SMS or email |
| Audit logging | Append-only record of every access, edit and failed login |

![Overall system dataflow](docs/images/07_overall_dataflow.png)

## Key design decisions

- **Patients request corrections instead of editing clinical notes**, which protects the record while respecting their right to challenge it.
- **UUID primary keys** avoid exposing how many patients the system holds.
- **NHS numbers are validated** as 10 digits with the modulus 11 check digit.
- **Validation rules** reject past appointments, patient bookings more than two months ahead, and unsupported or oversized files.
- **Access control** limits patients to their own records and clinicians to patients in their care.
- **Compliance** with UK GDPR, the Data Protection Act 2018, the NHS Data Security and Protection Toolkit and the DCB0129 clinical safety standard.

## Data model

![Server-side ERD](docs/images/08_server_side_erd.png)

## Workflows and dataflows

| Workflows | Dataflows |
| --- | --- |
| [Patient app](docs/images/01_patient_workflow.png) | [Patient app](docs/images/04_patient_dataflow.png) |
| [Professional dashboard](docs/images/02_professional_workflow.png) | [Professional dashboard](docs/images/05_professional_dataflow.png) |
| [Server side](docs/images/03_server_workflow.png) | [Server side](docs/images/06_server_dataflow.png) |

The diagram source is written in Mermaid and renders directly on GitHub: see [DIAGRAMS.md](DIAGRAMS.md).

## Interface wireframes

![Medical professional dashboard](docs/images/09_medical_professional_dashboard_wireframe.png)

![Patient app](docs/images/10_patient_app_wireframe.png)

## Dataset visualisations

| | |
| --- | --- |
| ![Engagement trend](docs/images/11_engagement_trend_line_chart.png) | ![Form risk dashboard](docs/images/12_form_response_risk_dashboard.png) |
| ![Weekly exercise completion](docs/images/13_weekly_exercise_completion_progress.png) | ![Appointment timeline](docs/images/14_appointment_reminder_timeline.png) |
