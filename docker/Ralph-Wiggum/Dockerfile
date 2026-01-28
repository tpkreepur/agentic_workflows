# Start with the official Rocky Linux 9 base
FROM rockylinux:9

# Metadata for Production Tracking
LABEL maintainer="Enterprise DevOps"
LABEL project="Ralph Wiggum Refactoring Agent"
LABEL version="1.0.0"

# 1. System Updates & Toolchain Installation
# We install 'epel-release' to access robust versions of 'jq' and 'python3'
RUN dnf update -y && \
    dnf install -y epel-release && \
    dnf install -y \
    git \
    wget \
    unzip \
    which \
    jq \
    python3 \
    python3-pip \
    procps-ng \
    --setopt=install_weak_deps=False && \
    dnf clean all

# 2. Install OpenJDK 21 (Target Runtime)
# The 'devel' package is mandatory for compilation capabilities
RUN dnf install -y java-21-openjdk-devel

# Verify Java Installation
RUN java -version && javac -version

# 3. Install Maven 3.9.6
# Manual installation ensures consistent versioning across all agent instances
ENV MAVEN_VERSION=3.9.6
RUN wget https://archive.apache.org/dist/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz -P /tmp && \
    tar xf /tmp/apache-maven-${MAVEN_VERSION}-bin.tar.gz -C /opt && \
    ln -s /opt/apache-maven-${MAVEN_VERSION} /opt/maven && \
    rm /tmp/apache-maven-${MAVEN_VERSION}-bin.tar.gz

# Set Environment Variables
ENV JAVA_HOME=/usr/lib/jvm/java-21-openjdk
ENV M2_HOME=/opt/maven
ENV PATH=${M2_HOME}/bin:${JAVA_HOME}/bin:${PATH}

# 4. Install GitHub CLI (gh)
# We add the official repository to get the latest version supporting Copilot extensions
RUN dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo && \
    dnf install -y gh

# 5. User Security Configuration
# Create a non-root user 'ralph' to execute the loop. 
# This prevents the agent from accidentally modifying container system files.
RUN useradd -m -s /bin/bash ralph
WORKDIR /home/ralph

# 6. Dependency Pre-Loading (Optimization)
# We can pre-install common Python libs used for parsing
RUN pip3 install --no-cache-dir mdformat

# Switch to the agent user
USER ralph

# 7. Script Injection
# We assume the orchestration scripts are in a local 'scripts' directory
COPY --chown=ralph:ralph scripts/ /home/ralph/scripts/
RUN chmod +x /home/ralph/scripts/*.sh

# Entrypoint configuration
CMD ["/home/ralph/scripts/ralph_loop.sh"]
