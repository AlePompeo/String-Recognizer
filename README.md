# 🔁 Rete Sequenziale — Riconoscitore di Pattern `ba(dc)*bac`

Progetto universitario per il corso di **Calcolatori Elettronici** — progettazione e realizzazione di una **macchina di Mealy** in grado di riconoscere sequenze che rispettano il formato `ba(dc)*bac`, realizzata con il simulatore [Digital](https://github.com/hneemann/Digital).

<img width="481" height="948" alt="image" src="https://github.com/user-attachments/assets/e07d42e3-20d8-4bf3-a0d6-023765d13f80" />

---

## 📌 Descrizione

Il circuito implementa una **rete sequenziale sincrona** (macchina di Mealy) che accetta in ingresso una sequenza infinita di caratteri dall'alfabeto `{a, b, c, d}` e produce in uscita `sì` ogni volta che viene completata una sottosequenza valida secondo l'espressione regolare:

```
ba(dc)*bac
```

dove `(dc)*` indica zero o più ripetizioni della coppia `dc`. Il riconoscimento è **sovrapposto**: una sequenza accettata può fungere da prefisso di una nuova sequenza valida.

---

## 🔤 Codifiche

### Alfabeto di input — `x1, x0`

| Carattere | `x1` | `x0` |
|-----------|------|------|
| `a`       | 0    | 0    |
| `b`       | 0    | 1    |
| `c`       | 1    | 1    |
| `d`       | 1    | 0    |

### Alfabeto di output — `z`

| Valore | `z` |
|--------|-----|
| `sì`   | 0   |
| `no`   | 1   |

---

## 🧠 Architettura della Macchina di Mealy

La macchina è descritta da un automa a stati finiti (FSM) con **3 flip-flop D** che codificano lo stato corrente tramite `Q2`, `Q1`, `Q0`. Le funzioni di stato successivo e di uscita sono implementate con **reti combinatorie a due livelli** (AND → OR), minimizzate tramite mappe di Karnaugh.

### Stati interni

| Stato | `Q2` | `Q1` | `Q0` | Significato                           |
|-------|------|------|------|---------------------------------------|
| S0    | 0    | 0    | 0    | Stato iniziale / reset                |
| S1    | 0    | 0    | 1    | Letto `b`                             |
| S2    | 0    | 1    | 0    | Letto `ba`                            |
| S3    | 0    | 1    | 1    | Letto `ba(dc)*b`                      |
| S4    | 1    | 0    | 0    | Letto `ba(dc)*ba`                     |
| S5    | 1    | 0    | 1    | Letto `ba(dc)*d` (attesa del `c`)     |
| S6    | 1    | 1    | 0    | Accettazione — letto `ba(dc)*bac`     |

### Struttura del circuito

- **Rete combinatoria di ingresso**: calcola `D2`, `D1`, `D0` (ingressi ai flip-flop) a partire da `Q2^n`, `Q1^n`, `Q0^n`, `x1`, `x0`.
- **Flip-flop D con clock comune**: memorizzano lo stato corrente `Q2^n`, `Q1^n`, `Q0^n`.
- **Tunnel**: collegano le uscite dei flip-flop alle reti combinatorie senza cablaggio diretto, migliorando la leggibilità del circuito.
- **Rete combinatoria di uscita**: calcola `z` in funzione dello stato corrente e dell'ingresso (logica Mealy).

---

## 🛠️ Come aprire e simulare il circuito

### Requisiti
- [Java Runtime Environment (JRE)](https://www.java.com/)
- [Digital — Logic Circuit Simulator](https://github.com/hneemann/Digital/releases)

### Apertura
1. Scaricare e avviare `Digital.jar`
2. Aprire il file `circuito.dig` tramite **File → Apri**

### Simulazione
- Avviare la simulazione con **Simulazione → Avvia**
- Pilotare il clock manualmente o in modalità tempo reale
- Impostare `x1` e `x0` per inserire i caratteri uno alla volta
- Osservare `z`: vale `0` (`sì`) al completamento di una sequenza valida

---

## 📁 Struttura del Repository

```
.
└── circuito.dig    # Macchina di Mealy per il riconoscimento di ba(dc)*bac
```

---

## ✅ Funzionalità Implementate

- Riconoscimento corretto di tutte le sequenze valide `ba(dc)*bac` con `n ≥ 0` ripetizioni di `dc`
- Gestione corretta delle sequenze sovrapposte (overlap): dopo l'accettazione, la macchina individua automaticamente eventuali prefissi condivisi con la sequenza successiva
- Uscita Mealy: `z=0` viene prodotta nell'ultimo colpo di clock della sequenza accettata, senza stato di accettazione separato
