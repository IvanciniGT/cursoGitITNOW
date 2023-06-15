
# Qué es GIT?

Sistema de control de versiones distribuido

## Sistema de control de versiones 

- Guardar el código fuente de distintas versiones (que coexisten en paralelo en el tiempo) de un software
- Contruir un historial completo del proyecto que estamos desarrollando, auditable, y consultable en el futuro
- Syncronizar cambios entre los participantes de un proyecto

### Antiguos

- CVS
- TFS
- SVN Subversión (tortoise)

## distribuido?

Un proyecto... y su historia es la suma de todos los repositorios que tiene cada participante en el proyecto.

## Repositorio

Una especia de BBDD donde se guardan las distintas versiones de un proyecto, junto con metadatos:
- Quien ha creado una versión nueva, por qué?...
- Dentro de un repositorio, lo que guardamos son RAMAS.

## Commit en git

Una copia de seguridad COMPLETA (no incremental, no cambios) del estado actual en el que se encuentra el proyecto.
Los commits se almacenan en RAMAS.

## Quién creo GIT

Linus Torwalds, para poder manejar todo el código de Linux

## Rama (branch) ***

Una linea de evolución paralela en el tiempo de mi proyecto.

    REPOSITORIO 
        RAMAS
            COMMITS

---

# Cómo usamos git, operaciones básicas

                                rama compartida: C1 -> C2 -> C3 -> CM
                                            |
                           Repositorio Remoto 1 del proyecto A -> Servidor compartido en RED: Sistemas que permitan ALOJAR repos remotos de git:
                                            |                                                   GITHUB, GITLAB, BITBUCKET
    ----------------------------------------------------------------------------------------------- Red de mi empresa
     |                                                                          |
    IvanPC                                                                   MenchuPC
     |                                                                          |
     proyectoA                                                                  proyectoA
        |- carpeta (archivos) fichero1.txt                                          |- carpeta (archivos) fichero1.txt
            |- repositorio (asociado al proyectoA)                                  |- repositorio
                |- rama Ivan:       C1 -> C2 -> C3 -> CI -> CF                             |- remoto/rama compartida: C1 -> C2 -> C3  ->  CM
                |                       vvvvv            /                                 |                            vvvvv             ^
                |- rama compartida: C1 -> C2 -> C3 -> CM                                   |- rama compartida:        C1 -> C2 -> C3  ->  CM
                |                       vvvvv         ^                                    |                            vvvvv             ^
                |- remoto/Rama:     C1 -> C2 -> C3 -> CM **  copia del remoto              |- rama menchu:            C1 -> C2 -> [C3] -> CM -> CM2
                    compartida

        OPERACIONES GIT:
            - Generar una copia de seguridad de mi carpeta a una rama: COMMIT
            - Copiar commits de una rama a otra: 
              - creación de rama: checkout -b
                                  switch -c
                                  branch 
              - operación de fusión de cambios: Llevar commits de una rama a otra
                                  merge
                                  rebase
            - Copiar una rama de mi repo local al repo remoto: PUSH
            - Descargar un proyuecto, con sus ramas de un repo remoto a un local (lo que hace inicialmente menchu): CLONE
            - Menchu crea una nueva rama
            - Menchu despliega en su carpeta del proyecto, lo que hay en un determinado commit: CHECKOUT
            - Copiar los commit que tengo en una rama (copia) de una rama remota y copiar esa rama al repositorio remoto: PUSH
            - Copiar una rama remota, a mi copia local de esa rama remota: FETCH
              - De hecho el fetch COPIA en mi local todas las ramas remotas
            - git tiene una operación llamada PULL, que hace: FETCH + fusión de cambios (merge - por defecto- o bien un rebase)

# Servidores de alojamiento de repos remotos:

Destacan: Github, Gitlab, BitBucket

Además de permitirnos alojar repos remotos, ofrecen funcionalidades adicionales que no ofrece nativamente GIT.
- Autenticación de usuarios
- Control de acceso a repositorios
- Control de permisos sobre ramas
  - Pull request / Merge request: Solicitudes de fusión de cambios que hacen personas sin permisos sufientes para trabajar en un repo
- Notificaciones cuando hay cambios (email)
- ...

# Los 3 árboles de git

