
# Git 

Es un sistema de control de versiones distribuido:
No existe un repositorio central que contenga toda la info del proyecto.
Tenemos muchos repositorios en las máquinas de los participantes, que cada uno es diferente de los demás.

# Commit en git

Es una foto completa del proyecto en un inistante del tiempo.
No es un conjunto de cambios incremental.
Esos, cuando son necesarios git los calcula bajo demanda.
Los commits se guardan en ramas.

Asociado a un commit se guarda cierta información (metadatos):
- Autor
- Fecha
- Mensaje descriptivo. Este mensaje puede tener varias lineas de texto.
    La primera linea se toma como TITULO/RESUMEN del paquete de cambios.
    El resto de lineas como información adicional.
        Si por ejemplo hacemos un git log --oneline, se muestra solo la primera linea
        Si hacemos un git log, se muestran todas las lienas del comentario

        Para configurar el ditor que usamos para los mensajes:
            git config --global core.editor "vim"

- ...

Un commit debe incluir solamente lo referido a un cambio. <<< IMPORTANTE

## Operaciones básicas de git en local

### Creación de un repositorio

Un repo almacena toda la historia del proyecto.
Cada usuario/participante del proyecto tiene su propio repo de git, que se guarda en una carpeta oculta dentro del proyecto: .git

Hay 2 formas de tener un repo:
- Crearlo desde cero. Esto realmente solo lo hace una persona por proyecto: 
    $ git init
- Clonar un repo desde un remoto
    $ git clone URL_REMOTO [nombre_carpeta]

### Establecer alguna configuración
    $ git config [--global] user.name "Ivan"
    $ git config [--global] user.email "ivan@gmail.com"

## Los tres árboles de git

    CARPETA                 AREA DE PREPARACION         REPO LOCAL
    (working dir)           (staging)                   (.git)
                 ---------->
                 add
                                                --------> commit -m "MENSAJE"
                 ---------->----------------------------> commit -a -m "MENSAJE"

## Auditoria / Consulta de información

### Consultar el histórico de commits:

git log --oneline --all

### Consultar los cambios introducidos en un commit:

git show COMMIT_ID*

    * Opcionalmente: 
        - HEAD Por defecto apunta al último commit
        - HEAD^  HEAD ~  Apunta al commit anterior al HEAD
        - HEAD^^ HEAD~2  Apunta a 2 commits antes del HEAD

### Consultar las diferencias entre archivos, carpetas, commits

    CARPETA                 AREA DE PREPARACION         REPO LOCAL
    (working dir)           (staging)                   (.git)
    archivo1                archivo1                    C1(archivo1)    <-----|
                                                        C2(archivo1)    <-----|  git diff C1 C2 -- archivo1

        <------------------->
         git diff -- archivo1
                                    <------------------->(C2)
                                    git diff --cached -- archivo1

        <----------------------------------------------->(C2)
                    git diff HEAD -- archivo1

# RESTAURACIONES DE ARCHIVOS

    CARPETA                 AREA DE PREPARACION         REPO LOCAL
    (working dir)           (staging)                   (.git)
    archivo1                archivo1                    C1(archivo1)
                                                        C2(archivo1)

                 <----------
                 git restore -- archivo1
                                    <------------------- (C2)
                                    git restore --staged -- archivo1
                                    git reset -- archivo1
                 <-------------------------------------- (C1, C2)
                  git restore --source C1 -- archivo1

                 <------------------<-------------------- (C1, C2)
                  git restore --staged --worktree --source C1 -- archivo1
                  git checkout C1 -- archivo1                                   Esto, a día de hoy, está desaconsejado

## Consultar el estado del proyecto

    CARPETA                 AREA DE PREPARACION         REPO LOCAL
    (working dir)           (staging)                   (.git)


git status
    Muestra las diferencias entre:
        - CARPETA <> AREA DE PREPARACION
        - AREA DE PREPARACION <> REPOSITORIO

## GENERAR UN COMMIT DE REVERSION DE CAMBIOS

