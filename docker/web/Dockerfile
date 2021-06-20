# If you run this in M1, it'll assume current arch which is linux/arm64/v8. This leads to a missing /lib64/.. file.
FROM --platform=linux/amd64 python:3.9-slim

ARG APP_HOME=/app
WORKDIR ${APP_HOME}
ENV PYTHONUNBUFFERED 1

COPY requirements.txt ./
RUN set -ex \
	&& buildDeps=" \
		build-essential \
		libssl-dev \
    postgresql \
    postgresql-server-dev-11 \
    libpq-dev \
		" \
    && apt-get update \
    && apt-get install -y --no-install-recommends $buildDeps \
    && pip install --no-cache-dir -r requirements.txt \
    && apt-get purge -y --auto-remove $buildDeps \
    && rm -rf /var/lib/apt/lists/* \
    && find /usr/local \
        \( -type d -a -name test -o -name tests \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' +

COPY . .
RUN DJANGO_SETTINGS_MODULE=config.settings.local DJANGO_DOT_ENV_FILE=.env.local python manage.py collectstatic --noinput