- Working directory: Directorio de trabajo
- Staging area: Área de preparación
- Repositorio


    WORKING DIR         STAGING                 REPOSITORIO
    Mi carpeta          Area de preparación ---> COMMIT
     archivos    -->    archivos

El área de staging nos da una oprtunidad de elegir lo que queremos guardar en cada momento en el repositorio, que no tiene por qué coincidir con lo que hay en mi carpeta.

El problema es que ese AREA DE STAGING no lo visualizamos

# Operaciones básicas de git

## Inicializar un repo

git init -> Crea la carpeta .git, que contiene el repo y el área de staging... Pero por ahora nos olvidamos de esa carpeta.

## Consultar el estado del proyecto

git status
    - Compara la carpeta de mi proyecto con el área de preparación y me muestra las diferencias
    - Compara el área de preapración con el último commit que haya en el repositorio y me muestra las diferencias 

## Añadir ficheros al área de staging

git add <pathspec>
        ARCHIVO     git copia el archivo al área de staging
        CARPETA     git copia la carpeta y todo lo que hay dentro al área de staging
        .           git copia al staging todos los archivos y carpetas dentro del 
                    directorio actual... recursivamente
        *           Similar al PUNTO, pero no añade (copia) archivos ni carpetas ocultos
        :/          Similar al PUNTO, pero copia todo lo que hay desde el directorio raiz

## Generar un commit

git commit -m "Alta del proyecto"
git commit -a -m "Alta del proyecto"
    El argumento -a, le dice a git, que primero haga un add de los ficheros que ya estuviera controlando (no ficheros nuevos)

## Configuración de git

git config [--global] user.name "Ivan Osuna"
git config user.email "ivan.osuna.ayuste@gmail.com"

## Mostrar el historial de commits del proyecto

git log --oneline --all

## Mostrar los cambios añadidos en un commit

git show COMMITID (alternativa: HEAD; HEAD^; HEAD~2)

## Viajado en el tiempo. 

Ver cómo era mi proyecto en una determinada coipia de seguridad (commit)

git checkout COMMITID (alternativa: HEAD; HEAD^; HEAD~2)
 ^^^^
 Éste es el único uso legítimo de checkout

## Diferencias entre versiones de COSAS Que guarda git

git diff <argumentos> -- <diferencias con respecto a qué>
                         archivo
                         carpeta
                         Si no pongo nada, todo el proyecto

git diff
    Por defecto muestra las diferencias entre la carpeta de trabajo y el área de staging
    --cached    muestra las diferencias entre el área de staging    y el último commit
    HEAD        muestra las diferencias entre la carpeta            y el último commit
    o un COMMIT ID                                                  o el commit elegido
    2 commit id muestra las diferencias entre los commits

## ESTADO DE NUESTRO PROYECTO

                                            GIT
                        ----------------------------------------
    Carpeta          <>     Staging        <>        Repositorio
 -------------------------------------------------------------------------------
    libreria.md (3) ->    libreria.md (3)       ---> C1 (libreria.md (1))
    codigo.md (4)   ->    codigo.md (4)         ---> C2 (libreria.md (2))
                                                ---> C3 (libreria.md (3))
                                                ---> C4 (libreria.md (3) + codigo.md (1))
                                                ---> C5 (libreria.md (3) + codigo.md (2))
                                                ---> C6 (libreria.md (3) + codigo.md (3))

# HEAD

HEAD indica dónde tengo YO la cabeza (y mis ojos) situados.
Por defecto, git siempre pone el HEAD en el último commit.
En definitiva:
- Commit que estoy visualizando en la carpeta de trabajo
- Commit a partir del cual se generará el proximo commit

# La variable HEAD la usamos para distintos menesteres:

- referirme al commit actual en el que me encuentro, sin necesidad de conocer su id
- referirme a commits anteriores
  - penúltimo commit HEAD^
                     HEAD~
  - antepenúltimo commit HEAD^^
                         HEAD~2
  - ante-antepenúltimo commit HEAD^^^
                         HEAD~3

## CAMBIAR EL HEAD A otro commit

Por defecto, git pone el HEAD en el último commit
git 

## Git restore

Restaurar un archivo o carpeta a una versión anterior.

