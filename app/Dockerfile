# ============================================================
# 베이스 이미지
# ============================================================
# OSRF(Open Source Robotics Foundation)에서 공식 제공하는 이미지
# jazzy-desktop-full = Ubuntu 24.04 Noble + ROS2 Jazzy + RViz2 + Gazebo 포함
FROM osrf/ros:jazzy-desktop-full

# ============================================================
# 빌드 인자 (팀원마다 다를 수 있는 값을 변수로 관리)
# ============================================================
ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=1000

# ============================================================
# 시스템 패키지 업데이트 및 개발 도구 설치
# ============================================================
RUN apt-get update && apt-get install -y \
    # 기본 개발 도구
    git \
    wget \
    curl \
    vim \
    build-essential \
    cmake \
    # Python 도구
    python3-pip \
    python3-colcon-common-extensions \
    python3-rosdep \
    python3-vcstool \
    # ROS2 공통 패키지
    ros-jazzy-navigation2 \
    ros-jazzy-nav2-bringup \
    ros-jazzy-slam-toolbox \
    ros-jazzy-ros2-control \
    ros-jazzy-ros2-controllers \
    ros-jazzy-joint-state-publisher \
    ros-jazzy-joint-state-publisher-gui \
    ros-jazzy-xacro \
    ros-jazzy-robot-state-publisher \
    # 네트워크/디버깅 도구
    iproute2 \
    iputils-ping \
    && rm -rf /var/lib/apt/lists/*   # 캐시 삭제 → 이미지 크기 감소

# ============================================================
# rosdep 초기화
# ============================================================
# rosdep: 패키지 의존성 자동 설치 도구
RUN rosdep update

# ============================================================
# 비root 사용자 생성 (보안 및 파일 권한 문제 방지)
# ============================================================
# 주의: osrf/ros:jazzy-desktop-full은 Ubuntu 24.04 베이스라 UID/GID 1000이
# 이미 기본 'ubuntu' 사용자/그룹에 할당되어 있는 경우가 많다.
# 그래서 무조건 groupadd/useradd로 새로 만들면 "GID already exists" 에러가 난다.
# → 이미 해당 UID/GID가 존재하면 이름만 바꿔서(rename) 재사용하고,
#   없으면 새로 생성하도록 분기 처리한다.
RUN if getent group $USER_GID > /dev/null 2>&1; then \
        EXISTING_GROUP=$(getent group $USER_GID | cut -d: -f1) && \
        groupmod -n $USERNAME $EXISTING_GROUP; \
    else \
        groupadd --gid $USER_GID $USERNAME; \
    fi \
    && if getent passwd $USER_UID > /dev/null 2>&1; then \
        EXISTING_USER=$(getent passwd $USER_UID | cut -d: -f1) && \
        usermod -l $USERNAME -d /home/$USERNAME -m $EXISTING_USER; \
    else \
        useradd --uid $USER_UID --gid $USER_GID -m $USERNAME; \
    fi \
    # sudo 권한 부여 (패키지 추가 설치 시 필요)
    && apt-get update && apt-get install -y sudo \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
    && rm -rf /var/lib/apt/lists/*

# ============================================================
# 작업 디렉토리 및 사용자 전환
# ============================================================
WORKDIR /ros2_ws
RUN chown -R $USERNAME:$USERNAME /ros2_ws
USER $USERNAME

# ============================================================
# ROS2 관련 환경변수 (ENV로 지정 — 어떤 방식으로 컨테이너를 실행해도
# 항상 적용되도록 함. .bashrc의 export는 대화형 셸에서만 로드되므로
# `docker run ... bash -c "..."`나 `docker exec`에서는 반영되지 않는다.)
# ============================================================
ENV ROS_DOMAIN_ID=42
ENV ROS_LOCALHOST_ONLY=0

# ============================================================
# ROS2 환경 자동 source 설정 (대화형 셸 편의용)
# ============================================================
# 컨테이너에서 터미널을 대화형으로 열 때, 매번 수동으로 source 하지 않아도 됨.
# 단, ROS_DOMAIN_ID/ROS_LOCALHOST_ONLY는 위에서 ENV로 이미 고정되어 있으므로
# 여기서는 ROS2 setup.bash만 source한다.
RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc \
    && echo "source /ros2_ws/install/setup.bash 2>/dev/null || true" >> ~/.bashrc

# ============================================================
# 컨테이너 시작 시 실행할 기본 명령
# ============================================================
# bash 셸로 진입 (대화형 개발 환경)
CMD ["/bin/bash"]
