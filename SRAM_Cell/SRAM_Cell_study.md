# 6T SRAM Cell 설계 및 DC 특성 분석

## 1. 설계 목적

본 설계에서는 CMOS 기반의 **6T SRAM(6-Transistor SRAM) Cell**을 구성하고, DC Simulation을 통해 SRAM Cell의 저장 안정성을 확인하였다.

SRAM Cell은 두 개의 Cross-Coupled Inverter를 이용하여 `Q`와 `Q'`에 상보적인 데이터를 저장하며, 두 개의 Access NMOS를 통해 Bit Line인 `BL`, `BL'`과 연결된다.

본 설계에서는 단순히 SRAM Cell의 동작 여부를 확인하는 것뿐만 아니라, **MOS Size에 따른 Pull-Up, Pull-Down 및 Access Transistor의 구동력 변화가 SRAM의 Noise Margin과 안정성에 미치는 영향**을 고려하였다.

---

## 2. 6T SRAM Cell 구조

6T SRAM Cell은 총 6개의 MOSFET으로 구성된다.

- PMOS × 2 : Pull-Up Transistor
- NMOS × 2 : Pull-Down Transistor
- NMOS × 2 : Access Transistor

기본적인 구조는 다음과 같다.

`BL ─ Access NMOS ─ Q ─ Cross-Coupled Inverter ─ Q' ─ Access NMOS ─ BL'`

두 Inverter의 출력은 서로 반대편 Inverter의 입력으로 연결되어 **Positive Feedback 구조**를 형성한다.

따라서 정상적인 저장 상태에서는

- `Q = 1`, `Q' = 0`
- `Q = 0`, `Q' = 1`

중 하나의 상태를 안정적으로 유지할 수 있다.

Access NMOS의 Gate는 `WL(Word Line)`에 연결되어 있으며, WL의 상태에 따라 SRAM Cell과 Bit Line 사이의 연결 여부가 결정된다.

---

## 3. SRAM Cell Schematic

설계된 SRAM Cell은 두 개의 Cross-Coupled CMOS Inverter와 두 개의 Access NMOS로 구성하였다.

각 Inverter의 출력인 `Q`와 `Q'`을 상대편 Inverter의 Gate에 연결하여 저장을 위한 Feedback 구조를 구현하였다.

Access NMOS는 각각 `BL-Q`, `BL'-Q'` 사이에 배치하고, 두 Access NMOS의 Gate를 공통 `WL`에 연결하였다.

본 설계에서 사용한 주요 Node는 다음과 같다.

| Node | 역할 |
|---|---|
| VDD | SRAM Cell 전원 |
| VSS | Ground |
| WL | Word Line |
| BL | Bit Line |
| BL' | Complementary Bit Line |
| Q | Storage Node |
| Q' | Complementary Storage Node |

---

## 4. SRAM Cell Layout

Schematic을 기반으로 6T SRAM Cell의 Layout을 구성하였다.

Layout에서는 SRAM Cell의 면적을 감소시키는 동시에 Cross-Coupled Inverter 및 Access Transistor 사이의 연결이 효율적으로 이루어지도록 배치하였다.

특히 SRAM은 Memory Array에서 동일한 Cell이 반복적으로 배치되기 때문에 단일 Cell의 면적과 배선 구조가 전체 Memory Density에 큰 영향을 미친다.

따라서 Layout 설계 시 다음 요소를 고려하였다.

- PMOS/NMOS의 효율적인 배치
- VDD/VSS Routing
- Q/Q' Feedback Routing
- BL/BL'의 수직 방향 Routing
- WL의 공통 연결
- Contact 및 Via 배치
- Cell Area 최소화
- 반복적인 SRAM Array 배치를 고려한 구조

---

## 5. DC Simulation을 통한 SRAM 안정성 분석

SRAM Cell의 안정성을 확인하기 위해 DC Sweep Simulation을 수행하였다.

Cross-Coupled Inverter의 특성을 확인하기 위해 `Q`와 `Q'`의 Voltage Transfer Characteristic을 분석하였다.

Simulation 결과 두 Storage Node가 서로 반대되는 값을 가지며 Cross-Coupled 구조에 의해 안정적인 상태를 형성하는 것을 확인할 수 있었다.

SRAM의 두 Inverter는 서로 Positive Feedback을 형성하기 때문에 한쪽 Node의 전압이 증가하면 반대쪽 Node의 전압은 감소하는 형태의 특성을 나타낸다.

