# Tags

Nos permiten identificar CLARAMENTE COMMITS del historial.

Quiero extraer del repo TAL commit para:
- Su instalación    v1.5.7
- Pruebas           v2.0.0-RC
- Hotfix            v1.0.7

                 resta
        suma     suma
suma    +resta  +multiplicar
v       v       v
C1--->-C2------>C3--->C4
                       ^
                        Este commit contiene los cambios que se han ido realizando en los anteriores
                        TAG: v.1.1.0
                        Se asocia a una versión: Y puedo haber ido generando una versión que recoja 17 funcionalidades ... independientes entre si.

git tag -l
git tag -a "TAG" -m "descripcion"
git tag -d

Para git, internamente, los tags se gestionan como ramas inmutables.
- git clone URL destino
- git clone --branch desa     --single-branch
- git clone --branch v2.0.1   --single-branch
- git switch rama
- git switch v1.2.3

Por defecto los tags no se suben al remoto, cuando hacemos un git push.
Con las mismas, los tags no se descargan al hacer un fetch en automático.

En ambos casos hay que pedirlo expresamente.

git push --tags
git fetch --tags
git pull --tags

git push v1.1.0

---

En git, ayer vimos que podemos trabajar con varios remotos. Para qué?
- Que una persona pueda tener su propio repo remoto para sus copias de seguridad de sus ramas, que no sube al remoto del proyecto global
- Puede ser que internamente en la empresa use un repo para desarrollo... donde tengo tooodos los commits.
  Pero a los clientes les doy otro repo remoto, donde subo solo algunas ramas.

---

Sistema simulación créditos
- Proyecto, con su(s) propio(s) repo(s) Microservicios en Backend - JAVA Springboot
    Yo controlo lo que va a producción (Entorno de producción es uno o varios servidores dentro de la empresa)
- App Web, ReactJs
    Yo no controlo nada del entorno de producción
- App iOS, Swift
    Yo no controlo nada del entorno de producción
- App Android, Kotlin
    Yo no controlo nada del entorno de producción


MICROSERVICIO (Hooks de git)


master                                                                                C10'' [v1.0.0]    (De aquí saco los commits que instalo)
                                                                                     /
release                                          C8' [v1.0.0-RC1]                  C10' [v1.0.0-RC2]   (De aquí saco los commits que meto en un entorno de pre, 
                                                /                                  /                      para pruebas de sistema End2End y aceptación)
                                               /                                  /
desarrollo---> C1 --> C2 ---> C3 --> C4 ----> C8 ------> C9 [v1.0.0-dev] -----> C10 [v1.0.0-dev]     (De aquí saco los commits que meto en un entorno de integración... 
                      \              /\       /                                                        para pruebas de integración))
                       \            /  \     /
    rama1               C2 ------> C5   \   /
                         \               \ /
    rama2                 C2 --> C6 ----> C7


MICROSERVICIO JAVA -> Herramienta de automatización de tareas en desarrollo : MAVEN, gradle, sbt
FRONTAL WEB        -> Herramienta de automatización de tareas en desarrollo : npm
Microsoft .net     -> Herramienta de automatización de tareas en desarrollo : dotnet (nuget)
Despliegue en Kubernetes -> Herramienta de automatización de tareas en desarrollo : helm
---

# Tipos de software

- Aplicación
- Librería
- Demonio
- Servicio
- Script
- Comando

- Driver
- SO


---

# Submodulos de git

Es una referencia a otro repo de git que meto en una carpeta de un repo de git.
Para que sirve esto.

Esta funcionalidad se superpone entre herramientas.

Estoy montando un proyecto en la empresa:
- Aplicación WEB (ReactJs) para el sistema de cálculos de créditos.
    Quizás hay una serie de componentes WEB creados a nivel corporativo, que reutilizo en el proyecto.
- Backend Microservicios de ese sistema...
    Quizás hay una librería desarrollada en la casa que me permite conectar con el sistema de Autenticación de cuentas (KeyCloak, ActiveDirectory)
- Montando un despliegue de infra en IBMCloud, AZURE para albergar esos microservicios: Scripts terraform 
    Pero... quizás en la casa hay una seríe de modulos de terraform prediseñados para crear redes... hosts...
