FROM python:3.12 AS base

RUN apt-get update && apt-get install -y \
    gettext \
    libffi8 \
    libgdk-pixbuf2.0-0 \
    liblcms2-2 \
    libopenjp2-7 \
    libssl3 \
    libtiff6 \
    libwebp7 \
    shared-mime-info \
    mime-support \
    libpq5 \
    && rm -rf /var/lib/apt/lists/*

RUN curl -sSL https://install.python-poetry.org | python3 - && \
    mv /root/.local/bin/poetry /usr/local/bin/poetry

WORKDIR /app
RUN poetry config virtualenvs.create false

RUN useradd -m saleor
#USER saleor
RUN mkdir -p /app/media /app/static 
#&& chown -R saleor:saleor /app
#WORKDIR /app
COPY pyproject.toml poetry.lock /app/
COPY . .

RUN poetry install --no-root

#RUN useradd -m saleor
#USER saleor
#WORKDIR /app

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}

RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 ./manage.py collectstatic --no-input

EXPOSE 8000

ENV PYTHONUNBUFFERED=1

CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]
