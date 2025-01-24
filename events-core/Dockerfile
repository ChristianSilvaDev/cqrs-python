# syntax=docker/dockerfile:1

ARG PYTHON_VERSION=3.12.4
FROM python:${PYTHON_VERSION}-slim as base

# Prevents Python from writing pyc files.
ENV PYTHONDONTWRITEBYTECODE=1

# Keeps Python from buffering stdout and stderr to avoid situations where
# the application crashes without emitting any logs due to buffering.
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Create a non-privileged user that the app will run under.
# See https://docs.docker.com/go/dockerfile-user-best-practices/ARG UID=10001
ARG UID=10001
RUN useradd \
    -r \
    -u "${UID}" \
    -c "" \
    -M \
    -d /nonexistent \
    -s /sbin/nologin \
    appuser

# Install poetry
RUN --mount=type=cache,target=/root/.cache/pip pip install poetry

# Deactivate virtualenv
RUN poetry config virtualenvs.create false

# Install dependencies
COPY pyproject.toml poetry.lock ./
RUN --mount=type=cache,target=/root/.cache/pip \
    poetry install --no-root --no-interaction --no-ansi

USER appuser

# Copy the source code into the container.
COPY . .

# Expose the port that the application listens on.
EXPOSE 8001

CMD ["uvicorn", "events_core.app:app", "--host=0.0.0.0", "--port=8001", "--reload"]
