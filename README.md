markdown
# EP01 — Ingeniería DevOps

## Integrantes

- Martin Peña
- Benjamin Yantzen

## Modelo de ramificación

### Modelos considerados

- **GitFlow**
- **GitHub Flow**
- **Trunk-Based Development**

### Modelo utilizado

**GitFlow**

### Justificación

Elegimos GitFlow para separar el código estable (main) del código en integración (develop), clave para nuestros releases con SemVer. 
Esto nos dio orden y revisión por PR, algo que perderíamos con Trunk-Based. Esto se probó al sacar un hotfix/* desde main para el bug del párrafo duplicado, sin tocar features inestables
## Buenas prácticas del repositorio

### Convención de commits

Se utiliza el estándar de **Conventional Commits**, con el formato:

<tipo>(<alcance opcional>): <descripción>


Tipos utilizados en este proyecto:

| Tipo    | Uso                                              |
|---------|---------------------------------------------------|
| feat    | Nueva funcionalidad                               |
| fix     | Corrección de errores                             |
| docs    | Cambios en documentación                          |
| chore   | Tareas de mantenimiento (configuración, CI, etc.) |

Ejemplos usados en el repositorio:
- `feat(ui): actualizar presentacion para EP01 DevOps`
- `docs: agregar changelog del microservicio empresas`
- `chore(ci): agregar workflow hola mundo`
- `fix(ui): corregir descripcion de responsabilidad del servicio`

### Naming de ramas

| Prefijo      | Propósito                                   | Se crea desde | Se fusiona en |
|--------------|----------------------------------------------|----------------|----------------|
| `feature/*`  | Nuevas funcionalidades o tareas de mejora     | `develop`      | `develop`      |
| `hotfix/*`   | Correcciones urgentes sobre producción        | `main`         | `main` y luego `develop` |

Ejemplos: `feature/presentacion-devops`, `feature/changelog`, `feature/configuracion-ci`, `hotfix/corregir-descripcion`.

### Flujo de merge

main
├── hotfix/* → main → develop
└── develop
└── feature/* → develop


1. Las `feature/*` se crean desde `develop` y se fusionan de vuelta en `develop` vía Pull Request.
2. Los `hotfix/*` se crean desde `main`, se fusionan en `main` vía Pull Request, y luego ese cambio se propaga a `develop`.
3. Cuando `develop` acumula las features listas para una nueva versión estable, se abre un Pull Request de `develop` hacia `main`.

### Estrategia de revisión

- Cada Pull Request requiere al menos un **reviewer** distinto del autor.
- El reviewer revisa los cambios en la pestaña "Files changed" y debe dejar una revisión de tipo **Approve** antes del merge.
- Los roles de autor/reviewer se alternan entre los integrantes para asegurar que ambos participen tanto escribiendo código como revisándolo.

### Estructura del repositorio

ms/empresas/
├── src/
│ ├── main/
│ │ ├── java/
│ │ └── resources/
│ └── test/
├── docs/
├── .github/
│ └── workflows/
├── pom.xml
├── Dockerfile
├── docker-compose.yml
├── README.md
└── CHANGELOG.md



### Control de versiones

El proyecto sigue **versionado semántico** (SemVer: `MAYOR.MENOR.PARCHE`), documentado en `CHANGELOG.md`. La versión actual es `1.0.0`, correspondiente a la versión inicial del microservicio.

## GitHub Actions

Se configuró un workflow (`.github/workflows/hola-mundo.yml`) que se ejecuta automáticamente:

- En cada `push` a la rama `develop`.
- En cada `pull_request` dirigido a la rama `main`.

El job `saludo` imprime información del repositorio, la rama y el actor que ejecuta la acción, sirviendo como prueba de concepto de integración continua (CI).

## Uso de inteligencia artificial

se uso como complemento para manejar errores y apoyo dentro de visual studio
## Conclusiones

### Reflexión Martin Peña

Aprendí a manejar ramas, PRs y SemVer, aunque lo más difícil fue la configuración (.gitignore, rutas, Maven, YAML). Ser reviewer del código de Benjamín me hizo notar detalles que en lo propio no vería. A futuro, sería más estricto con el Approve desde el primer PR.
### Reflexión Benjamin Yantzen

Entendí por qué no conviene tocar la rama principal directamente: aprendí features, resolución de conflictos y rutas relativas. Turnarnos como autor y revisor con Martín me hizo escribir código pensando en que otro lo revisaría. A futuro, haría commits más atómicos en vez de un solo PR grande.
