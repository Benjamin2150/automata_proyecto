# 🤺 PDA – Secuencias de Victoria | Esgrima

> Proyecto de **Teoría de Autómatas y Lenguajes Formales**  
> Profesor: Dr. Alonso Inostrosa Psijas  
> Modelado de un combate de esgrima mediante un **Autómata Apilador (Pushdown Automaton)**

---

## 📋 Descripción

Este proyecto modela las **secuencias de acciones válidas** que conducen a la victoria del atacante en un combate de esgrima, usando un **Autómata Apilador (PDA)**. Incluye:

- 🎯 Definición formal completa del PDA
- 📊 Tabla de transiciones (13 reglas)
- 🗺️ Diagrama de estados
- ✅ Traza aceptada paso a paso
- ❌ Traza rechazada con razón del rechazo
- 🎮 **Simulador interactivo** en el navegador

---

## 🚀 Cómo usar

**Opción 1 – Directamente en el navegador:**
```
Abrir el archivo:  index.html
```
No requiere servidor, instalación ni dependencias. Solo abrir con cualquier navegador moderno.

**Opción 2 – Clonar el repositorio:**
```bash
git clone https://github.com/[usuario]/automata_proyecto.git
cd automata_proyecto
# Abrir index.html en el navegador
```

---

## 🤺 Dominio: Esgrima (Fencing)

Un combate de florete entre dos tiradores donde **solo el atacante puede ganar**:

| Posición | Código | Descripción |
|----------|--------|-------------|
| En Garde | G      | Posición inicial de guardia |
| Largo    | L      | Fuera de alcance de ataque |
| Medio    | M      | Distancia de espadachín |
| Corto    | C      | Distancia de ataque directo |
| Corps-à-corps | CC | Único lugar donde el tocado es válido |

**Escalera de distancias:**
```
G ──avance──► L ──avance──► M ──avance──► C ──avance──► CC ──tocado──► VICTORIA
```

---

## ⚙️ Definición Formal del PDA

```
PDA = (Q, Σ, Γ, δ, q₀, Z₀, F)

Q  = { q_a, q_d, q_v }       — ataque, defensa, victoria
Σ_A = { avance, tocado }      — acciones del atacante
Σ_O = { retirada, parada_fallida } — acciones del defensor
Γ  = { G, L, M, C, CC }      — 5 posiciones (símbolos de la pila)
q₀ = q_a                     — estado inicial
Z₀ = G                       — símbolo inicial de la pila
F  = { q_v }                 — aceptación por estado final
```

---

## 📊 Tabla de Transiciones

### Turno del Atacante (q_a)

| # | Estado | Lee | Tope | → Estado | Pila | Significado |
|---|--------|-----|------|----------|------|-------------|
| 1 | q_a | avance | G | q_d | L G | en garde → largo (push L) |
| 2 | q_a | avance | L | q_d | M L | largo → medio (push M) |
| 3 | q_a | avance | M | q_d | C M | medio → corto (push C) |
| 4 | q_a | avance | C | q_d | CC C | corto → corps-à-corps (push CC) |
| 5 | q_a | tocado | CC | **q_v** | CC | **VICTORIA ★** |

### Turno del Defensor (q_d)

| # | Estado | Lee | Tope | → Estado | Pila | Significado |
|---|--------|-----|------|----------|------|-------------|
| 6 | q_d | retirada | L | q_a | ε (pop) | retrocede a en garde |
| 7 | q_d | retirada | M | q_a | ε (pop) | retrocede a largo |
| 8 | q_d | retirada | C | q_a | ε (pop) | retrocede a medio |
| 9 | q_d | retirada | CC | q_a | ε (pop) | retrocede a corto |
| 10 | q_d | parada_fallida | L | q_a | L | no-op en largo |
| 11 | q_d | parada_fallida | M | q_a | M | no-op en medio |
| 12 | q_d | parada_fallida | C | q_a | C | no-op en corto |
| 13 | q_d | parada_fallida | CC | q_a | CC | no-op en corps-à-corps |

---

## ✅ Traza Aceptada

**Cadena:** `avance · parada_fallida · avance · retirada · avance · avance · parada_fallida · tocado`

| Paso | Estado | Lee | Tope | Regla | Pila |
|------|--------|-----|------|-------|------|
| 0 | q_a | — | — | inicio | G |
| 1 | q_a | avance | G | #1 push L | L G |
| 2 | q_d | parada_fallida | L | #10 no-op | L G |
| 3 | q_a | avance | L | #2 push M | M L G |
| 4 | q_d | retirada | M | #7 pop | L G |
| 5 | q_a | avance | L | #2 push M | M L G |
| 6 | q_d | parada_fallida | M | #11 no-op | M L G |
| 7 | q_a | avance | M | #3 push C | C M L G |
| 8 | q_a | avance | C | #4 push CC | CC C M L G |
| 9 | q_d | parada_fallida | CC | #13 no-op | CC C M L G |
| **10** | q_a | tocado | CC | **#5** | **→ q_v ✅ ACEPTA** |

---

## ❌ Traza Rechazada

**Cadena:** `avance · retirada · tocado`

| Paso | Estado | Lee | Tope | Regla | Pila |
|------|--------|-----|------|-------|------|
| 0 | q_a | — | — | inicio | G |
| 1 | q_a | avance | G | #1 push L | L G |
| 2 | q_d | retirada | L | #6 pop | G |
| **3** | q_a | tocado | G | **— sin transición** | **❌ RECHAZA** |

**Razón:** `δ(q_a, tocado, G)` no está definida. `tocado` exige `CC` en el tope. Semánticamente: A perdió toda ventaja de posición y no puede rematar desde la guardia.

---

## 🧠 ¿Por qué PDA y no AFD?

Un Autómata Finito Determinista (AFD) **no puede recordar** cuántos avances se realizaron ni cuál era la posición exacta anterior tras una retirada. La **pila** resuelve esto:

- **PUSH** (avance): apila la nueva posición encima
- **POP** (retirada): desapila exactamente un nivel, volviendo al estado previo correcto
- **NO-OP** (parada_fallida): la pila no cambia

Esto justifica formalmente el uso de un PDA sobre un AFD.

---

## 📁 Estructura del proyecto

```
automata_proyecto/
├── index.html      ← Aplicación web interactiva completa
└── README.md       ← Este archivo
```

---

## 🎓 Criterios de Evaluación

| Criterio | Puntaje |
|----------|---------|
| Contexto del dominio | 15 pts |
| Justificación del dominio | 10 pts |
| Definición formal del PDA | 25 pts |
| Justificación de decisiones de diseño | 20 pts |
| Traza aceptada | 12 pts |
| Traza rechazada | 12 pts |
| Dominio y preguntas | 6 pts |
| **Total** | **100 pts** |

---

*Teoría de Autómatas y Lenguajes Formales · Dr. Alonso Inostrosa Psijas*
