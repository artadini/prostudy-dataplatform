# Prostudy Learning-Data API (Test Environment)

Local HTTP server that provides the data for the assignment. It simulates an internal API that brings together two source systems:

| Source | Endpoints | Timestamps |
|---|---|---|
| CRM | `/participants`, `/enrollments` | Local time Europe/Berlin, **without** offset (`2026-03-04T10:15:00`) |
| LXP (learning platform) | `/progress-events`, `/survey-responses` | UTC with `Z` suffix (`2026-03-04T09:15:00Z`) |
| Course catalog | `/trainings` | – |

## Getting Started

Requirement: Python ≥ 3.9. No additional packages required.

```bash
cd api
python server.py            # runs on http://127.0.0.1:8000
python server.py --port 9000
```

Alternatively, using Docker:

```bash
docker build -t prostudy-learning-api ./api
docker run --rm -p 8000:8000 prostudy-learning-api
```

Interactive documentation (Swagger UI): <http://127.0.0.1:8000/docs>  
OpenAPI specification: <http://127.0.0.1:8000/openapi.json>

## Authentication

```bash
curl -X POST http://127.0.0.1:8000/login \
     -d "username=prostudy" -d "password=learn-data-2026"
# → {"access_token": "…", "token_type": "bearer", "expires_in": 3600}

curl http://127.0.0.1:8000/trainings -H "Authorization: Bearer <token>"
```

A JSON body (`{"username": …, "password": …}`) also works. The token is valid for 60 minutes.

## Pagination

All list endpoints except `/trainings` return:

```json
{
  "data": [ … ],
  "pagination": {
    "page_size": 100,
    "returned": 100,
    "total_count": 658,
    "next_token": "MTAwOmE3YjJj…"
  }
}
```

To request the next page, pass the value from `next_token` as the `page_token` query parameter. `next_token = null` means it is the last page. `page_size` may be at most 500. A `page_token` is only valid for the combination of endpoint, filters, and `page_size` with which it was created.

## Filters for Incremental Loads

| Endpoint | Parameter | Semantics |
|---|---|---|
| `/participants`, `/enrollments` | `modified_after` | `modified_at >= value` (local time; offsets/`Z` are converted) |
| `/progress-events` | `since`, `until` | `since <= event_time < until` (UTC) |
| `/survey-responses` | `since` | `submitted_at >= value` (UTC) |

Additional filters: `training_id`, `participant_id` (enrollments), `enrollment_id` (events, feedback), `survey_type` (feedback).

## Behavior You Should Know About

* The API returns `503 Service Unavailable` for approximately 4% of requests (header `Retry-After: 1`). A robust client retries the request. For debugging, this can be disabled: `API_FLAKY=0 python server.py`.
* Expired or missing tokens return `401`.
* Invalid parameters return `400`/`422` with an explanation in the `detail` field.
* The data is a fixed snapshot (as of 01.09.2026). The server does not change it; restarting restores the original state.

## Source Data Model (Summary)

* **Training**: `training_id`, `training_name`, `track`, `variant` (full-time / part-time / work-adjacent), `duration_weeks`, `list_price_eur`, `modules[]` (`module_id`, `module_order`, `module_name`, `estimated_hours`)
* **Participant**: `participant_id`, name, `email`, `birth_date`, `city`, `federal_state`, `funding_type`, `acquisition_channel`, `created_at`, `modified_at`
* **Enrollment**: `enrollment_id`, `participant_id`, `training_id`, `cohort_start_date`, `planned_end_date`, `status` (registered / active / paused / completed / dropped out / cancelled), `status_changed_at`, `created_at`, `modified_at`
* **ProgressEvent**: `event_id`, `enrollment_id`, `module_id`, `event_type` (module_started / exercise_submitted / quiz_passed / module_completed), `event_time`
* **SurveyResponse**: `response_id`, `enrollment_id`, `survey_type` (weekly feedback / completion feedback), `survey_week`, `question_key`, `answer_value` (always a string), `submitted_at`

Questions in the weekly feedback: `zufriedenheit_gesamt` (overall satisfaction, 1–5, 5 = very satisfied), `tempo` (pace, 1–5, 1 = much too slow, 3 = appropriate, 5 = much too fast), `freitext` (free text). In the completion feedback: `nps` (0–10), `zufriedenheit_coach` (coach satisfaction, 1–5), `weiterempfehlung_grund` (reason for recommendation, text).