git restore --staged        RESTAURAR ARCHIVOS EN EL AREA DE STAGING
git restore --worktree      RESTAURAR EN MI CARPETA *ES EL VALOR POR DEFECTO*
            --source        Si no se especifica nada:
                --worktree     Se restaura desde staging
                --staged       Se restaura desde el último commit

                                        GIT
                        ----------------------------------------
    Carpeta          <>     Staging        <>        Repositorio
 -------------------------------------------------------------------------------
    libreria.md (3) ->    libreria.md (3)       ---> C1 (libreria.md (1))
    codigo.md (5)   ->    codigo.md (5)         ---> C2 (libreria.md (2))
                                                ---> C3 (libreria.md (3))
                                                ---> C4 (libreria.md (3) + codigo.md (1))
                                                ---> C5 (libreria.md (3) + codigo.md (2))
                                                ---> C6 (libreria.md (3) + codigo.md (3))
                                                ---> C7 (libreria.md (3) + codigo.md (4))
                                                ---> C8 (libreria.md (3) + codigo.md (5))

git checkout -> git checkout (Para cambiar el HEAD y viajar en el tiempo)
                git switch   (Para trabajar con ramas)
                git restore  (Para restaurar archivos a versiones pasadas)

git reset -- fichero  Me permite restaurar un fichero en el área de staging al último commit.

---
## Para qué voy creando todos esos COMMITS

- Copias de seguridad de mi proyecto
- Tener puntos de restauración
- Controlar distintas versiones
- Una de :
  - Entender qué ha ocurrido con el proyecto                        ----> AUDITORIA
        merge
  - Conocer los cambios que se han ido incorporando a mi proyecto   ----> FACILITAR EL MNTO del producto
        rebase
  Y estas 2 son incompatibles

  CADA COMMIT DEBE INCORPORAR UN PAQUETE DE CAMBIOS / FUNCIONALIDAD


Por la mañana empiezo a tarbajar:
A las 12:00 Me voy a tomar un café y hago commit! y push 
A las 14:30 me voy a comer y haog commit !!!! y push 
A las 19:00 me voy a casa y hago commit !!!! y push

---
Git revert: Genera un nuevo commit de anulación de cambios
Git cherry-pick: Genera un commit de recuperación de cambios

----

git flows

---

REESCRIBIR EL HISTORIAL DE UN PROYECTO
--------------------------------------
- Si he generado 5 commits con: Me voy a comer... a trabajar... Basura: Los quiero eliminar o empaquetar
- He metido mal un mensaje de commit
- Me olvidé de un fichero
- Generé un commit, pensando que algo estaba acabado y ooh sorpresa... me faltó inicialziar un dato... otro commit

Todas estas cosas van ensuciando el historial.
Y es importantisimo tener un historial LIMPIO de un proyecto... para que luego sirva para algo.

# git commit --amend
    Es la más sencilla: Me permite reescribir el último commit:
        - Tanto su contenido
          git commit --amend -a --no-edit
        - Como solo el mensaje
          git commit --amend -m "Vuelvo a poner Operación elevado a"

    Me permite NO TENER QUE COMPACTAR COMMITS... ya que siempre voy reescribiendo el último

# git reset
    Me permiten compactar commits a posteriori

    Reset: borra commits del historial
    Pero... se puede ejecutar en 3 modos:
                    Workdir         Staging         Repo
                    mi carpeta
        --soft                                       √
            Compactar commits
        --mixed                        √             √                               POR DEFECTO
        --hard           √             √             √
            DE LOS POCO COMANDO DESTRUCTIVOS QUE TIENE GIT 
            
# git rebase --interactive
    Me permiten compactar commits a posteriori


IMPORTANTE !!!!

Estas operaciones SOLO DEBERÍA ( salvo que sepa muy bien lo que estoy haciendo y el impacto que va a tener ) REALIZARLAS
si los commits los tengo en mi máquina solamente. Es decir, que nadie más tenga esos commits.


----



Motivos para hacer commits trabajando yo solo sin compartir:
- Quiero un punto de restauración
- Quiero hacer una copia de seguridad de lo que llevo...
  - Me voy a la oficina y llevo trabajando toda la mañana. 
  - Y si me roban el portatil... y si se me cae... y se jode el HDD + PUSH (en mi rama, que no comparto con nadie)
- Creo que he acabado algo... lo commiteo: VERSION PARA COMPARTIR
    Y justo antes de compartirlo... Lo reviso... que me ha venido la inspiración... y mierda! se me olvido tal cosa.
    AMEND


