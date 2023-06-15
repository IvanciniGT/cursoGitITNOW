# Trabajo con ramas

## Fusiones de cambios

- Merge
  - Fast forward
        COPIA COMMITS (ideal) 
  - Estrategia recursiva \                          <<< Merge tradicional
        GENERA UN COMMIT DE FUSION DE CAMBIOS (Sencilla de entender a priori)
            En el historial del proyecto nos vuelve locos
  - Estrategia octupus   / Generan commit
- Rebase
        CONCEPTUALMENTE ES MAS COMPLEJO: 
            No genera commits nuevos.. pero reescribe los commits que llevo 

    C1---C2---C3        MERGE          C1---C2---C3 -C4 -C5
     \                                  \         
      C4---C5                            C4---C5--

    C1: archivo1(1)
        archivo2(1)

    C2: archivo1(1)
        archivo2(2a)
    
    C3: archivo1(2)
        archivo2(2a)
    
    C4: archivo1(1)
        archivo2(2b)
    
    C5: archivo1(1)
        archivo2(3b)

    CF: archivo1(2)
        archivo2(2a+3b) <<< CONFLICTOS

    C1---C2---C3        REBASE          C1---C2---C3
     \                                             \       
      C4---C5                                       C4'---C5'


    C4': archivo1(2)
         archivo2(2a+2b=r1)    <<<< CONFLICTOS
    
    C5': archivo1(2)
         archivo2(2a+3b=r2)    <<<< CONFLICTOS


    desarrollo ------------- F1 ----- F2 ----- F3----------CFX
       \                    / merge --squash               / ff
        \ rama F1-C1-C2-C3-/   \                          /
                                \                        /
                                 \ rama F2 -C1'-C2'-C3'-C4'

## Ramas comunes:

- master / main:
  - Nunca creamos commits
    Si hacemos un merge en esta rama... si es un merge tradicional.... genera commit
    Deberíamos hacer solo fast-forward
  - Lo que hay en master se considera listo para producción

- desarrollo
  - Depende del proyecto (tipo, participantes, metodología)

- ramas por características (tareas, requisitos)

- ramas de hotfix


version 1.0.0 de mi codigo
    funcionGeneral(a)

tarea 1
    tarea1()
        funcionGeneral(a, t1)

    funcionGeneral(a, t1)
        ....
        + t1

tarea 2
    tarea2()
        funcionGeneral(a, t2)

    funcionGeneral(a, t2)
        ....
        + t2
---
    funcionGeneral(a, t1, t2)
        ....
        + t1
        + t2


Despues del desarrollo y pruebas (y antes de integrar) existe una tarea llamada: refactorizado


    empiezo   resta
---C1---C2---C3---
    |    suma    |
    |            v junto la suma, la resta y la multiplicacion
    ----C4-------CF (conflicto)
        multiplicacion
                c1 estoy con la fusion... a medias... me voy a comer
                c2f estoy ahora que no se como va
                c3f casi casi


me voy a la rama desarrollo y ejecuto

git pull:
    git fetch 
    git diff desarrollo remote/desarrollo
    Miras si tu has metido commits en desarrollo, que ahora nos la lien con lo que hay en desarrollo
    Y decido como hacer la fusión de esos cambios:
        merge (Y dejar constancia de la integración)
            Si el problema no me afecta solo a mi
        rebase (y cambiar mi código y no dejar constancia de la integración)
            Si el problema me afecta a mi
    El problema es que un git pull, trabaja o bien con merge o bien con fetch en función de como tengas configurado git para tu proyecto en TU MAQUINA
    Antiguamente todo git pull = se reolvía mediante merge
    En la última versión de git, si no tengo configurado la estrategia de fusion de cambios en pull (merge o rebase)
    - me da un warning o directamente me peta hasta que lo configure

git merge ANA_RAMA --ff-only

---

# Repos remotos ?

- Es un repo común, compartido, para sincronizar






En la carpeta raiz:
    mkdir repo_remoto
    cd repo_remoto
    git init --bare

En la carpeta raiz (cd ..)
    git clone repo_remoto repo_local_1
    cd repo_local_1
        // Creado archivo1.txt
    git config user.name "Ivan"
    git config user.email "ivan@miemail.com"
    git add .
    git commit -m '"primero commit"
    git push

En la carpeta repo_remoto
    git log --oneline


    Por defecto un git pull es
    lo mismo que ejecutar:
    1º git fetch
    2º git merge origin/rama


---
# Identificación

Saber quien eres <<< Esto lo hace git cada vez que da de alta un commit

# Autenticación

Asegurarme que realmente eres quien dices que eres (token de seguridad, certificado, contraseña, características BIOMETRICAS)
Git no autentica                                                ^                       ^
Los servicios de alojamiento de remotos suelen autenticar       github                gitlab = bitbucket