---

## 6. Butterfly Curve

SRAM Cell의 안정성을 평가하기 위해 두 Cross-Coupled Inverter의 VTC를 서로 겹쳐 **Butterfly Curve**를 확인하였다.

Butterfly Curve는 SRAM Cell이 외부 Noise에 의해 저장된 Data가 반전되기 전까지 어느 정도의 Noise를 견딜 수 있는지를 평가하는 데 사용된다.

두 Inverter의 Transfer Characteristic을 서로 반전하여 겹치면 Butterfly 형태의 곡선이 형성된다.

Butterfly Curve에서 형성되는 두 영역은 SRAM의 두 안정적인 저장 상태와 관련된다.

- `Q = 1`, `Q' = 0`
- `Q = 0`, `Q' = 1`

두 곡선 사이의 여유가 클수록 SRAM Cell의 저장 안정성이 높은 것으로 해석할 수 있다.

---

## 7. Static Noise Margin (SNM)

SRAM Cell의 안정성을 정량적으로 평가하는 대표적인 지표가 **Static Noise Margin(SNM)**이다.

SNM은 SRAM Cell이 저장된 Data를 유지하면서 견딜 수 있는 최대 DC Noise의 크기를 의미한다.

Butterfly Curve에서는 두 VTC 사이에 들어갈 수 있는 **최대 정사각형의 한 변의 길이**를 이용하여 SNM을 정의할 수 있다.

즉,

> **SNM이 클수록 SRAM Cell이 외부 Noise에 의해 Data가 반전될 가능성이 낮으며, 저장 안정성이 높다.**

따라서 SRAM 설계에서는 단순히 `Q`와 `Q'`이 정상적으로 저장되는지만 확인하는 것이 아니라 충분한 SNM을 확보하는 것이 중요하다.

---

## 8. MOS Size와 SRAM Margin의 관계

SRAM Cell의 안정성은 각 MOSFET의 Size에 크게 영향을 받는다.

MOSFET의 구동 능력은 1차적으로 다음과 같이 표현할 수 있다.

β = μ · Cₒₓ · (W/L)

따라서 동일한 공정과 Channel Length 조건에서는 Width `W`가 증가할수록 해당 MOSFET의 구동 능력이 증가한다.

6T SRAM에서는 세 종류의 Transistor 사이의 상대적인 구동력 관계가 중요하다.

- Pull-Up PMOS
- Pull-Down NMOS
- Access NMOS

따라서 SRAM Cell에서는 모든 MOS를 동일한 Size로 설정하기보다는 **Read Stability와 Write Ability를 고려하여 MOS Size Ratio를 결정해야 한다.**

---

## 9. Read Margin과 MOS Size

Read 동작에서는 `WL`이 활성화되면서 Access NMOS가 ON 상태가 되고 Storage Node가 Bit Line과 연결된다.

이때 저장된 Low Node의 전압이 Access NMOS를 통해 상승할 수 있다.

Low Node의 전압이 지나치게 상승하면 Cross-Coupled Inverter의 Switching Point를 넘어 저장된 Data가 반전될 위험이 있다.

따라서 Read Stability를 확보하기 위해서는 일반적으로 **Pull-Down NMOS가 Access NMOS에 비해 충분한 구동력을 가지도록 설계하는 것이 중요하다.**

이를 Cell Ratio 관점에서 표현하면 다음과 같이 생각할 수 있다.

Cell Ratio ≈ β(Pull-Down NMOS) / β(Access NMOS)

Pull-Down NMOS의 구동력이 충분하면 Read 동작 중 Low Storage Node가 VSS 방향으로 안정적으로 유지될 수 있으므로 Read Margin 확보에 유리하다.

즉,

**Pull-Down Strength ↑ → Read Stability ↑ → Read Margin 확보에 유리**

---

## 10. Write Margin과 MOS Size

Write 동작에서는 Access NMOS를 통해 Bit Line의 새로운 Data를 Storage Node에 강제로 전달하여 기존 SRAM 상태를 반전시켜야 한다.

따라서 Access NMOS가 지나치게 약하거나 Pull-Up PMOS가 지나치게 강하면 Storage Node의 기존 High 상태를 Low로 변경하기 어려워질 수 있다.

Write 동작을 원활하게 하기 위해서는 Access NMOS가 Pull-Up PMOS에 대해 충분한 구동력을 가져야 한다.

즉,

