# 🤺 PDA – Secuencias de Victoria · Esgrima

> **Teoría de Autómatas y Lenguajes Formales**  
> Dr. Alonso Inostrosa Psijas

Modelado de un combate de **esgrima** mediante un **Autómata Apilador (PDA)**. El autómata acepta exactamente las secuencias de acciones que llevan al atacante a ejecutar un tocado válido.

---

## 📂 Archivos del proyecto

| Archivo | Descripción |
|---|---|
| `Proyecto_PDA_Esgrima.pptx` | 📊 Presentación PowerPoint — 9 slides con bullets de ideas |
| `Guion_Presentacion_PDA.docx` | 📄 Guión completo — lo que decir en cada slide |
| `index.html` | 🌐 Web app interactiva — simulador + diagrama de estados |
| `README.md` | 📋 Este archivo |

---

## 🎯 Definición Formal

```
PDA = (Q, Σ, Γ, δ, q₀, Z₀, F)

Q  = { q_a, q_d, q_v }           — ataque / defensa / victoria
Σ_A = { avance, tocado }          — acciones del Atacante
Σ_O = { retirada, parada_fallida } — acciones del Defensor
Γ  = { G, L, M, C, CC }          — 5 posiciones de la pila
q₀ = q_a                          — estado inicial
Z₀ = G                            — símbolo inicial (En Garde)
F  = { q_v }                      — aceptación por estado final
```

---

## 🗺️ Diagrama de Estados

```
           avance, G / L·G
           avance, L / M·L
           avance, M / C·M
           avance, C / CC·C
    ┌──────────────────────────────────┐
    ↓                                  │
──▶ (q_a) ──────────────────▶ ((q_v))
    ↑          tocado, CC/CC           
    │                                  
    └──────────────────────────────────
          retirada, X / ε  (POP)
    - - - parada_fallida, X / X (NO-OP)
          X ∈ {L, M, C, CC}
```

---

## 📊 Tabla de Transiciones (13 reglas)

### Turno del Atacante — q_a

| # | Lee | Tope | → Estado | Operación | Significado |
|---|-----|------|----------|-----------|-------------|
| 1 | avance | G | q_d | PUSH L | En Garde → Largo |
| 2 | avance | L | q_d | PUSH M | Largo → Medio |
| 3 | avance | M | q_d | PUSH C | Medio → Corto |
| 4 | avance | C | q_d | PUSH CC | Corto → Corps-à-corps |
| **5** | **tocado** | **CC** | **q_v** | **VICTORIA ★** | **Tocado válido** |

### Turno del Defensor — q_d

| # | Lee | Tope | → Estado | Operación | Significado |
|---|-----|------|----------|-----------|-------------|
| 6 | retirada | L | q_a | POP | Retrocede a En Garde |
| 7 | retirada | M | q_a | POP | Retrocede a Largo |
| 8 | retirada | C | q_a | POP | Retrocede a Medio |
| 9 | retirada | CC | q_a | POP | Retrocede a Corto |
| 10 | parada_fallida | L | q_a | NO-OP | Mantiene Largo |
| 11 | parada_fallida | M | q_a | NO-OP | Mantiene Medio |
| 12 | parada_fallida | C | q_a | NO-OP | Mantiene Corto |
| 13 | parada_fallida | CC | q_a | NO-OP | Mantiene Corps-à-corps |

---

## ✅ Traza Aceptada

**Cadena:** `avance · parada_fallida · avance · retirada · avance · avance · parada_fallida · tocado`

| Paso | Estado | Lee | Tope | Operación | Pila |
|------|--------|-----|------|-----------|------|
| 0 | q_a | — | — | inicio | `G` |
| 1 | q_a | avance | G | PUSH L | `L G` |
| 2 | q_d | parada_fallida | L | NO-OP | `L G` |
| 3 | q_a | avance | L | PUSH M | `M L G` |
| 4 | q_d | retirada | M | POP | `L G` |
| 5 | q_a | avance | L | PUSH M | `M L G` |
| 6 | q_d | parada_fallida | M | NO-OP | `M L G` |
| 7 | q_a | avance | M | PUSH C | `C M L G` |
| 8 | q_a | avance | C | PUSH CC | `CC C M L G` |
| 9 | q_d | parada_fallida | CC | NO-OP | `CC C M L G` |
| **10** | **q_a** | **tocado** | **CC** | **→ q_v ★** | **ACEPTA ✅** |

---

## ❌ Traza Rechazada

**Cadena:** `avance · retirada · tocado`

| Paso | Estado | Lee | Tope | Resultado |
|------|--------|-----|------|-----------|
| 0 | q_a | — | — | inicio — pila: `G` |
| 1 | q_a | avance | G | PUSH L — pila: `L G` |
| 2 | q_d | retirada | L | POP — pila: `G` |
| **3** | **q_a** | **tocado** | **G** | **δ(q_a, tocado, G) no definida → RECHAZA ❌** |

**Razón:** `tocado` requiere `CC` en el tope. O logró retirar a A hasta la guardia inicial antes de que pudiera rematar.

---

## 🧠 ¿Por qué PDA y no AFD?

Un AFD tiene memoria finita y **no puede recordar** cuántos avances se realizaron.  
Si O ejecuta varias retiradas, el AFD no sabe a qué posición exacta regresar.  
La **pila** resuelve esto: cada POP vuelve exactamente al estado anterior, sin importar la profundidad.

---

## 📋 Criterios de Evaluación

| Criterio | Puntos |
|---|---|
| Contexto del dominio | 15 |
| Justificación del dominio | 10 |
| Definición formal (Q, Σ, Γ, δ, q₀, Z₀, F) + diagrama + tabla | 25 |
| Justificación de decisiones de diseño | 20 |
| Traza aceptada | 12 |
| Traza rechazada | 12 |
| Dominio del tema / preguntas | 6 |
| **Total** | **100** |

---

## 🚀 Uso

Abrir `index.html` directamente en el navegador. No requiere servidor ni instalación.

---

*Teoría de Autómatas y Lenguajes Formales · Dr. Alonso Inostrosa Psijas*
