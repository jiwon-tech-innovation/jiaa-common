# JIAA Project 2 - 통합 시스템 (w/ GPT-SoVITS)

이 저장소는 Node.js 데이터 서비스, Go 코어 서비스, AI 서비스, 그리고 Electron 클라이언트 애플리케이션을 포함한 JIAA 프로젝트 2의 전체 스택 구현을 담고 있습니다.

## 📂 프로젝트 구조

- **Backend Services:**
  - **`jiaa-server-data/`**: (Node.js) Kafka 데이터 수신, InfluxDB 저장.
  - **`jiaa-server-core/`**: (Go) gRPC 메인 서버, 점수 계산, 명령 제어.
  - **`jiaa-server-ai/`**: (Python) AI 기능(Whsiper STT, Game Detection, LLM).
- **AI Models:**
  - **`GPT-SoVITS/`**: (Python) 로컬 전용 음성 합성(TTS) 서버. JIAA의 목소리를 생성합니다.
- **Client Application (`jiaa-client/`):**
  - **`dev_1/`**: Input Tracking & App Detector (Python).
  - **`dev_2/`**: Vision/Webcam Service (Python).
  - **`dev_3/`**: Electron UI Client (React/TypeScript).

---

## 🚀 실행 가이드 (Quick Start)

시스템을 실행하려면 **3개의 터미널**이 필요합니다.

### 1️⃣ 터미널 1: 백엔드 인프라 실행 (Docker)
DB, Kafka, Backend Service, AI Service를 실행합니다.

```bash
docker-compose up -d --build
```
> **확인:** `docker ps`로 모든 컨테이너(`core`, `data`, `ai`, `kafka` 등)가 `Up` 상태인지 확인하세요.

---

### 2️⃣ 터미널 2: GPT-SoVITS 실행 (Local TTS)
JIAA의 목소리를 생성하는 로컬 AI 서버를 실행합니다. (포트 9880)

```bash
cd GPT-SoVITS

# 가상환경이 있다면 activate 후 실행 (권장)
# 예: conda activate gpt-sovits

# 서버 실행 (명령어가 깁니다. 복사해서 쓰세요!)
python3 api.py \
  -s "custom_weights/xxx_e8_s120.pth" \
  -g "custom_weights/xxx-e15.ckpt" \
  -dr "custom_weights/vocal_vocal_역삼동 15.m4a.reformatted.wav_10.wav_10.wav_0000021120_0000126720.wav" \
  -dt "안녕하세요. 저는 지아입니다." \
  -dl "ko" \
  -a "127.0.0.1" \
  -p 9880
```
> **성공 시:** `http://127.0.0.1:9880`에서 서버 대기 중 로그가 뜹니다.

---

### 3️⃣ 터미널 3: 클라이언트 통합 실행 (Electron + Python Agents)
UI, 웹캠 감시, 앱 감시를 한 번에 실행합니다.

```bash
cd jiaa-client/dev_3

# 패키지 설치 (최초 1회)
npm install

# 통합 실행
npm run dev:all
```
> **팁:** `dev:all` 명령어는 내부적으로 `electron`, `webcam.py`, `app_detector.py`를 동시에 켜줍니다.

---

## 🛑 종료 방법 (Cleanup)

```bash
# 클라이언트 (터미널 3)
Ctrl + C (터미널에서 중단)

# TTS 서버 (터미널 2)
Ctrl + C

# 백엔드 (터미널 1)
docker-compose down
```

## ✅ 검증 (Verification)

1. **아바타 반응:** 웹캠 앞에서 눈을 감거나 자리를 비우면, 아바타가 **로컬 TTS 목소리**로 경고해야 합니다.
2. **게임 감지:** `Steam`이나 `LoL` 등을 켜면 `app_detector`가 감지하고 경고/종료 명령을 내려야 합니다.