**Access Strength ↑ → Write Ability ↑**

반대로,

**Pull-Up PMOS Strength ↑ → 기존 High 상태 유지 능력 ↑ → Write 동작은 어려워질 수 있음**

따라서 Write Margin을 확보하기 위해서는 Access NMOS와 Pull-Up PMOS 사이의 적절한 Size Ratio 설정이 필요하다.

---

## 11. Read Margin과 Write Margin의 Trade-Off

6T SRAM에서 MOS Size를 결정할 때 중요한 점은 **Read Stability와 Write Ability 사이에 Trade-Off가 존재한다는 것**이다.

Read Stability를 높이기 위해서는 Pull-Down NMOS를 상대적으로 강하게 설계하는 것이 유리하다.

반면 Write Ability를 높이기 위해서는 Access NMOS가 Pull-Up PMOS에 비해 충분히 강해야 한다.

따라서 SRAM Cell의 Transistor Size는 단순히 모든 MOS의 Width를 증가시키는 방식으로 결정할 수 없으며,

**Pull-Up PMOS / Access NMOS / Pull-Down NMOS의 상대적인 구동력 비율을 고려하여 최적화해야 한다.**

이를 간단히 정리하면 다음과 같다.

| MOS Size 관계 | 주요 영향 |
|---|---|
| Pull-Down NMOS ↑ | Read Stability 향상 |
| Access NMOS ↑ | Write Ability 향상 |
| Pull-Up PMOS ↑ | Data Hold 능력 증가, Write 동작에는 불리할 수 있음 |
| Pull-Down / Access Ratio | Read Margin에 영향 |
| Access / Pull-Up Ratio | Write Margin에 영향 |

---

## 12. Simulation 결과 해석

DC Simulation 결과 Cross-Coupled Inverter의 VTC와 Butterfly 특성을 통해 SRAM Cell의 두 안정 상태를 확인할 수 있었다.

그러나 SRAM Cell의 동작 여부만으로 안정적인 SRAM 설계라고 판단하기에는 부족하다.

실제 SRAM Cell에서는 MOS Size 변화에 따라 각 Transistor의 구동력이 변화하며, 이에 따라 Butterfly Curve의 형태와 Noise Margin 또한 달라질 수 있다.

따라서 현재 Simulation 결과를 기반으로 다음 단계에서는 Pull-Up, Pull-Down 및 Access Transistor의 Width를 Sweep하여 MOS Size Ratio에 따른 Margin 변화를 비교할 필요가 있다.

특히 다음 항목을 비교하는 것이 중요하다.

1. Hold 상태의 SNM
2. Read 동작 시 Read SNM
3. Write 동작 시 Write Margin
4. Pull-Down / Access Transistor Size Ratio
5. Access / Pull-Up Transistor Size Ratio

이를 통해 단순히 정상적으로 동작하는 SRAM Cell이 아니라 **Read와 Write 동작 모두에서 충분한 Margin을 확보할 수 있는 MOS Size 조건을 결정할 수 있다.**

---

## 13. 결론

본 설계에서는 6개의 MOSFET을 이용하여 6T SRAM Cell의 Schematic 및 Layout을 구성하고, DC Simulation을 통해 Cross-Coupled Inverter의 특성을 확인하였다.

Simulation에서 `Q`와 `Q'`의 상보적인 특성과 Butterfly 형태의 Transfer Characteristic을 확인함으로써 SRAM Cell의 저장 동작과 안정성을 분석할 수 있었다.

SRAM Cell의 안정성은 단순히 회로의 정상 동작 여부뿐만 아니라 **MOS Size에 따른 각 Transistor의 상대적인 구동력과 Noise Margin을 함께 고려해야 한다.**

특히,

**Pull-Down NMOS ↔ Access NMOS → Read Margin**

**Access NMOS ↔ Pull-Up PMOS → Write Margin**

의 관계가 중요하다.

따라서 향후 MOS Width Sweep을 통해 Pull-Up, Pull-Down 및 Access Transistor의 Size Ratio를 변화시키고, 각 조건에서 Butterfly Curve와 SNM 및 Write Margin을 비교함으로써 SRAM Cell의 최적 MOS Size를 결정할 필요가 있다.

> **최종적으로 SRAM Cell 설계에서는 최소 면적만을 목표로 하기보다 Read Stability, Write Ability 및 Noise Margin을 동시에 만족할 수 있는 MOS Size Ratio를 선정하는 것이 중요하다.**
