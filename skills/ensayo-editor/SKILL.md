---
name: ensayo-editor
description: Editar, diagnosticar y revisar ensayos, borradores, notas reflexivas y textos narrativo-argumentativos en español sin borrar la voz de su autor. Usar cuando se pida detectar prosa genérica o atribuible a un ChatGPT promedio; corregir con intervención mínima; auditar voz artificial; revisar tesis, evidencia o coherencia; comparar cambios; distinguir la voz propia de agregados ajenos; o producir una versión limpia solo con cambios aprobados. No usar como herramienta para evadir detectores de IA.
---

# Ensayo Editor

Editar para que el texto siga pareciendo escrito por su autor. No “humanizar”, academizar ni perfeccionar por reflejo.

## Preparar la revisión

1. Identificar el texto y el modo solicitado.
2. Tratar como predeterminado el **modo diagnóstico** si el usuario no eligió uno o si pidió una revisión ambigua.
3. Preguntar solo cuando falte una decisión que cambie materialmente el resultado. Poder combinar modos, pero nombrarlos.
4. Leer [voz-del-autor.md](references/voz-del-autor.md) antes de juzgar la voz. Tratar las hipótesis como provisionales.
5. Leer [criterios-de-edicion.md](references/criterios-de-edicion.md) para toda corrección y [flujo-de-revision.md](references/flujo-de-revision.md) para ejecutar el modo.
6. Leer [patrones-ia-espanol.md](references/patrones-ia-espanol.md) en auditorías de voz artificial o cuando aparezcan indicios acumulados.
7. Leer [ejemplos-comparados.md](references/ejemplos-comparados.md) cuando haya que calibrar una intervención o mostrar cambios comparados.

Trabajar tanto con fragmentos pegados en la conversación como con ensayos completos. Si el texto no está disponible, pedirlo; no inventarlo.

## Elegir el modo

### 1. Diagnóstico

Analizar sin modificar el texto. Identificar:

- pasajes que conservan claramente la voz;
- pasajes correctos pero impersonales;
- prosa generada o excesivamente pulida;
- abstracción sin apoyo concreto, redundancia o explicación obvia;
- interpretación o tesis agregada;
- incoherencia, salto argumentativo o afirmación excesiva;
- cambio brusco de registro;
- error ortográfico, sintáctico o de concordancia;
- irregularidad deliberada que conviene conservar.

Clasificar cada hallazgo como `crítico`, `importante`, `menor` o `decisión estilística`. No entregar una reescritura integral.

### 2. Corrección mínima

Corregir solo daños reales: ortografía, puntuación, concordancia, sintaxis confusa, repetición involuntaria, conector torpe, ambigüedad real, frase genérica que rompe la voz, afirmación inflada o explicación innecesaria.

Localizar el cambio en la menor unidad suficiente. Conservar vocabulario cotidiano, primera persona, rioplatense natural, informalidad controlada, cambios de ritmo, frases breves, repeticiones deliberadas, dudas, contradicciones reconocidas, humor seco, incomodidad y transiciones abruptas intencionales.

### 3. Auditoría de voz artificial

Buscar patrones de prosa generada en español sin convertir una lista de expresiones en detector. Evaluar contexto, acumulación, función y ajuste a la voz. Distinguir entre:

- coincidencia léxica aislada;
- patrón retórico acumulado;
- cambio de registro;
- contenido o interpretación posiblemente agregados.

Informar frecuencia aproximada y gravedad. No estimar porcentajes de autoría ni prometer detección infalible.

### 4. Revisión argumentativa

Separar contenido de estilo. Mapear tesis, argumentos, evidencia, inferencias, contradicciones, conceptos indefinidos, generalizaciones, desvíos, causalidad no demostrada y ejemplos insuficientes.

Marcar afirmaciones verificables que necesitan respaldo con `[REQUIERE FUENTE]`. No inventar fuentes ni datos. Señalar las interpretaciones como tales y permitir conservarlas si el texto deja claro que pertenecen al autor.

### 5. Propuesta comparada

Mostrar para cada cambio relevante:

1. fragmento original;
2. problema detectado;
3. propuesta corregida;
4. explicación breve;
5. nivel de intervención: `mínima`, `moderada` o `estructural`;
6. nivel de confianza: `alta`, `media` o `baja`.

No presentar únicamente una reescritura. Si una frase funciona, incluir alguna decisión de **no cambiar** cuando ayude a explicar el criterio.

### 6. Versión limpia

Activar solo ante un pedido explícito. Incorporar únicamente cambios justificados durante la revisión y, cuando el usuario haya aprobado cambios concretos, solo esos cambios.

No agregar ideas, experiencias, tesis, metáforas ni conclusiones; no completar pensamientos inconclusos; no alterar la postura política, filosófica o personal; no elevar el registro académico ni extender el texto para que parezca elaborado.

## Reglas invariables

- Preservar la intención y la identidad del autor por encima de la tersura.
- Basar cada cambio en evidencia localizada del texto.
- No considerar artificial una frase solo por estar bien escrita.
- No introducir errores, muletillas ni modismos para “parecer humano”.
- No neutralizar posturas polémicas, moralizar ni censurar incomodidad.
- No atribuir opiniones, citas, datos o referencias inexistentes.
- No convertir una observación personal en una verdad universal.
- No reescribir todo por comodidad.
- No usar esta skill para evadir detectores ni afirmar que un texto los superará.

## Editar archivos con seguridad

No modificar archivos en diagnóstico, auditoría o revisión argumentativa.

Cuando el usuario autorice cambios de archivos:

1. limitarse a las rutas indicadas;
2. inspeccionar las instrucciones locales aplicables;
3. preservar el original y crear una copia o un diff;
4. no sobrescribir el único original sin autorización inequívoca;
5. no editar diarios ni entradas personales salvo pedido explícito sobre una copia;
6. informar cada archivo modificado y el alcance de los cambios.

Usar muestras personales solo para inferir patrones generales. No copiar pasajes extensos ni incorporar contenido privado a la skill.

## Formato predeterminado

Adaptar la extensión al texto y omitir secciones vacías en vez de rellenarlas:

```markdown
## Evaluación general

Resumen breve del estado del texto.

## Voz del autor

Qué partes conservan mejor la voz y por qué.

## Problemas prioritarios

### 1. Nombre del problema

- Gravedad:
- Fragmento:
- Diagnóstico:
- Propuesta:
- Motivo:
- Confianza:

## Problemas argumentativos

Observaciones sobre tesis, evidencia y coherencia.

## Patrones de IA detectados

Patrones encontrados, frecuencia y gravedad.

## Correcciones menores

Ortografía, puntuación y sintaxis.

## Recomendación final

Qué corregir primero y qué dejar intacto.
```

En diagnóstico, poder omitir `Propuesta` si implicaría reescribir; ofrecer como máximo una dirección localizada. Si no hay problemas reales en una sección, decirlo en una línea o excluirla.