Quiero quitar un paquete de cambios de un código, sin quitarlo del historial (Eso me permite recuperarlo en el futuro: cherry-pick)

    C1 -> C2 -> C3 -> C4
                         ^ Nos puede interesar revertir los cambios relaizados en C3
    git revert C3

    En ese caso, git:
        - Calcula los cambios que se introdujeron en ese commit.. que a priori git no los conoce: C2 -> PATCH -> C3
        - Una vez conocido el paquete de cambios, se genera un commit nuevo C5, después de C4, que anule ese PATCH

## RECUPERAR UN PAQUETE DE CAMBIOS QUE SE INTRODUJERON EN UN COMMIT ANTERIOR

    C1 -> C2 -> C3 -> C4
                         ^ Nos puede interesar recuperar los cambios relaizados en C3
    git cherry-pick C3

    En ese caso, git:
        - Calcula los cambios que se introdujeron en ese commit.. que a priori git no los conoce: C2 -> PATCH -> C3
        - Una vez conocido el paquete de cambios, se genera un commit nuevo C5, después de C4, que aplique ese PATCH

    Habitualmente se usa en 2 escenarios:
        - Para recuperar cambios previamente revertidos (git revert)
        - Para traer cambios que se hayan realizado en otra rama.

## REESCRIBIR EL HISTORIAL (REPO)

IMPORTANTE:     Solo debemos realizar estas operaciones cuando no haya compartido los commits con otras personas.
                En general antes de hacer un push... aunque si he hecho un push solo para mi, tampoco es problema
                El problema es si hago un push que optros puedan descargar... y de hecho lo han descargado = PROBLEMA !!!!!

- commit --amend

    Es la opción más sencilla para reescribir el historial. Permite reescribir el último commit:
        - Si no quiero reescribir el mensaje de confirmación, añadiré el parametro --no-edit

    Es útil en diferentes escenarios:
        - Si genero commits para tener puntos de restauración 
        - Si genero commits para tener copias de seguridad
        - Si genero un commit y me faltan cosas
        - Si genero un commit y meto mal el mensaje 
        - En general cuando quiera reescribir el último commit

- git reset     Permite eliminar los últimos n commits del historial. No commits a discrección, no puedo borrar uno concreto.
                **De hecho, lo que me permite es volver a un commit, borrando todos los que hay detrás de ese.**
                Básicamente estoy borrando las copias de seguridad / instantaneas / fotografías que he ido haciendo de mi proyecto. Esto no implica por necesidad que estemos borrando cambios en nuestros archivos.

                CARPETA                 AREA DE PREPARACION         REPO LOCAL
                (working dir)           (staging)                   (.git)
        soft                                                            √
        mixed*                              √                           √
        hard         √                      √                           √

        * Por defecto hace mixed

    Es útil en diferentes escenarios:
        - Tirar a la basura todo lo que he hecho desde un punto
            git reset --hard 
        - Consolidar varios commits en uno
            git reset --mixed o --soft 

- git rebase --interactive
    ME PERMITE REESCRIBIR EL HISTORIAL A MI ANTOJO. PELIGROSO DE NARICES
    Es útil cuando la he cagao mucho... y tengo un lio de narices en el historial! Cosa que debería evitar a toda costa

    Este comando, a ver si un día le cambian el nombre...
    porque NO TIENE MUCHO QUE VER CON LO QUE VA A ASE RUN REBASE en fusión de cambios

---
CARPETA                     STAGING                     REPOSITORIO
archivo(44)                 archivo(44)                 C1(archivo(1))
                                                        C2(archivo(2))
                                                        C3(archivo(3))
                                                        C44(archivo(44))

---
# RESET Modo mixed

CARPETA                     STAGING                     REPOSITORIO
archivo(44)                 archivo(1)                 C1(archivo(1)) <<<
                            ^^ se deja como está en el commit que restauro

                            ## Sacar cosas del área de staging 

---
# GIT RESET HARD 

CUIDADO, PRECAUCION
SIEMPRE COPIA DE SEGURIDAD
PELIGRO
DANGER !!!!!

CARPETA                     STAGING                     REPOSITORIO
archivo(1)                  archivo(1)                  C1(archivo(1))

git reset --hard C1
gir restore

git restore no borra commits... recupera archivos/caroetas al working dir o al área de staging
    ESA ES SU MISION PRINCIPAL
git reset --hard BORRA COMMITS DEL FINAL DEL HISTORIAL
    ESA ES SU MISION PRINCIPAL

