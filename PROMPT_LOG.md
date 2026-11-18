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

**Auditado por:** Matias rodete. Me pidió que salieran cuantas series faltan por registrar 
---

## Paso 2 — Construcción: decisiones aceptar / rechazar / revisar

### Decisión 1
- **Prompt que usé:** Le pasé a Claude las instrucciones completas del Assignment 2 y le escribí: "con mi proyecto de fit life". Después le pegué mi index.html.
- **Lo que propuso la IA:** guardar el historial en una clave nueva de `localStorage` (`fitlife_entrenamientos`) y "agregarla porque la app todavía no lo guarda", con objetos sin número de serie.
- **Campo de la especificación en juego:** Qué datos/inputs necesita.
- **Decisión:** Rechazar
- **Por qué:** mi app ya guarda las series en `fitlife_series` con número de serie. Con una clave nueva se duplicarían los datos y las series que ya tengo no contarían para el análisis.

### Decisión 2
- **Prompt que usé:** "si" (después de pedirle a Claude que me ayudara a subir de nivel).
- **Lo que noté en el output:** en mi prueba en el deploy el mensaje salió "✅ Vas progresando en sentadilla: tu mejor serie en la última sesión fue 33 kg × 5.", con el ejercicio en minúsculas tal como lo escribí. Si lo escribía en mayúsculas, el mensaje salía diferente aunque fuera el mismo ejercicio.
- **Campo de la especificación en juego:** Qué hace (el texto exacto del mensaje y que el nombre no dependa de mayúsculas).
- **Decisión:** Revisar
- **Por qué:** el análisis ya trataba "sentadilla" y "SENTADILLA" como el mismo ejercicio, así que el mensaje también debía verse igual. Pedí que se muestre siempre con mayúscula inicial ("Sentadilla").

---

## Paso 3 — Validación (antes de S20)

### Caso 1 (estancado)
- **Deploy responde:** Sí — probado el 17/11/2026 a las 22:25 en https://fit-life-alpha.vercel.app
- **Input exacto:** tres series de "press banca", serie 1, 60 kg, 8 repeticiones, guardadas en 3 fechas distintas (10/11/2026, 12/11/2026 y 14/11/2026).
- **Output real (copiado y pegado):**
```
Serie guardada.
⚠️ Llevas 3 sesiones estancado en press banca (60 kg × 8). Prueba subir 1-2 repeticiones o bajar el peso 10% una semana.
```
- **Veredicto:** Aceptar
- **Razón:** el output muestra "60 kg × 8" y la alerta ⚠️ al guardar la tercera sesión sin mejorar, que es exactamente lo que dice el Caso 1 de mi especificación.

### Caso extra — mismo ejercicio con mayúsculas y minúsculas (después de la Decisión 2)
- **Deploy responde:** Sí — probado el 17/11/2026 a las 22:45.
- **Input exacto:** "sentadilla" 20/11/2026, serie 2, 60 kg × 6; "SENTADILLA" 21/11/2026, serie 3, 60 kg × 6; y una serie de 70 kg × 5 (FECHA Y CÓMO ESCRIBISTE EL NOMBRE).
- **Output real (copiado y pegado):**
```
Serie guardada.
✅ Vas progresando en Sentadilla: tu mejor serie en la última sesión fue 70 kg × 5.
```
- **Veredicto:** Aceptar
- **Razón:** el mensaje dice "Sentadilla" con mayúscula aunque lo escribí en minúsculas y en mayúsculas, y tomó todas las series como el mismo ejercicio, como dice mi especificación.

---

## Uso de IA
- **Herramienta(s):** Claude (chat).
- **Para qué la usé:** para redactar la especificación de 3 campos y generar el código del detector de estancamiento dentro de mi index.html.
- **Qué decidí o verifiqué yo:** rechacé que se creara una clave nueva de localStorage y pedí usar la que ya tenía (fitlife_series); al probar en el deploy vi que el nombre del ejercicio salía en minúsculas y pedí revisarlo; subí cada cambio a GitHub y probé los casos en la página real.