# Autorización

Sabiendo   y confirmado quien eres... es decir, una vez te he autenticado, miro si te dejo hacer lago o no!
Esto también lo ahcen los servicios de repos remotos, como gitlab

---
# Remotos:
## Gestion de remotos:
git remote                  lista de remotos
            add     nombre URL
            remove  nombre
            rename  nombre
            get-url nombre

## Enviar a un remoto una rama !
git push

## Traerme una copia de las ramas que hay en el remoto
git fetch

De la rama remota, copiaré commits a mi rama: git merge / rebase
Que opcionalmente puedo ejecutar en automático con un git pull



Cuando vaya a hacer un commit en una rama que está en un remoto, siempre lo primero un pull, para evitarme problemas
Y desde lo último que hay en el remoto, hago mi merge o mi rebase

---

# Repositorio

Almacen en mi local de ramas, las cuales guardan commits.

# Commit

Una copia de seguridad (foto) de lo que tengo en stage en un momento dado

# Patch (en git)

Un paquete de cambios (lo que otros sistemas de control de versión guardan en un commit)
Que git calcula de forma dinámica

# stash

Almacen en mi local de qué? patches

git stash list      Ver lo stash que tengo. Que pueden ser muchos
git stash save MENSAJE Me permite crear un nuevo stash, recogiendo los cmabios que ahora mismo tengo  y poniendo mi propio mensaje

git stash           Igual que el anterior, pero me pone un mensaje por defecto

git stash show   STASH_ID    Muestra los cambios en un stash
                    ^ 
                    Si no lo pongo, se toma el último
                    v
git stash apply  STASH_ID    Aplica los cambios que hay en un stash
git stasg drop  STASH_ID   Elimina un stash que tenga guardado

git stash pop STASH_ID = git stash apply + git stash drop 
git stash push = git stash 


mkdir stash
cd stash
git init
// Creo el archivo1.txt
git add .
git commit -m 'primer commit'
// Modificar el fichero
2 opciones
    OPCION A:
        git add archivo1.txt
        git commit -m 'mensaje'
    OPCION B:
        git commit -am 'menaje'


# CASOS DE USO DE UN STASH

- Si estoy currando en una rama y estoy a medias y me tengo que ir a otra rama
- Estoy haciendo unos cambios en una rama y por despiste resulta que debía estar trabajando en otra rama
- Me piden que hay algo mal en produccion... HOTFIX
  Pero también lo tengo mal en el codigo actual
- Si me meto en un charco... no se si voy bien
  Miro a ver si el problema lo puedo resolver por otro lado... sino vuelvo

# Tags

Identificadores públicos asociados a los commits
Los identificadores internos que usa git, son secuencias de caracteres que no me empapo... no me lo puedo aprender.
Al commit a029478459823, identificalo también por el nombnre: MIVERSION

git tag -l          Muestra los tags existentes
git tag -a MITAG COMMIT_ID

git checkout MITAG
git switch MITAG


                                
                            v1.1.2 (esto lo quiero en master?)***
                            /
------v1.0.0----v1.1.0----v1.1.1----v2.0.0

desarrollo --------------------------v2.1.0--- 
                                              ^ Cherry pick v1.1.2
                                              ^ Stash apply

*** SI: Si el problema continua en master
    NO: Si el problema no continua en master

    hotfix                                   1.1.2
    hotfix                          1.1.1    |
                                    |   \    |
    hotfix         C1--CHF1         |    \   |
                    1.0.0  \        |     \  |
    master --------C1------CHF1   1.1.0--- 1.1.1---2.0.0----2.1.0
    desarrollo ----C1---C2---C3  /----------------/-----------/
        1.1.0 (a medias)                                ^
                                                    He aplicado un patch
                                                    de la 1.1.2

    Sale un error en 1.0.0 que no puede esperar 


Para subir algo a producción, lo tengo que meter en master?
Tendré que hacer lo que se haya estipulado en el proyecto.
Cuantos clientes tengo? 300 

Y no tengo un sistema de despliegue continua.
Lo que tengo es un sistema de entrega continua.
    Tendré un autyomatismo que me lo publique en una web
        ---> v1.0.1


Se detecta un error en v1.0.1, qué hago?
Depende.... cuánta prisa corre?
            cómo voy con lo que tengo en desarrollo?

Si corre poca prisaq o estoy a punto de liberar... dónde arreglo el código?
En la 1.0.1? Ni de coña... en la 1.1.0 -> V1.1.1
Por qué?
    Ya que el cliente se tiene que actualizar, que se actualize al ultimo cambio que tengo, QUE LE SEA COMPATIBLE



git clone REPO --branch v1.1.2 --single-branch