FROM ubuntu:18.04 as ubuntu1804-bazel-nojava
ENV DEBIAN_FRONTEND="noninteractive"

### Install packages required by Bazel and its tests
RUN dpkg --add-architecture i386 && \
    apt-get -qqy update && \
    echo "Installing base packages" && \
    apt-get -qqy install --no-install-recommends apt-utils curl lsb-release software-properties-common && \
    echo "Installing packages required by Bazel" && \
    apt-get -qqy install --no-install-recommends build-essential coreutils clang curl ed expect file git iproute2 iputils-ping libbz2-1.0:i386 libncurses5:i386 libssl-dev libstdc++6:i386 libz1:i386 less netcat-openbsd python python-dev python-six python3 python3-dev python3-six unzip wget xvfb zip zlib1g-dev && \
    echo "Installing packages required by C++ coverage tests" && \
    apt-get -qqy install --no-install-recommends lcov llvm && \
    apt-get -qqy purge apport && \
    rm -rf /var/lib/apt/lists/*

FROM ubuntu1804-bazel-nojava AS ubuntu1804-bazel-java11
RUN apt-get -qqy update && \
    apt-get -qqy install openjdk-11-jdk && \
    rm -rf /var/lib/apt/lists/*

### Required by rbe_autoconfig
ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk-amd64/
ENV LANG "C.UTF-8"
ENV LANGUAGE "C.UTF-8"
ENV LC_ALL "C.UTF-8"



cd /mnt
curl -Lo android-ndk.zip https://dl.google.com/android/repository/android-ndk-r15c-linux-x86_64.zip
unzip android-ndk.zip
rm android-ndk.zip
chown -R root:root /mnt/android-ndk-r15c

# Install Android SDK
mkdir -p /mnt/android-sdk-linux
cd /mnt/android-sdk-linux
curl -Lo android-sdk.zip https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
unzip android-sdk.zip
rm android-sdk.zip
yes | tools/bin/sdkmanager --licenses > /dev/null
tools/bin/sdkmanager --update
tools/bin/sdkmanager \
    "build-tools;29.0.2" \
    "emulator" \
    "extras;android;m2repository" \
    "platform-tools" \
    "platforms;android-24" \
    "platforms;android-28" \
    "system-images;android-19;default;x86" \
    "system-images;android-21;default;x86" \
    "system-images;android-22;default;x86" \
    "system-images;android-23;default;x86"
chown -R root:root /mnt/android-sdk-linux








FROM ubuntu1804-bazel-java11 AS bazel
RUN curl -Lo /usr/local/bin/bazel https://github.com/bazelbuild/bazelisk/releases/download/v1.1.0/bazelisk-linux-amd64 && \
    chown root:root /usr/local/bin/bazel && \
    chmod 0755 /usr/local/bin/bazel