- Planchando esos Entornos que acabo de contratar en IBM Cloud, les quiero dejar con una paquetería instalada y una configuración determinada: Playbooks Ansible
    Pero quizás en la casa hay unos ROLES de ansible que me permiten la instalación de un sistema de monitorización

En general, nos estamos encontrando con un problema que es la GESTION DE DEPENDENCIAS 
Tengo un proyecto que depende de otros proyectos.

Tanto el proyecto que YO ESTOY DESARROLLANDO, como el proyecto en el que me baso, están en sendos repos de git
Pero para mi proyecto, necesito tener disponible el otro proyecto, del que dependo.
En cada lenguaje se SUELE dar ya una solución a este tema.

Librería base: JAVA -> maven -> Publicar un artefacto (libraría JAVA) -> NEXUS / Artifactory
Proyecto nuevo de alto nivel -> JAVA -> Maven (dependencia) ----------->

En el mundo .net: IGUAL : Nuget

React: Javascript: NPM 

Cuando tengo una herramienta que me gestiona dependencias, es ideal, ya que las dependencias son multinivel.
Pero hay lenguajes... programas que no tienen esto muy resuelto y en este caso, los submodulos de git, nos echan una mano

TERRAFORM
    script de terraform parta desplegar TODA una infra                  REPO 1 de git
        MODULO: RED IBM Cloud                                           REPO 2 de git

ANSIBLE
    script de ansible (playbook) instalaciones y configuración en un entoprno           REPO 1 de git
        ROLE: Instalar ciertos paquetes y configurarlos                                 REPO 2 de git
    Los roles se buscan o en las ubicaciones definidas en la variable de entorno ANSIBLE_ROLES_PATH
        o en la subcarpeta "roles" de mi proyecto                                                           <<<< SUBMODULES de git

    
SUBMODULOS:

Un submodulo es una carpeta que apunta a un COMMIT Concreto de otro REPOSITORIO
Por defecto los submodulos no se clonan con el repo.
- Se lo puedo pedir explicitamente mediante el argumento --recurse-submodules
- O una vez clonado un repo ejecutar:
  - git submodule init <<< Para que lea el fichero .gitmodules
  - git submodule update <<< para que los descargue

El módulo queda siempre en estado dettached, desasociado, de forma que aunque salgan nuevas versiones de la ibrería
NO SE ACTUALICEN EN AUTOMÁTICO <<<< ESTO ES ESENCIAL 
Para decir la versión que uso:
    - cd <carpeta del submodulo>
    - OPCION 1
      - git fetch                        # Actualizarme a lo nuevo que haya de la librería
      - git log --oneline --all          # Ver todas las versiones (si no lo tengo claro)
    - OPCION 2
      - git fetch v1.2.7
    
    - git checkout v1.2.7 | git switch v1.2.7
    - cd ..                             # Volver a la carpeta del proyecto padre
    - git add <carpeta submodulo>
    - git commit -m "Nueva versión de la librería"
  
Para añadir un submodulo:
    git submodule add URL_REMOTO carpeta



----

Terraform me permite gestionar (crear, mantener o desmantelar) infras en clouds.

script = carpeta
    ficheros.tf

    En algún caso nos pasa que recursos que quiero crear en un cloud, se me complica la configuración ...
    Y hay "librerias" que lo resuelven. -> Modulos

    Module{
        source = "git::http://miservidor/mimodule?tags=v2.3.4"
    }

Un script que monto en terraform, para el despliegue de una infra, lo ejecutaré para desplegar 1 única infra o varias?
Puede ser que lo uquiera desplegar en un unico entorno...
Pero puede que me intere desplkegar en varios entornos.... distintos clientes.

Cliente1: ---> En 3 minutos le tengo una infra para él (terraform)... y le despliego en esa infra el producto (ansible)
Cliente2: ---> En 3 minutos le tengo una infra para él (terraform)... y le despliego en esa infra el producto (ansible)
...

Evidentemente,. distintos clientes podrán tener distintas configuraciones de entorno.
Cliente 1: un cluster de 2 máquinas para HA... pero no hay muchos usuarios haciendo peticiones
Cliente 2: un cluster de 8 máquinas para HA y ESCALABILIDAD... ya que hay muchos usuarios haciendo peticiones

Esto implica que tendré distintos ficheros de configuración (variables/parámetros) para cada cliente

