# planeodev CI: built by .github/workflows/docker-multiarch-cicd-recommendationservice.yaml ONLY when src/recommendationservice/** (or that
# workflow) changes. Edit this comment to force a rebuild of this image without a code change.
# Copyright 2020 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# planeodev fork: python:3.10.11-slim (Debian 11 bullseye) can no longer apt-get —
# bullseye-security returns 404 (EOL). Same Python minor on Debian 12, pinned by index digest.
FROM python:3.10-slim-bookworm@sha256:f8aa74bffbe59d02f7442dba43c9ea72b2c34cc29aa7a7edc74b29521b23bb43 as base

FROM base as builder

RUN apt-get -qq update \
    && apt-get install -y --no-install-recommends \
        wget g++ \
    && rm -rf /var/lib/apt/lists/*

# download the grpc health probe
# renovate: datasource=github-releases depName=grpc-ecosystem/grpc-health-probe
ENV GRPC_HEALTH_PROBE_VERSION=v0.4.18
RUN wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe

# get packages
COPY requirements.txt .
RUN pip install -r requirements.txt

FROM base as without-grpc-health-probe-bin
# Enable unbuffered logging
ENV PYTHONUNBUFFERED=1

# get packages
WORKDIR /recommendationservice

# Grab packages from builder
COPY --from=builder /usr/local/lib/python3.10/ /usr/local/lib/python3.10/

# Add the application
COPY . .

# set listen port
ENV PORT "8080"
EXPOSE 8080

ENTRYPOINT ["python", "recommendation_server.py"]

FROM without-grpc-health-probe-bin
COPY --from=builder /bin/grpc_health_probe /bin/grpc_health_probe
USER 10002
ENV DISABLE_PROFILER 1