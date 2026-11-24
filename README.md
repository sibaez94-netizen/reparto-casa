# Reparto de casa

Tablero semanal para repartir las tareas de casa entre dos personas, con el peso de cada
tarea calculado a partir de los minutos que cuesta, y registro de quién hizo qué.

No hay servidor: es una página estática en GitHub Pages que lee y escribe un único archivo,
`docs/datos.json`, a través de la API de GitHub.

## Puesta en marcha

1. Crea un repositorio **público** llamado `reparto-casa`.
2. Sube este contenido tal cual (`README.md` y la carpeta `docs/`).
3. En **Settings → Pages**, pon como origen la rama `main` y la carpeta `/docs`.
4. Entra en `https://<tu-usuario>.github.io/reparto-casa/`. Ya se ve el reparto, en modo
   de solo lectura.
5. En la pestaña **Mi móvil**, cada uno conecta su móvil con un token suyo (abajo).

La página deduce el usuario y el repositorio del propio enlace de GitHub Pages, así que no
hay nada que configurar aunque le cambies el nombre al repositorio. Si algún día la sirves
fuera de `github.io`, ajusta el objeto `REPO` al principio del `<script>`.

## El token

Para **mirar** no hace falta nada. Para **marcar tareas o cambiar minutos** sí, porque
escribir en GitHub siempre requiere autenticación, también en un repositorio público.

Cada persona se hace el suyo:

- GitHub → Settings → Developer settings → Personal access tokens → **Fine-grained tokens**
- Repository access: **Only select repositories** → solo `reparto-casa`
- Repository permissions: **Contents: Read and write**. Nada más.
- Caducidad: la que quieras. Cuando caduque, la app avisa y basta con generar otro.

El token se guarda en el `localStorage` del navegador de esa persona y no se sube a ningún
sitio. En «Mi móvil» hay un botón para borrarlo. Si pierdes el móvil, revoca ese token desde
GitHub: solo puede escribir en este repositorio y en ningún otro.

## Cómo funciona el reparto

- Cada tarea tiene unos **minutos** y las **veces por semana** que toca. De ahí sale su peso
  en porcentaje. Si alguien no está de acuerdo con el peso de una tarea, se discuten los
  minutos, no el porcentaje.
- **Repartir equilibrado** va de la tarea más larga a la más corta y le da cada una a quien
  menos minutos acumule. Sale un reparto cerrado cerca del 50/50.
- El tiempo se le apunta a **quien marca la casilla**, no a quien tenía la tarea asignada.
- Una tarea que esta semana no toca se marca con la × y deja de contar para el total.

## Qué hay en `docs/datos.json`

```jsonc
{
  "version": 1,
  "personas": [{ "id": "SIL", "nombre": "SIL" }, { "id": "LRC", "nombre": "LRC" }],
  "tareas": [
    { "id": "coc1", "nombre": "Fregar los cacharros", "zona": "Cocina",
      "minutos": 15, "veces": 7, "orden": 1 }
  ],
  "semanas": {
    "2026-S48": {
      "coc1": {
        "asignado": "SIL",            // "SIL" | "LRC" | "ambos" | null
        "omitida": false,
        "hechos": [{ "id": "a1b2", "p": "LRC", "t": "2026-11-24T09:12:00Z" }]
      }
    }
  }
}
```

Las semanas van por numeración ISO y empiezan en lunes. La app conserva las **últimas 30
semanas** dentro del archivo para que no crezca sin fin; el resto del historial sigue en los
commits del repositorio.

## Dos personas a la vez

Cada cambio se guarda poco después de hacerlo, como un commit. Si los dos tocáis algo casi a
la vez, GitHub rechaza la segunda escritura; la app relee el archivo, vuelve a aplicar encima
lo que tenía pendiente y reintenta, de forma que no se pierde nada de ninguno de los dos.
Cada «hecho» lleva su propio identificador, así que nunca se duplican ni se borran los del
otro. La página además se refresca sola cada 25 segundos.

No es tiempo real: un cambio tarda uno o dos segundos en guardarse, y el otro móvil lo ve en
el siguiente refresco.

## Aviso

El repositorio es público: cualquiera que dé con la dirección puede leer el reparto y el
historial. Por eso las personas van como iniciales. Si en algún momento quieres que deje de
ser público, hace falta GitHub Pro para publicar Pages desde un repositorio privado.
