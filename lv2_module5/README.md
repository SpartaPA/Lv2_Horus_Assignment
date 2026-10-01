비전 기반 객체 추적 시스템 실행 가이드.

작성자가 아닌 팀원이 **이 문서만 보고 실행할 수 있어야** 한다 (문제 5 필수 항목). 경로 · 명령은 실제로 돌린 것을 붙여넣는다.

## 환경

| 항목 | 값 |
|---|---|
| 인지 PC OS | <TBD> |
| 인지 PC ROS 2 | <TBD> |
| Raspberry Pi OS | <TBD> |
| Raspberry Pi ROS 2 | <TBD> |
| OpenCV | <TBD> |
| `ROS_DOMAIN_ID` | <TBD> |
| RMW 구현 | <TBD> |

> 발제에 환경 스펙이 두 판 있다. 운영 공지로 확정한 쪽을 적고, 나머지는 지운다.

## 장비

| 항목 | 값 |
|---|---|
| 카메라 모델 | <TBD> |
| 카메라 시리얼 · 펌웨어 · SDK · ROS wrapper | <TBD> |
| 사용 프로파일 (해상도 · FPS · encoding) | <TBD> |
| 모터 모델 | <TBD> |
| 모터 ID | <TBD> |
| baud · 프로토콜 | <TBD> |
| 제어 모드 (Position / Velocity) | <TBD> |
| 회전 범위 | <TBD> |
| 속도 상한 | <TBD> |
| OpenCR 포트 | <TBD> |

값은 **실제 장비에서 읽은 것**만 적는다. 다른 팀 값 복사 금지.

## 대상

| 항목 | 값 |
|---|---|
| 목표물 | 파란 퍽 · 30×30×60 mm 직육면체 |
| 고정 자세 | <TBD — 세운 면 / 눕힌 면. 면적비가 2배 차이난다> |
| 촬영 거리 | <TBD> |
| 배경 · 조명 조건 | <TBD> |

## 설치 · 빌드

    # 인지 PC
    cd lv2_module5/ros2_ws
    colcon build --symlink-install
    source install/setup.bash

## OpenCR 업로드 (Raspberry Pi, SSH)

    ssh <user>@<pi-host>
    # 빌드 · 업로드 명령

업로드 중에는 시리얼 모니터를 닫는다. 제어 프로그램과 같은 포트를 동시에 점유하면 실패한다.

## 실행

순서를 지킨다.

    # 1. 카메라
    # 2. 인지 노드
    # 3. 제어 노드

순서를 지켜야 하는 이유: <TBD>

## 중지

    # 정상 중지

비상 정지: <TBD — 전원 차단 절차>

## 설정

| 파일 | 바꾸는 값 |
|---|---|
| `config/vision.yaml` | HSV 범위, 최소 면적, 해상도 |
| `config/control.yaml` | Kp, direction, speed_limit, 회전 범위, 데드밴드 |
| `config/safety.yaml` | 입력 타임아웃, 복귀 프레임 수, 상태 유예 |

## 인터페이스

| 항목 | 규약 |
|---|---|
| 목표 토픽 | `/target` · `geometry_msgs/msg/PointStamped` |
| `point.x` / `point.y` | 정규화 중심 오차 `ex` / `ey` (오른쪽 · 아래쪽 양수) |
| `point.z` | 면적비. **`z=0` = 미검출** — 이때 x · y로 제어하지 않는다 |
| `header.stamp` | 원본 영상 시각. 촬영 시각을 모르면 "영상 수신 시각"임을 명시 |
| 발행 | 영상 처리마다. 정상 영상의 미검출도 `z=0`으로 발행 |
| QoS | best-effort, depth 1 |
| 상태 토픽 | `/tracking_status` · 값: `IDLE` `TRACKING` `LOST` |
| 모터 명령 | <TBD — 토픽 · 타입 · 단위 · 부호 · 주기 · 정지 명령> |
| 입력 타임아웃 | 0.5 s |

    ex = (cx - W/2) / (W/2)
    ey = (cy - H/2) / (H/2)
    area_ratio = contour_area / (W * H)
    command = clamp(direction * Kp * ex, -speed_limit, +speed_limit)

## bag 재현

**실제 모터 출력을 비활성한 상태로** 재생한다.

    # 기록
    # 재생 (입력 재처리 — 저장된 /target 과 섞지 않도록 remap)
    # 재생 (결과 재분석)

시간 기준이 필요하면 `--clock`과 관련 노드의 `use_sim_time`을 함께 적용한다. bag 시각과 현재 벽시계를 섞어 타임아웃 · 지연을 계산하지 않는다.

## 결과 위치

| 내용 | 경로 |
|---|---|
| 검출 · 판정 이미지 | `results/images/` |
| 원본 CSV · 상태 로그 | `results/logs/` |
| 비교 그래프 | `results/plots/` |
| 회차별 성능표 | `results/metrics.csv` |
| bag · 영상 | `recordings/README.md` |

## 다른 팀원의 실행 확인

문제 5 필수 항목. 작성자가 아닌 사람이 이 문서만 보고 실행한 기록.

| 확인자 | 날짜 | 기준 커밋 | 결과 | 수정한 누락 항목 |
|---|---|---|---|---|
| | | | | |

## 알려진 문제

-
