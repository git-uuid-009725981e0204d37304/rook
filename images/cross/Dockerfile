# Copyright 2016 The Rook Authors. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM BASEIMAGE

# install build tools, compilers and cross compilers for all supported platforms
RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yy -q --no-install-recommends \
        awscli \
        ca-certificates \
        curl \
        docker.io \
        git \
        jq \
        lsb-release \
        make \
        rsync \
        runit \
        sudo \
        zip && \
    curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    DEBIAN_FRONTEND=noninteractive apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yy -q --no-install-recommends nodejs && \
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt-get autoremove -y && \
    DEBIAN_FRONTEND=noninteractive apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# install go-lang from the official repo
RUN GO_VERSION=1.9.2 && \
    GO_HASH=de874549d9a8d8d8062be05808509c09a88a248e77ec14eb77453530829ac02b && \
    curl -fsSL https://golang.org/dl/go${GO_VERSION}.linux-amd64.tar.gz -o golang.tar.gz && \
    echo "${GO_HASH}  golang.tar.gz" | sha256sum -c - && \
    tar -C /usr/local -xzf golang.tar.gz && \
    rm golang.tar.gz
ENV PATH /usr/local/go/bin:$PATH

# precompile the go standard library for all supported platforms and configurations
# the install suffixes match those in golang.mk so please keep them in sync
RUN platforms="darwin_amd64 windows_amd64 linux_arm linux_amd64 linux_arm64" && \
    for p in $platforms; do CGO_ENABLED=0 GOOS=${p%_*} GOARCH=${p##*_} GOARM=7 go install -installsuffix static -a std; done

COPY run.sh rsyncd.sh /build/

ENTRYPOINT [ "/tini", "-g", "--", "/build/run.sh" ]