El único punto donde se cruzan ambos comandos sería:

    git reset --hard HEAD
    git restore --worktree --staged --source HEAD -- :/

    Esos 2 comandos son equivalentes: Se cargan los cambios que haya realizado después del último commit
    git reset --hard 

    La diferencia es que mientras que 
    - git restore me permite trabajar a nivel de archivo o carpeta
    - git reset --hard SOLO A NIVEL DEL PROYECTO COMPLETO

## GIT REBASE --interactive

Me deja reescribir completamente el historial de mi proyecto.
Siempre copia de seguridad... La riego a la mínima.

C1  Lo mantengo.. ni lo toco
    <- rebase
C2          ME lo quedo tal cual
C3          Lo junto con el anterior, y le añado el mensaje del C3
C4          Lo junto , olvidandome del mensaje
C5          Me lo quedo, pero le cambio el mensaje
C6          A la basura
C7          Me lo quedo, pero le cambio el mensaje

C1  commit1
C2  commit2 + commit3 (además incluye el cambio del C4)
C5  nuevo mensaje
C7  nuevo mensaje

---

# RAMAS

Linea de evolución (paralela en el tiempo a otras) del código de mi proyecto.

Más importante... y crítico en todo proyecto es definir un GIT FLOW... Workflow...
En definitiva cómo vamos a trabajar con respecto a las ramas que vamos crando en un proyecto.

Cualquier proyecto de software va evolucionando en el tiempo, dando lugar a diferentes versiones del software.
Dependerá de:
- Complejidad del proyecto
- Número de entornos con los que trabajo
- Número de clientes que tenga, y si controlo lo que se instala en cada uno.
  - 1 cliente ... En un momento dado del tiempo, tendré solo 1 versión del producto en producción. ME QUITA MUCHOS PROBLEMAS
  - n clientes: N= (2-2m) y donde no controlo la versión que mi software que tienen instalada.
El hecho de adoptar un determinado esquema de ramas para mi proyecto y de cómo paso cosas de unas ramas a otras, que es lo que llamamos GIT FLOW.

---
Sistema de cálculo de préstamos hipotecarios.
Le doy los datos de un inmueble -> ME ofrece una estimación de los pagos que tengo que hacer... importe total... intereses.. etc.

- Microservicios en Backend: cálculos ~producción 2 o 3 versiones en paralelo
- App web, que tendrá posiblemente 1 en producción
- Apps móviles:
  - Tendré 500m en producción... donde además no control la versión que se usa.

Script Ansible para automatizar el planchado de una infraestructra en la casa
---

En general, en todo proyecto, siempre vamos a tener 3 ramas:

- principal, master, main: 
    Aquí solo se sube código listo para producción (releases)
    Nunca, jamás, bajo pena de amputación (de las uñas) se debe realizar un commit en esta rama
        A esta rama sólo se traen commits de otras ramas.
- desarrollo:
    En ella hay código que aún no está listo para producción.. y vamos haciendo los cambios pertinentes...
        - añadiendo funcionalidad
        - corrigiendo problemas
        - ...
    Es la rama en la que realmente estamos trabajando la mayor parte del tiempo
- release, intregación, pruebas, qa
    En esta rama se deja código que consideramos listo para producción, a falta de pruebas finales
    En esta rama tampoco hacemos commits... nunca jamás!

PROYECTO A:

 principal                   C3 [1.0.0] ---------- C5[1.1.0]
                            /                     /
 release                   C3 [1.0.0-RC1] ----  C5[1.1.0-RC1]
                          /                     /
 desarrollo ---C1---C2---C3 [1.0.0-RC1]---C4---C5[1.1.0-RC1][1.1.0]---C6

Script powerShell para configurar unos entornos windows que crea 5 usuarios en una máquina, 4 carpetas y les pone permisos.
En este escenario tan sencillo, cada movimiento de commits entre ramas se hace mediante un copiado de commits.
    C3(desarrollo) lo copio a release y lo copio a principal
        Cómo se llama esa operación en git: merge de tipo fast forward

ESTE FLUJO me sirve si YO CONTROLO MI PROYECTO. Estoy montando un software que vendo o regalo a la gente... Y YO CONTROLO lo que voy metiendo en el software.

## Qué me aporta esa forma de trabajo en el día a día? 

