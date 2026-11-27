# OhMio

> **Tu presupuesto energético, en una sola pantalla.**

OhMio es una app iOS que ayuda al hogar mexicano a entender, prever y reducir su consumo eléctrico **antes** de que llegue el recibo de CFE. Traduce el consumo en una métrica simple, te da **una sola acción al día** y te dice cuándo conviene usar la electricidad, todo **on-device**, sin internet y sin que tus datos salgan del iPhone.

Proyecto desarrollado para el **Swift Changemakers Hackathon 2026** (Human-Centered AI).

**Presentación:** [Ver en Canva](#) <!-- reemplaza con tu liga -->
**Demo:** [Ver video](#) <!-- opcional -->

---

## El reto

> ¿Cómo podemos crear aplicaciones que mejoren la eficiencia, el ahorro energético y la huella de carbono de las personas?

## El problema

México ya tiene programas de ahorro energético (CONUEE, FIDE, PAESE, Programa ASI de CFE), pero el cambio de hábitos en el hogar sigue sin llegar:

- Cerca del **88 %** de los puntos de luz en viviendas siguen sin ser LED.
- Se estima que alrededor del **30 %** de la energía se desperdicia.
- El recibo de CFE tiene más de 20 conceptos técnicos que casi nadie entiende.
- La tarifa **DAC** (Doméstico de Alto Consumo) puede encarecer el kWh de forma importante, y casi nadie sabe cuánto le falta para llegar a ella.
- El dolor llega **dos meses después**, cuando ya no recuerdas qué lo causó.

Las apps ambientales existentes le hablan a la consciencia del usuario. El mexicano promedio no abre una app por culpa, la abre por su bolsillo.

## La solución

OhMio convierte el recibo en acción:

| Qué hace | Cómo |
|---|---|
| **Entiende tu recibo** | OCR on-device con Vision: escaneas el recibo y la app extrae kWh, periodo y tarifa |
| **Te muestra tu margen** | Cuántos kWh te quedan antes del umbral DAC, con semáforo verde / ámbar / rojo |
| **Un consejo al día** | Un solo aparato, una sola acción, con ahorro en kWh, kg de CO₂ y pesos |
| **Lo explica en tu idioma** | Narrativa en español mexicano generada por Foundation Models (IA on-device) |
| **Te dice cuándo** | Mapa de 24 h con las horas en que la red está más limpia o en pico de demanda |
| **Privacidad total** | Todo corre en el dispositivo. Sin servidores, sin cuentas |

## Pantallas

| Pestaña | Contenido |
|---|---|
| **Inicio** | Margen antes del DAC, consejo del día y estado de la red |
| **Consejos** | Recomendaciones por horario y por aparato, rankeadas por impacto |
| **Red** | Mapa de calor de 24 h de intensidad de carbono / demanda |
| **Perfil** | Tarifa, región, aparatos y recibos escaneados |

>  *Agrega aquí capturas de pantalla en `docs/screenshots/`.*

##  IA y Human-Centered AI

- **Vision** — OCR del recibo CFE, on-device.
- **Foundation Models** — narrativa contextual en español mexicano.
  - Recibe una **decisión ya tomada**, nunca números crudos para calcular. Solo narra.
  - Timeout de 2 s con **fallback determinista**: la app nunca se queda sin respuesta.
- **Cálculo determinista** — el margen DAC y el ranking de aparatos son matemática, no IA.

Esta separación hace el sistema **interpretable y confiable**: la IA explica, no decide ni inventa cifras. Además baja la carga cognitiva (una métrica, una acción) y funciona sin conexión.

##  Arquitectura

MVVM con `@Observable`, servicios sin estado y persistencia local.

```
OhmMio/
├── App/          # Entrada de la app, RootView, MainTabView
├── Models/       # User, Receipt, Appliance, DACMargin, Tariff, Region, CarbonIntensity, ActionDecision
├── Services/     # ReceiptScanner (Vision), DACCalculator, ApplianceRanker,
│                 # PriorityAgent, NarrativeService (Foundation Models),
│                 # CarbonIntensityService, LocationService, StorageService
├── State/        # AppState
├── Views/        # Onboarding, Dashboard, Tips, CarbonMap, Profile, Scanner, Shared
├── Resources/    # Datos locales (tarifas, curvas)
└── Utils/        # Extensiones y formateadores
```

**Flujo de datos:**
`Recibo (foto) → Vision OCR → Parser → SwiftData → DACCalculator + ApplianceRanker → ActionDecision → NarrativeService (LLM on-device) → Dashboard`

##  Stack

- **Lenguaje:** Swift
- **UI:** SwiftUI (UIKit puntual para cámara)
- **Persistencia:** SwiftData
- **IA / ML:** Vision, Foundation Models
- **Ubicación:** Core Location
- **Fotos:** PhotosUI
- **Sin APIs externas ni servicios en la nube**

## ▶️ Cómo correrlo

**Requisitos**
- Xcode con soporte para iOS 18+
- Para la narrativa con IA: dispositivo o simulador compatible con Apple Intelligence (si no está disponible, la app usa el texto de respaldo)

**Pasos**
```bash
git clone https://github.com/<tu-usuario>/OhMio.git
cd OhMio
open OhmMio.xcodeproj
```
Selecciona un simulador de iPhone y presiona **⌘R**.

##  Roadmap

- [ ] **Reconocimiento de aparatos por video** (Vision + Core ML): recorres tu casa con la cámara y la app detecta refrigerador, AC, lavadora, etc.
- [ ] **Chatbot conversacional** (Foundation Models): pregunta marca, antigüedad y horas de uso de lo detectado, y cruza esos datos con los kWh reales del recibo.
- [ ] **Replantear la jerarquía del DAC**: pasar de "margen restante" a ahorro acumulado y hábitos, para que el margen no se lea como permiso de gasto.
- [ ] Notificaciones predictivas con WeatherKit (días de alto consumo por calor).
- [ ] Modo familia con margen compartido.
- [ ] Equivalencias en términos cotidianos (horas de Netflix, tacos, etc.).
- [ ] Reporte bimestral comparativo.
- [ ] Tarifas comerciales.

## Alcance y limitaciones

- El consumo por aparato es una **estimación heurística** (watts nominales × horas de uso). El roadmap busca afinarla con datos del usuario y del recibo.
- Los valores de tarifas y curvas de carbono son datos locales empaquetados; conviene validarlos periódicamente contra fuentes oficiales.

## 👥 Equipo

| Nombre | Rol |
|---|---|
| Emiliano Ruíz Plancarte |  Desarrollo iOS / SwiftUI / Arquitectura |
| Lourdes María García Garfias | UI Design, Accesibilidad, Desarrollo iOS |
| Melanie Sayuri Solis  | ML / Modelos CoreML  |

## 📄 Licencia

Código liberado bajo **Creative Commons Zero (CC0 1.0)** durante el evento, conforme a los lineamientos del hackathon. Al terminar, la propiedad intelectual regresa a sus autores.