En paralelo, terraform me genera un fichero (.tfstate) donde guarda datos de los entornos que ha ido creando en los clouds)
Cliente1: .tfstate
    MAQUINA1: IP 172.29.213.28
    MAQUINA2: IP 172.29.213.21
Cliente1: .tfstate
    MAQUINA1: IP 172.29.215.28
    ...
    MAQUINA8: IP 172.29.215.21


proyecto terraform
    ficheros .tf
    carpeta entornos/
        cliente1/
            variables
            .tfstate
        cliente2/
            variables
            .tfstate

Quiero 1 único REPO en git para esto? NO ni de coña!
Quiero mezclar los datos de un cliente con los de otro? NO
De hecho, puede ser que la persona resposnable de tocar los ficheros de 1 cliente sea diferente de la persona con permisos para el otro cliente.

REPO1: Script

REPOs: ENTORNOS
    Cliente1 ---> submodulo que apunta al REPO1(v1)
    Cliente2 ---> submodulo que apunta al REPO1(v2)
    ...

Terraform: Una herramienta que permite INFRAESTRUTURA COMO CODIGO (la infra no es solo que esté en ficheros... sino que tiene versiones.)

PRODUCTO                                                            INFRA
Version 1.0.0 de mi producto                                        version 1 de la infra
    vvv
Versión 1.1.0 de mi producto                                        version2 de la infra + host(s) para un cluster de KAFKA
    Se usa un sistema de mensajería (KAFKA) 

---

# Notas de git

Suele ser una funcionalidad poco usada, porque solemos usar esa funcionalidad desde otras herramientas...
la cosa es si realmente me interesa que eso sea así.

Una nota es un texto que puedo asociar a un commit.
Un tag es un IDENTIFICADOR
La nota está pensada para meter 500 frases...
Pero eso de alguna forma es lo que tenemos en los mensajes de confirmación de los commits...
La descripción del commit la meto en el mensaje de confirmación del commit!

Ahora bien... Cuando meto un mensaje de confirmación de un commit?  Cuando genero el commit...
Y lo puedo cambiar a posteriori? MALAMENTE !
Por qué malamente?
- 1º Se reescribe el commit y cambia su ID
- 2º No se propaga por las ramas. 
- 3º No es fácil al cabo de un tiempo

Hay veces que me interesa meter información adicional a un commit tiempo después:
- Detallar un bug que hay en un commit
- Mandar información a otros sistemas o personas
- Añadir el resultado de unas pruebas que se han ejecutado sobre una versión

git notes add -m "nota" COMMIT_ID
                        TAG

git log --show-notes

git notes edit COMMIT_ID
               TAG
          remove



BitBucket  ~ JIRA


---

REPO principal del proyecto
 
master                                  ---Liberar----          <<<< Sin humanos
                                            ^
release                            ----Pruebas finales------    <<<< No humanos
                                    ^
desarrollo ----Nueva funcionalidad-----                 <<< Es gestiona por 2 personas (Linux Torvals en Linux)

En este repo quiero un historial limpio, claro, controlado

Y somo 20 personas trabajando en el proyecto.
De esas 20, 10 senior
            10 juniors
    En paralelo: 10 cuidadosos
                 10 que van a lo loco

----

Bueno... cuando haya que hacer algo: Que se hagan un fork del proyecto... que no un clone

# FORKEAR un proyecto

Hacer una copia de un repo, que sea propiedad de otra persona (equipo)

Característica 17 ---> Forkeate el repo

En ese nuevo repo, podré hacer lo que quier... pero....
En un momento dado, acabaré con un commit que tenga los cambios necesarios.
Y ese commit le quiero llevar de un proyecto (repo) a otro.
Le quiero devolver al proyecto original -> desarrollo

Y en ese momento lo que hago es solicitar un MERGE REQUEST

Esa solicitud de merge es recibida por los mantenedores del proyecto ORIGINAL y deben procesarla.



---

Evitar conflictos en el código cuando varios tocamos un archivo:

1º No añadir cosas al final del fichero... CONFLICTOS SEGURO !
Lo mejor entre medias:

    def funcion1():

    ## fin de la funcion 1

    def funcion3():


    function funcion2(){

    }// final funcion 2

2º Que haya una linea clara de cierre de funciones

funcion1 {
    tarea1
} 

----

funcion1 {
    tarea1
}

funcion 2{

}

----


linea1
Ana
linea2
linea3
Ilya
linea4
Diego
linea5