- Multiples copias del estado del proyecto en cada momento (versiones)
  Para esto necesito ramas? Las ramas van a ir asociadas a tareas o entornos.
    Lo último que hay en la rama principal es lo que sé que voy a instalar en los nuevos entornos de producción
    Lo último que hay en la rama release es lo que voy a instalar en el entorno de preproducción, integración...
    Lo último que hay en desarrollo es de donde se debe partir cuando se quieran hacer cambios en el proyecto

Pero joder... si estoy trabajando YO y FELIPE en el proyecto.. pues ya sabemos lo que hay y de donde coger cada cosa...
para qué complicarnos con tantas ramas?
1. Hoy es Diego... y mañana? Va a seguir Diego en la empresa? 
   Quizás a Diego le toca la lotería y se pira a las candongas!!!!
   Diego no quiere estar recibiendo puñeteras llamdas de Menchu... que le ha tocao ahora pringar en el proyecto que antes llevaba Diego 
   DEJAR UN HISTORIAL **CLARO** DEL PROYECTO
2. Diego... que aún no le ha tocado la loteria al pobre... sigue currando com los demás, como un pringao... y quiere 
   dentro de su pringadez trabajar lo emnos posible, dicho de otra forma, querrá automatizar trabajos.
   Por ejemplo: Las pruebas que se hacen sobre su programa (script)
   Por ejemplo: La distribución de ese script.


Script Ansible (playbook)
Lo tendré en mi máquina ... en desarrollo... y lo probaré... a ver si funciona... 
Pero cuando funciona, lo quiero que se ejecute en 500 entornos... o en el mismo entorno 500 veces.
Configuro en el Ansible Tower ese playbook... y le digo que lo ejecute todos los dias a las 3:00
Qué versión el digo al ansible tower que ejecute?
    Si configuro la 1.0.0... cuando saque la 1.1.0 qué pasa?  voy jodido... ya me toca entrar en el tower y cambiarlo.
    Coño los 5 minutitos del cigarro...
    Podría haber configurado otra cosa: 
        - Toma la última versión de mi repo de git en la rama principal!


PROYECTO B: Donde los clientes deciden qué se instalan en producción. No es mi decisión.

 hotfix                                             C5[1.1.0]--C8[1.1.1]--------------------
                                                    /                                       \
 principal                   C3 [1.0.0]            C5[1.1.0]                   C7 [2.0.0]   C8[1.1.1]   C9[2.1.0]
                            /                     /                            /                       /
 release                   C3 [1.0.0-RC1] ----  C5[1.1.0-RC1]                C7[2.0.0-RC1]            C9[2.1.0-RC1]
                          /                     /                           /                        /
 desarrollo ---C1---C2---C3 [1.0.0-RC1]---C4---C5[1.1.0-RC1][1.1.0]---C6---C7[2.0.0-RC1]------------C9[2.1.0-RC1]

 - App teléfonos móviles
 - Word

Todavía yo controlo el flujo de mi proyecto... cómo evoluciona mi proyecto.
En un momento dado, puede haber 5 versiones en paralelo del sistema en producción.

Si yo, lo que hago es dejar las versiones que tengo en principal en un REPOSITORIO DE ARTEFACTOS
                                                                        nexus
                                                                        artifactory
                                                                        docker hub
                                                                        una web desde la que la gente descarga mis versiones

Y los hotfixes los llevo a la rama principal Cómo ? MERGE donde se pone ahí por el artículo 33
Y si hay conflictos? No hay conflictos... lo pones. La razón la tiene el que quiere meter algo ahí
---

Cualquier cambio de rama hacia PRINCIPAL o RELEASE debe estar automatizado.



PROYECTO C: Que sigue una metodología AGIL

 principal                   C3 [1.0.0] ----------------- CA3
                            /                            /
 release                   C3 [1.0.0-RC1]               CA3     CFAB        CFABC        Pruebas de Sistema
                          /                            /        /            /
 desarrollo ---C1---C2---C3 [1.0.0-RC1]-------------- CA3 ---- CFAB-------CFABC
                          \                           * \      * \         *
 rama tarea 1               C3 ----CA1 --- CA2 ---- CA3  \    /   \       /   Pruebas unitarias
                             \                            \  /     \     /
 rama tarea 2                 C3 ---- CB1* --- CB2*-------CFAB**    \   /      Pruebas unitarias*    Integración**
                               \                                     \ /
 rama tarea 3                   C3 ---- CC1 ---------------------CC2--CFABC   Pruebas unitarias*    Integración**


