# project-name

## 1. Jetson Nano와 Arduino를 이용한 미세먼지 측정

Jetson Nano와 Arduino를 사용하여 미세먼지 센서를 제어하고 데이터를 수집합니다.

### 1-1. Arduino 코드 작성

아래 코드를 Arduino IDE에 복사하여 사용합니다. (오류 발생 시 한글 주석을 영어로 변경하세요.)

```cpp
int pin = 8;
unsigned long duration;
unsigned long starttime;
unsigned long sampletime_ms = 30000;  // 30초 동안 샘플링
unsigned long lowpulseoccupancy = 0;
float ratio = 0;
float concentration = 0;

void setup()
{
    Serial.begin(9000);
    pinMode(pin, INPUT);
    starttime = millis();
    Serial.println("미세먼지 측정을 시작합니다...");
    Serial.println("==============================");
}

void loop()
{
    duration = pulseIn(pin, LOW);
    lowpulseoccupancy += duration;

    if ((millis() - starttime) > sampletime_ms)  // 30초마다 측정
    {
        ratio = lowpulseoccupancy / (sampletime_ms * 10.0);
        concentration = 1.1 * pow(ratio, 3) - 3.8 * pow(ratio, 2) + 520 * ratio + 0.62; // 단위: ug/m3

        Serial.println("==============================");
        Serial.print("미세먼지 농도: ");
        Serial.print(concentration);
        Serial.println(" ug/m3");

        // 대기질 상태 표시
        Serial.print("대기질 상태: ");
        if (concentration <= 30) {
            Serial.println("좋음");
        }
        else if (concentration <= 80) {
            Serial.println("보통");
        }
        else if (concentration <= 150) {
            Serial.println("나쁨");
        }
        else {
            Serial.println("매우 나쁨");
        }

        Serial.println("------------------------------");
        Serial.print("측정 시간: ");
        Serial.print(millis() / 1000);
        Serial.println("초");
        Serial.println("==============================\n");

        // 다음 측정을 위한 초기화
        lowpulseoccupancy = 0;
        starttime = millis();
    }
}
```

### 1-2. 설명

- **핀 설정**: 미세먼지 센서의 신호 핀을 Arduino의 8번 핀에 연결합니다.
- **샘플링 시간**: 30초 동안 데이터를 수집하여 평균 농도를 계산합니다.
- **농도 계산**: 수집된 데이터를 바탕으로 미세먼지 농도를 계산합니다.
- **대기질 상태**: 계산된 농도에 따라 대기질 상태를 출력합니다.

### 1-3. 주의 사항

- 코드에서 한글 주석이나 문자열로 인해 오류가 발생할 경우, 한글을 영어로 변경하여 재시도합니다.
- Arduino와 Jetson Nano 간의 시리얼 통신 설정에 유의합니다.

---
