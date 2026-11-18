# Bitácora de prompts — Assignment 2 · FitLife

**Deploy:** https://fit-life-alpha.vercel.app
**Repositorio:** https://github.com/zaraterd31-cpu/FitLife
**Función:** Detector de estancamiento en el gym

---

## Paso 1 — Especificación (Checkpoint A, S19)

**Qué hace:**
Cada vez que el usuario guarda una serie en "Registra tu entrenamiento", la app agrupa las series de ese ejercicio por fecha (1 fecha = 1 sesión) y toma la mejor serie de cada sesión: la de más kilos y, si empatan, la de más repeticiones. Después compara las últimas 3 sesiones:
- Si ni la 2.ª ni la 3.ª superan a la 1.ª (más kilos, o los mismos kilos con más reps), muestra: "⚠️ Llevas 3 sesiones estancado en [ejercicio] ([kg] kg × [reps]). Prueba subir 1-2 repeticiones o bajar el peso 10% una semana."
- Si alguna la supera, muestra: "✅ Vas progresando en [ejercicio]: tu mejor serie en la última sesión fue [kg] kg × [reps]."
- Si hay menos de 3 sesiones, muestra: "Registra al menos 3 sesiones de [ejercicio] para analizar tu progreso (llevas N)."
Para comparar el nombre del ejercicio no se distinguen mayúsculas ni espacios de más ("press  banca" = "Press banca").

**Qué datos/inputs necesita:**
- Los campos que ya existen en el formulario de entrenamiento: ejercicio, fecha, número de serie, kilos y repeticiones.
- El historial que la app ya guarda en `localStorage` con la clave `fitlife_series` (arreglo de `{ejercicio, fecha, serie, kilos, repeticiones}`). No hay que agregar base de datos, login ni API.
- Lo nuevo es solo un recuadro (`#resultado-progreso`) debajo del botón "Guardar serie", donde aparece el resultado.

**Cómo se sabrá que funciona:**
- Caso 1 (estancado): guardo Press banca 60 kg × 8 el 10/11, el 12/11 y el 14/11 → al guardar la tercera debe salir la alerta ⚠️ con "60 kg × 8".
- Caso 2 (progreso): después guardo Press banca 62.5 kg × 8 el 16/11 → debe salir "✅ Vas progresando… 62.5 kg × 8".
- Caso 3 (pocos datos): guardo Sentadilla 80 kg × 5 una vez → debe salir "…(llevas 1)".
- Caso límite: el mismo peso pero con más reps (40 × 8, 40 × 8, 40 × 9) cuenta como progreso, no como estancamiento.
- Si recargo la página y guardo otra serie, el análisis toma en cuenta las series anteriores (se leen de `localStorage`).

**Auditado por:** (nombre de tu compañero) — **Cambios que pidió:** (qué campo cuestionó y qué cambiaste)

---

## Paso 2 — Construcción: decisiones aceptar / rechazar / revisar

> Llena esto con lo que de verdad te proponga la IA. Copia el prompt y la parte de la respuesta.

### Decisión 1
- **Prompt que usé:** (pega el que usaste para pedir la especificación)
- **Lo que propuso la IA:** guardar el historial en una clave nueva de `localStorage` (`fitlife_entrenamientos`) y "agregarla porque la app todavía no lo guarda", con objetos sin número de serie.
- **Campo de la especificación en juego:** Qué datos/inputs necesita.
- **Decisión:** Rechazar
- **Por qué:** mi app ya guarda las series en `fitlife_series` con número de serie. Con una clave nueva se duplicarían los datos y las series que ya tengo no contarían para el análisis.

### Decisión 2 (opcional, sube el nivel)
- **Prompt que usé:**
- **Lo que propuso la IA:**
- **Campo de la especificación en juego:**
- **Decisión:**
- **Por qué:**

---

## Paso 3 — Validación (antes de S20)

- **Deploy responde:** Sí / No (fecha y hora de la prueba)
- **Input exacto:**
- **Output real (copiado y pegado):**
```
(pega aquí el texto exacto que mostró la app)
```
- **Veredicto:** Aceptar / Rechazar / Revisar
- **Razón (cita el output):**

---

## Uso de IA
- **Herramienta(s):**
- **Para qué la usé:**
- **Qué decidí o verifiqué yo:**