ESE GITFLOW, refleja la realidad de lo que ha acontecido. ESTO NO HAY UN DIOS QUE SE ENTERE !!!!!!!
Mi cerebro no da!...
Que es el commit CFABC? El trabajo en este git flowl, lo hemos realizado mediante merge, fusiones de cambios que generan commits.

A VECES lo que me interesa no es saber lo que ha ocurrido en el proyecto... o al menos, no a todos los niveles...
Lo que me interesa es SABER que hemos ido metiendo en cada momento. Y ESO ES LO QUE NOS DA UN REBASE!

Las metodologías ágiles se caracterizan por entregar el producto al cliente de forma INCREMENTAL !
Dia 0 empieza el proyecto
Dia 20, primera instalación en producción del proyecto. Te pongo solo la pantalla de login
Dia 40, segunda instalación en producción del proyecto. Te pongo además el formulario de alta de expedientes
Dia 60, segunda instalación en producción del proyecto. Te pongo además el formulario búsqueda de expedientesexpedientes

En metodologías ágiles, usamos el concepto de ITERACION (SCRUM: Sprints).
Programa entregas con una periodicidad más o menos fija. CADA 3 SEMANAS !
Y PLANIFICO solamente lo que voy a subir en la próxima iteración: = EXPECTATIVA
- Pantallita de búsquedas ...                   Tarea A
- El informe en PDF                             Tarea B             NO CARBURA
- Gráfico de situación de los expedientes       Tarea C             NO CARBURA 

REALIDAD Solo tengo funcionando la pantallita de búsquedas. ES LO QUE HAY
---

No hago ni un merge en desarrollo.
Todos los merge los hago en sus respectivas ramas contra desarrollo primero.
Es decir, me fuiono lo último que haya en desarrollo en mi rama concrete... Y cuando resuelva la mierda, lo pongo en desarrollo. Desarrollo no es todo lo que estoy haciendo...sino lo que tengo hecho que va bien!
Lo que hace cada uno, en su rama!

---

# Versiones de software

1.2.3 de un software

            Cuando se incrementan?
MAJOR 1     Cuando hay un cambio que no respeta retrocompatibilidad. No te aseguro que lo que estaba funcionando antes, te 
            siga funcionando
MINOR 2     Nueva funcionalidad
            Funcionalidad marcada como obsoleta @Deprecated
                + puede contener también arreglos de bugs
PATCH 3     Cuando se arregla un bug -> fix... o varios

ansible:
    datos de entrada:
        - nombreUsuario: Felipe
              vvvv
        - userName: Felipe

---
PRUEBAS DE SOFTWARE:
    Las pruebas en el mundo del software se clasifican de diferentes formas

Tipos de pruebas:

- En base al nivel de la prueba:
  - Unitarias       Prueba un componente AISLADO del sistema
  - Integración     Prueba la COMUNICACION entre componentes
  - Sistema         Prueba el COMPORTAMIENTO del sistema en su conjunto
  - Aceptación      Las que me piden

- En base al procedimiento de ejecución:
  - Estáticas, que no requieren poner en marcha el programa para su ejecución
    - Pruebas de calidad de código: SonarQube
  - Dinámicas, que si lo requieren (darle a play)
    - Funcionales
    - No funcionales
        - Estrés
        - Carga
        - HA
        - Rendimiento
        - UX

Me fío de las pruebas que se hacen en la máquina del desarrollador? Ni de coña!!!!
    Por qué? Porque está maleao'
Me fío de las pruebas que se hacen en la máquina del tester? Ni de coña!!!!
    Por qué? Porque está maleao'
