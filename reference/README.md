# Reference — готовые решения на базе RK3588

Сборник ссылок на коммерческие продукты с тем же SoC (Rockchip RK3588),
используемые как точка отсчёта для сравнения с платформой reimagined-rk3588.

---

## Advantech — RK3588 Visual Controller (ASR-A501 + AFE-R761)

### Ссылки

| # | Название | Ссылка |
|:-:|:---------|:-------|
| 1 | Advantech Rugged Visual Controller for Edge AI Vision with RK3588 (анонс) | [advantech.com](https://www.advantech.com/en/resources/news/advantech-rugged-visual-controller-for-edge-ai-vision-with-rockchip-rk3588) |
| 2 | AFE-R761 — Rugged Visual Controller (spec page) | [advantech.com](https://www.advantech.com/en-eu/products/7978f26b-5c2e-4118-8e67-ae09ca11fc27/afe-r761/mod_be4c17f0-88c3-406d-911e-bf9ddedf3b37) |
| 3 | ASR-A501 — 4" SBC на RK3588 (категория) | [advantech.com](https://www.advantech.com/en/products/4-single-board-computer/sub_9a85a991-5c8c-4def-873a-cc6545304a6e) |

### Краткая характеристика Advantech

**ASR-A501** (плата / 4" SBC):
- RK3588: 4×Cortex-A76 @ 2.4 GHz + 4×Cortex-A55 @ 1.7 GHz
- NPU: до 6 TOPS
- GPU: Arm Mali-G610 MP4
- Память: LPDDR4 4224 MT/s, onboard 8 GB / max 32 GB
- Хранилище: 64 GB eMMC
- Камеры: 2 × MIPI-CSI 4-lane (B2B 120-pin)
- Сеть: 1×2.5 GbE + 3×GbE
- Расширение: M.2 (B/E key)
- I/O: 2×USB 3.0 Type-A, 2×CAN-FD 5M, 2×RS-232/485, 1×HDMI 2.0
- Питание: 12–24 VDC (phoenix)
- Размеры: 175 × 120 × 56.7 mm
- Температура: 0~60°C
- ОС: Linux Debian 12, Android 14
- Сертификация: CE / FCC / CCC

**AFE-R761** (комплект / rugged-компьютер на основе ASR-A501):
- Те же CPU/GPU/NPU/память/хранилище
- Камеры: **4 × GMSL** (high-speed synchronous visual perception) + 2 × MIPI-CSI
- Сеть: 1×2.5 GbE + 3×GbE, SIM (LTE)
- Расширение: M.2 (B/E/M key)
- I/O: 2×USB 3.0, 2×CAN-FD 5M, 2×RS-232/485, DIO, EtherCAT master
- Питание: 12–24 VDC, ESD 8 kV contact / 15 kV air, locking connectors
- Размеры: 175 × 120 × 56.7 mm
- Температура: **−40~70°C** (0.7 m/s airflow)
- ОС: Linux Debian 12, Android 14
- Гарантия: 10 лет
- Целевое применение: AMR/AGV, 360° panorama, robotic control, AI surveillance

---

## Сравнение: reimagined-rk3588 vs Advantech ASR-A501 / AFE-R761

| Параметр | **reimagined-rk3588** (наша платформа) | **Advantech ASR-A501 / AFE-R761** |
|----------|----------------------------------------|----------------------------------|
| Архитектура | **Модульная**: SoM + интерфейсная плата под заказ | Фиксированный форм-фактор (4" SBC / rugged box) |
| SoC | RK3588 | RK3588 |
| NPU | 6 TOPS (3 cores) | 6 TOPS |
| Память | до 32 GB LPDDR4x/LPDDR5 | до 32 GB LPDDR4 |
| Хранилище | до 256 GB eMMC + M.2 NVMe | 64 GB eMMC + M.2 |
| MIPI CSI | **до 6** (2×DCPHY 4-lane + 2×DPHY, split x2+x2) | **2** × MIPI-CSI 4-lane |
| GMSL | — (через FPGA-адаптер или PCIe) | **4 × GMSL** (AFE-R761) |
| Сеть | 2×GbE встроенные + PCIe 2.5–10 GbE | 1×2.5 GbE + 3×GbE |
| CAN | на интерфейсной плате (гибко) | 2×CAN-FD 5M (закреплено) |
| EtherCAT | — | Да (AFE-R761, master driver) |
| PCIe | 3.0 x4 (FPGA, capture cards) | M.2 (B/E/M key) |
| USB 3.0 | Да (на интерфейсной плате) | 2×Type-A |
| HDMI RX | Да (встроенный в SoC) | — |
| HDMI TX | Да | 1×HDMI 2.0 |
| Питание | определяется интерфейсной платой | 12–24 VDC (phoenix) |
| Температурный диапазон | 70–85°C (дизайн-цель) | AFE: −40~70°C, ASR: 0~60°C |
| ESD / защита | определяется дизайном | 8 kV contact / 15 kV air, locking |
| Открытость | **Полная** (открытый HW, BSP Rockchip, mainline kernel) | **Закрытая** (вендор, фиксированные DT) |
| Гибкость конфигурации | **Высокая** (интерфейсная плата под проект) | Низкая (фиксированный I/O) |
| ОС | Linux BSP (Debian/Ubuntu) + mainline | Linux Debian 12, Android 14 |
| Сертификация | — (проект на этапе разработки) | CE / FCC / CCC |
| Гарантия | — | 10 лет |
| Целевое применение | Видеонаблюдение, машинное зрение, IP-камеры, распределённые системы | AMR/AGV, роботы, 360° панорама, AI surveillance |

### Ключевые различия и выводы

**Общие черты:**
- Одна и та же SoC (RK3588), тот же NPU (6 TOPS), та же GPU (Mali-G610).

**Преимущества нашей платформы (reimagined-rk3588):**
1. **До 6 MIPI-CSI камер** против 2 у Advantech — критично для многоканального видеонаблюдения.
2. **Модульная архитектура** — SoM + интерфейсная плата под конкретный проект (от IP-камеры до видеосервера).
3. **Открытость** — полный контроль HW/SW, возможность mainline kernel, кастомные DT.
4. **HDMI RX** — захват внешнего видеосигнала без доп. оборудования.
5. **PCIe 3.0 x4** — подключение FPGA для нестандартных интерфейсов (CameraLink и др.).
6. **Расширяемость сети** — до 10 GbE через PCIe.

**Преимущества Advantech (ASR-A501 / AFE-R761):**
1. **GMSL × 4** — штатная поддержка длинных линий до 15 м (автомобильный/промышленный стандарт).
2. **Rugged** — −40°C, ESD 15 kV, locking connectors, 10-летняя гарантия.
3. **EtherCAT** — встроенный master для motion control (роботы, AMR).
4. **Готовность «из коробки»** — сертифицирован, с поддержкой, с Android 14.
5. **SIM / LTE** — встроенная связь (AFE-R761).

**Итог:** Advantech — готовое rugged-решение для робототехники и AMR с GMSL и EtherCAT.
Наша платформа — **открытый и модульный** вариант для задач видеонаблюдения,
машинного зрения и распределённых систем с упором на максимальное количество
MIPI-камер, гибкость интерфейсов и полный контроль над HW/SW.
