# Tags

Permiten identificar commits

# Hooks

Scripts de automatización que se ejecutan cuando ocurren eventos.

# Notas

Mensajes adicionales para el historial

# Submodulos

Nos permiten una especie de gestión de dependencias

# Fork + Merge Request

## Fork

Es hacer una copia de un repo para que sea mio y poder trabajar a gusto

## Merge request (Gitlab) / Pull request (Github + BitBucket)

Solicitud de merge a los jefes supremos nivel dios del otro repo.
Podemos hacer un merge desde un fork o desde el propio repo. 
En muchos repos protegemos ramas, y cuando se quieren integrar cambios en esas ramas se hace mediante un merge request.

## Archivo README.md

Markdown que es un lenguaje para formatear y documentar información.

Qué va en estos documentos?
- De qué va esto?
- Cómo se usa?
- Contacto
- Bagdes: Imágenes generadas bajo demanda que informan del estado del proyecto.

---

# Qué meto en git?

No todo lo voy a guardar en git:
- Credenciales -> Sistema securizado
- Temporales
  - logs de ejecución
  - resultados de pruebas
- Compilados, o necesarios para compilar \
- artefacto final                        / Se debe poder generar desde lo que subo a git.... y por ende no lo subo a git.
- Dependencias (nuget, maven, npm, pip)
- configuraciones locales (entorno de desarrollo)

## Para evitar que ciertos archivos se suban al repo de git, tenemos los archivos .gitignore

- Son archivos de texto plano
- Puedo poner comentarios en ellos (con el #)
- Podemos usar caracteres comodín:
  - *
  - **
  - ?
                                                    # Quiero ignorar todos los archivos con extensión txt:                
                                                    *.txt
                                                    # Quiero ignorar todos los archivos con extensión txt de la carpeta temp:
                                                    temp/*.txt                
                                                    # Quiero ignorar todos los archivos con extensión txt de la carpeta temp y sus subcarpetas:
                                                    temp/**/*.txt                
                                                    # Quiero ignorar los ficheros con extensión de 3 caracteres
                                                    *.???
- Con respecto a las carpetas:
    - Las carpeta en UNIX -> LINUX -> git, se definen: carpeta/
                                                    # Quiero ignorar las carpetas tmp que haya por ahí
                                                    tmp/
    - Una barra al comienzo de la linea, marca una ruta relativa a la raíz del proyecto 
                                                    # Quiero ignorar la carpeta tmp que hay en la raíz del proyecto
                                                    /tmp/
- Contra-regla: se pone con una admiración
                                                    # Quiero ignorar todas las carpetas tmp:
                                                    tmp/
                                                    # Salvo la que hay en la raiz del proyecto
                                                    !/tmp/

## Imaginad que quiero que git ignore todos los archivos sin extensión del proyecto

                    # Ignora todos los archivos
                    *
                    # Pero no ignores los que tengan extensión
                    !*.*    
                    # Tampoco ignores las carpetas, que tampo llevan extensión (un punto en el nombre)
                    !*/


## Configuración global de gitignore

git config --global core.excludesfile ~/.gitignore

thumbs.db

# Qué guarda git?

Archivos... con su ruta
pero no guarda carpetas

Git no guardaría nunca una carpeta vacia

----

Desarrollar un software
1- Definir el git flow:
    - Roles en el proyecto:
      - Mantenedores        
            ^v Si voy a trabar con MERGE REQUEST
      - Desarrolladores     Internos o Externos? << SUBRAMAS o FORKs
    - Metodología de gestión de proyecto                    -> 
      - Metodología en cascada (tradicional): Pases a prod tengo? pocos
      - Metodología ágil: Un huevo de pases a prod. 1 por sprint (iteración)
    - Esquema de versionado                                 -> Ramas de nivel principal que creo y sistema de TAGs
      - Si controlo lo que se instala en los entornos de producción:
          - SI                  Los clientes en todo momento tienen lo que yo quiero.
          - NO                  Los clientes en todo momento tienen lo que ellos quieren
          - Controlo un poco    Los clientes tienen lo que tienen.... pero si se actualizan es a lo que yo quiero
      - Si voy a controlar varias versiones paralelas: MAJORs -> Varias ramas de release
    - Volumetría de commits                                 -> Ramas con las que voy a trabajar

---
# App para teléfonos moviles.

Queremos varias versiones paralelas del producto (MAJORS)? NO
Metodología ágil
Internos y externos

--- 

Al empezar:

--- hotfix                              C4 --- CHF1 *               Siempre sale de lo ultimo que hay en master
                                         ^      v ff [merge request]
--- master ---------------------------- C4 --- CHF1
                                         ^
--- release -------------------- C3     C4   Release Candidates
                                  ^      ^
--- desarrollo* -- C1 ---------- C3-----C4 ------------ CHF1 *
                    v             ^      ^ ff[mr]     cherry-pick, merge tradicional... si sigue aplicando en la versión que estamos desarrollando
     feature1      C1 --- C2 --- C3 --- C4
                    v                  / merge recursivo (minimizarse)
     feature2      C1 --- CA --- CB --/

            desarrollo ---> feature1 (cada vez que se añada un nuevo feature a desarrollo, los otros features tendrán que actualizarse)
                            rebase. Cada día quiero que se estén haciendo rebases a los features
            feature1   ---> desarrollo (cada vez que se acaba un feature)
                            git merge --ff-only
                            Antes de hacer ese merge, siempre hago primero un rebase

Al hacer los merge ff -> podre solicitar que se hagan o no con squash!

     llevar commits de un sitio a otro:
        - merge fast-forward = Copiar commit
        - merge recursivo    = Generar un commit de fusión de cambios nuevo
        - rebase             = Reescribir los commits desde origen para incluir cambios desde el principio.


Por que nunca querriamos una rama por spring? Una cosa son las características en las que trabajo y otra las que subo.