Me fío de las pruebas que se hacen en el entorno de integración? Uuuuuuuu!!!!!
Depende...
    de lo maleao que esté... El día 1 estará FETEN !!!! CANELITA EN RAMA !!! GUAY !!!
        Después de haber hecho 57 instalaciones? Maleao''''  Esta ha sido siempre mi mejor opción en cualquier caso!
    Hoy en día la tendencia es a tener entornos de integración de USAR y TIRAR (contenedores)

---

Monto un tren:

MOTOR       Prueba unitaria
                VVVV        El motor es capaz de "comunicar" el movimiento a las bielas? SI  IDEAL ! Integración
BIELAS      Si les pego una ostia se mueven o no? SI GUAY !!
                vvvv        Comunican el movimiento a las ruedas: SI  ESTUPENDO 
RUEDAS      Si le arreo, gira? Si gira!! COJONUDO !!
                ^^^^        Comunican la presión a las ruedas? SI !!!
FRENOS      Si les meto corriente, cierran las pinzas SI !

    Va pa'lante el tren (COMPORTAMIENTO) Pruebas de sistema

Pruebas de aceptación del tren... y dan bien... necesito hacer más pruebas? NO 
Qué tren??? Si solo tengo un motor, unas ruedas y un freno... y una bielas??? Qué tren? 

---

Fusiones de cambios:

TIPO 1: FAST FORWARD

    rama A --- C1 ---- C2 ---- C3 ------- C4 ------- C5
                                \ (1)     /        / (2a)
    rama B                       C3 ---- C4 ---- C5

        (1) Creación de una rama

        (2a) Fusión de cambios: Realmente hay algo que fusionar? No hay nada que fusionar...
            Solo hay que copiar commits de una rama a otra: MERGE DE TIPO FAST FORWARD
            En ocasiones (sobre todo trabajando con las ramas pincipales de un proyecto)
                solo queremos que se realicen este tipo de merge

TIPO 2: MERGE MEDIANTE ESTRATEGIA RECURSIVA

    rama A --- C1 ---- C2 ---- C3 ---- C6 ----------- CFC
                                \ (1)               / (2b)
    rama B                       C3 ---- C4 ---- C5

        (1) Creación de una rama

        (2b) Fusión de cambios: Realmente hay algo que fusionar? Si hay algo que fusionar...
            Hay que generar un commit nuevo, un commit de fusión de cambios: MERGE MEDIANTE ESTRATEGIA RECURSIVA!

    Habitual es mezclar los anteriores:

        rama A --- C1 ---- C2 ---- C3 ---- C6 ------------\----C7           Si la rama A es de mayor entidad que la B
                                    \ (1)              (2b)\  /(2a)
        rama B                       C3 ---- C4 ---- C5 ---- C7

    Por qué este caso es importante... o esta forma de trabajar, cuando la rama A es de mayor relevancia que la B?
        Si la jodo en esa fusión... y me da conflictos, cómo queda la rama A? Bloqueada hasta que resuelva los conflictos

    Esta forma de trabajar genera un historial REAL del proyecto. REFLEJA LA REALIDAD



division         C(Suma)------  C(Resta) ---- C(division)  --------------CI(RyMyD)
                 /                                                      /        \
master -----C(Suma) ------ C(RestaArreglada)  ---------------------C(Integra RyM)-CI(RyMyD)
                 \                                              \ /
multiplicacion    C(Suma)------  C(Resta) ---- C(multiplicacion)- C(Integra resta y multiplicacion)


            C(Suma) 
            C(resta) - master 
            C(multiplicacion) - multiplcacion


division         C(Suma)------  C(Resta) ---- C(division) -----------------------------------CFC
                                    |                                                        / 
master           C(Suma) ------ C(Resta) ---- C(multiplicacion) -- C(RestaArreglada)--C(Integra RyM)(Integra RyM)




--- G

# Gestión de ramas

git branch es el comando que permite gestionar ramas

git switch el comando que me permite cambiar entre ramas
    admite el parámetro -c RAMA     que permite crear una rama y colocarme en ella
    antiguamente usábamos el git checkout para ello

---

division        CSuma -- CResta -- CDivision  
                /                       \   merge               TENGO LA REALIDAD
master       CSuma ----- CArregloResta --CF
                                \           rebase              TENGO LA FUNCIONALDIAD
                multiplicacion  CResta2 -- CMultiplicacion2

La idea es:

desarrollo -C1------------------CA2 -------------------------- CB3
             \               FF/  \                           /
tarea 1       C1--- CA1 --- CA2    \
                                    \        
                        tarea 2     CA2 -- CB1'--- CB2 --- CB3
                                      \
                    tarea 3           CA2 ---- CC1'
