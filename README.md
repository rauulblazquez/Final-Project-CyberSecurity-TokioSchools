RAÚL BLÁZQUEZ MIR
Fecha: 01/02/2026
ÍNDICE
ÍNDICE................................................................................................................................................. 3
INTRODUCCIÓN - DATOS BÁSICOS.................................................................................................... 5
INTRODUCCIÓN - OBJETIVO Y ALCANCE........................................................................................... 6
Objetivo...................................................................................................................................................................6
Alcance (In-Scope)...............................................................................................................................................6
Fuera de alcance (Out-of-Scope).................................................................................................................... 7
Entorno y arquitectura del laboratorio..........................................................................................................8
Sistemas identificados durante la auditoría................................................................................................8
ARQUITECTURA - TOPÒLOGÍA Y SEGMENTACIÓN DE RED.............................................................. 9
Relación del entorno con la práctica realizada.........................................................................................10
Documentación relacionada.......................................................................................................................... 10
Memoria Técnica............................................................................................................................................... 11
RESUMEN EJECUTIVO...................................................................................................................... 12
Estado general...................................................................................................................................................12
Categorías de Riesgo y Razonamientos.....................................................................................................13
Para clasificar los riesgos se emplea una referencia simplificada del estándar CVSS:................13
Tabla resumen de vulnerabilidades.............................................................................................................14
Análisis CIA..........................................................................................................................................................15
Confidencialidad: CRÍTICA....................................................................................................................... 15
Integridad: CRÍTICA................................................................................................................................... 15
Disponibilidad: ALTA..................................................................................................................................15
Resumen de Vulnerabilidades.......................................................................................................................16
Conclusiones del informe ejecutivo.............................................................................................................17
VULNERABILIDADES ENCONTRADAS..............................................................................................18
Vulnerabilidad 1 — LLMNR Poisoning................................................................................................18
Vulnerabilidad 2 — Password in Object Description......................................................................18
Vulnerabilidad 3 — Object with Default Credentials......................................................................19
Vulnerabilidad 4 — Password Spraying.............................................................................................19
Vulnerabilidad 5 — DCSync................................................................................................................... 20
Vulnerabilidad 6 — Golden Ticket........................................................................................................20
Vulnerabilidad 7 — LDAP accesible sin autenticación...................................................................21
Vulnerabilidad 8 — Enumeración de recursos compartidos SMB............................................. 21
Tabla de vulnerabilidades y evaluación CVSS — Red y AD.................................................................. 22
Recomendaciones.............................................................................................................................................24
FASE DE RECONOCIMIENTO............................................................................................................ 26
INFORME TÉCNICO...........................................................................................................................37
LLMNR Poisoning......................................................................................................................................37
Password in Object Description............................................................................................................43
Object with Default Credentials & Password Spray........................................................................46
Explicación de posibles remediaciones...................................................................................................... 49
Deshabilitar LLMNR mediante Group Policy (GPO).........................................................................49
Eliminar datos personales y confidenciales dentro de las descripción.....................................51
Remediación de credenciales por defecto y ataques de Password Spraying......................... 52
Uso de cuentas gMSA para servicios...................................................................................................52
Deshabilitar almacenamiento de hashes débiles (LM Hash)....................................................... 53
Protección de LSASS y credenciales en memoria............................................................................53
Segmentación de red interna real........................................................................................................ 53
CONCLUSIONES................................................................................................................................ 54
ANEXOS.............................................................................................................................................55
Anexo I – Entorno de laboratorio..................................................................................................55
Anexo II – Herramientas utilizadas.............................................................................................. 55
Anexo III – Evidencias técnicas......................................................................................................55
BIBLIOGRAFÍA...................................................................................................................................56
INTRODUCCIÓN - DATOS BÁSICOS
En este proyecto final he tenido la oportunidad de poner en práctica, los conocimientos adquiridos
a lo largo del curso de ciberseguridad con la realización de una auditoría de seguridad y un test de
penetración sobre un entorno basado en Active Directory en el Windows Server 2019.. El objetivo
principal ha sido analizar si dicho sistema era seguro o si se presentaban vulnerabilidades que
pudieran ser aprovechadas por un atacante en un entorno empresarial real.
El escenario planteado se sitúa como profesional encargado de realizar un pentest a una red
corporativa, evaluando la seguridad del sistema de gestión de usuarios y accesos. Para ello, partí
de un entorno previamente desplegado y configurado con vulnerabilidades, lo que me permitió
trabajar en condiciones muy similares a las que se podrían encontrar en una empresa real y
entender mejor el impacto que pueden tener este tipo de fallos de seguridad.
A lo largo del proyecto seguí una metodología progresiva, comenzando por una fase de
reconocimiento y recolección de información, en la que utilicé distintas herramientas para
identificar servicios, usuarios y configuraciones del dominio. A medida que avanzaba en el
análisis, fui detectando diferentes debilidades relacionadas principalmente con la autenticación, el
uso de credenciales inseguras y ciertas malas prácticas habituales en entornos Active Directory.
Las vulnerabilidades encontradas no solo fueron identificadas, sino que también se explotaron de
forma controlada con fines educativos, lo que me permitió comprobar de primera mano cómo
errores aparentemente simples pueden facilitar el acceso no autorizado a cuentas del dominio e
incluso comprometer gran parte de la infraestructura. Esta experiencia ha sido clave para
entender la importancia de una correcta configuración y gestión de la seguridad en entornos
corporativos
Finalmente, el proyecto se completa con una serie de conclusiones y propuestas de remediación
orientadas a corregir las vulnerabilidades detectadas y mejorar la postura de seguridad general
del sistema. Este trabajo no solo refleja el análisis técnico realizado, sino también el aprendizaje
obtenido durante todo el proceso, reforzando una visión práctica y realista de la ciberseguridad
aplicada a entornos empresariales
INTRODUCCIÓN - OBJETIVO Y ALCANCE
Objetivo
En este trabajo mi objetivo ha sido evaluar el nivel de seguridad del dominio CHANGE.local,
desplegado en un entorno de laboratorio controlado diseñado para simular una infraestructura
empresarial real.
A través de esta auditoría he analizado vulnerabilidades técnicas, configuraciones inseguras y
posibles debilidades en los servicios internos del dominio. Mi intención ha sido comprobar hasta
qué punto un atacante con acceso inicial a la red interna podría escalar privilegios y comprometer
el dominio completo.
El enfoque seguido reproduce un escenario realista de intrusión dentro de un entorno corporativo,
aplicando una metodología estructurada y controlada.
Alcance (In-Scope)
Dentro del alcance de esta auditoría se han incluido los siguientes elementos del laboratorio
configurado:
● El dominio CHANGE
● El segmento de red interno 192.168.56.0/24, configurado para simular una
infraestructura empresarial realista.
● El Controlador de Dominio (DC) y los equipos miembros integrados en el dominio.
● Los servicios internos detectados en la red, incluyendo:
○ DNS
○ Kerberos
○ LDAP
○ SMB
○ RPC
○ Otros servicios asociados al funcionamiento de Active Directory
Fuera de alcance (Out-of-Scope)
He excluido expresamente del alcance de esta auditoría los siguientes aspectos:
● Técnicas de ingeniería social o campañas reales de phishing.
● Pruebas sobre terceros o infraestructuras externas al laboratorio.
● Ataques destructivos como denegaciones de servicio (DoS/DDoS), fuzzing agresivo o
cualquier acción que degrade intencionadamente el servicio.
● Escaneos o explotación fuera del rango definido o dirigidos hacia Internet.
Entorno y arquitectura del laboratorio
El entorno utilizado para este proyecto se corresponde con el laboratorio desplegado durante la
práctica, el cual he utilizado como base para realizar todas las fases del test de penetración. Se
trata de un entorno controlado cuyo objetivo es simular una infraestructura corporativa basada
en Active Directory, permitiendo analizar su nivel de seguridad de forma práctica.
Durante la auditoría he trabajado directamente sobre este laboratorio, partiendo de un acceso a la
red interna y siguiendo un enfoque progresivo de reconocimiento, enumeración y explotación, tal
y como se refleja en las evidencias incluidas en el informe.
El sistema objetivo identificado corresponde a un Controlador de Dominio Windows, accesible en
la red interna y perteneciente al dominio CHANGE, sobre el cual se han realizado todas las
pruebas descritas.
Sistemas identificados durante la auditoría
A lo largo de la fase de reconocimiento y enumeración, identifiqué los sistemas activos dentro de
la red del laboratorio mediante técnicas como escaneo de red y detección de hosts activos.
Los sistemas relevantes identificados fueron:
● Controlador de Dominio (Windows Server):
○ IP: 192.168.56.101
○ Rol: Controlador de Dominio de Active Directory
○ Dominio: CHANGE
● Equipo auditor:
○ Sistema operativo: Kali Linux
○ IP: 192.168.56.102
○ Utilizado para ejecutar todas las fases del test de penetración desde la red
interna.
○ Este entorno permite simular un escenario realista en el que un atacante con
acceso inicial a la red puede interactuar directamente con los servicios del
dominio.
ARQUITECTURA - TOPÒLOGÍA Y SEGMENTACIÓN DE RED
La arquitectura del laboratorio se basa en un único segmento de red interno, desde el cual he
llevado a cabo todas las acciones del test de penetración.
Durante la fase de reconocimiento identifiqué los siguientes elementos clave:
● Red del laboratorio: segmento interno donde se ubican todas las máquinas analizadas.
● Servidor objetivo: identificado con la IP 192.168.56.101, correspondiente a un
Controlador de Dominio.
● Dominio Active Directory: CHANGE.
El controlador de dominio ofrece los servicios típicos de un entorno Active Directory, entre los que
se encuentran DNS, Kerberos, LDAP, SMB y WinRM, lo que confirmó que se trataba del núcleo de
la infraestructura y el principal objetivo de la auditoría.
Relación del entorno con la práctica realizada
Todo el análisis descrito en este informe se ha realizado directamente sobre este laboratorio,
utilizando las mismas máquinas, direcciones IP y configuraciones detectadas durante la práctica.
Las evidencias mostradas en las fases posteriores (reconocimiento, enumeración, explotación y
post-explotación) corresponden a acciones reales ejecutadas contra este entorno, y los
resultados obtenidos reflejan el estado de seguridad del dominio CHANGE en el momento de la
auditoría.
Por tanto, el laboratorio representa una fotografía real del entorno analizado, y todas las
conclusiones y recomendaciones derivan exclusivamente de las pruebas realizadas sobre esta
infraestructura.
Documentación relacionada
Para la realización de esta auditoría me he apoyado únicamente en la documentación y recursos
indicados en el propio proyecto. Estas fuentes me han servido como referencia durante la
preparación del entorno, el análisis de las vulnerabilidades y la comprensión del funcionamiento
del sistema evaluado.
La documentación consultada ha sido la siguiente:
● Documentación oficial de Microsoft relacionada con Active Directory.
● Información técnica sobre el script vulnerable-AD, utilizado para la configuración del
laboratorio, han sido bastante más facilitadas por ChatGPT.
● Bases de datos públicas de vulnerabilidades, como CVE y NVD.
● Guías de uso y documentación de herramientas empleadas durante la auditoría..
● Estándares de seguridad relevantes, entre los que se incluyen NIST, CIS y OWASP.
Memoria Técnica
La auditoría descrita en este informe se ha llevado a cabo conforme a los datos recogidos a
continuación:
● Fecha de realización de la auditoría: 01/02/2026
● Ubicación: Barcelona, con control remoto del entorno analizado
● Servicio de aplicación: Auditoría de seguridad / test de penetración sobre un entorno de
Active Directory
Esta información contextualiza la ejecución del proyecto y sirve como referencia técnica para el
desarrollo del análisis presentado en el resto del documento.
RESUMEN EJECUTIVO
Estado general
Tras realizar la auditoría de seguridad sobre el entorno change, he podido comprobar que el
estado general de seguridad del dominio es crítico. Durante el análisis se identificaron múltiples
vulnerabilidades de alto impacto que permitieron el compromiso total del dominio.
A lo largo de las pruebas realizadas fue posible obtener control completo sobre el controlador de
dominio mediante la explotación de vulnerabilidades críticas. Como consecuencia de ello, se logró
la extracción de todas las credenciales del dominio y el establecimiento de persistencia a largo
plazo dentro del entorno.
Este nivel de compromiso supone un riesgo severo para cualquier organización, ya que permitiría
a un atacante acceder, modificar o destruir cualquier información dentro del dominio, así como
mantener acceso persistente incluso tras la aplicación de medidas de remediación básicas.
Categorías de Riesgo y Razonamientos
Para clasificar los riesgos se emplea una referencia simplificada del estándar CVSS:
Categoría de Riesgo CVSS Descripción / Razonamiento
Crítico 8.1 – 10.0
Representa un riesgo
severo y fácilmente
explotable. Debe
abordarse de inmediato
tras presentarse el
hallazgo.
Alto 6.1 – 8.0
Riesgo significativo
con posibilidad real de
explotación.
Debe corregirse lo antes
posible, después de los
críticos
Medio 4.1 – 6.0
Puede ser explotado en
ciertas condiciones. Su
remediación debe
planificarse y no ignorarse.
Bajo 0.1 – 4.0
Difícilmente explotable o
de impacto limitado. Su
remediación puede
programarse a largo plazo.
Informativo 0.0
No representa un
riesgo directo, pero
ofrece
información útil para
reforzar la seguridad.
Tabla resumen de vulnerabilidades
A continuación, se presenta un resumen general de las vulnerabilidades identificadas durante la
auditoría, clasificadas según su criticidad:
Criticidad Número de
vulnerabilidades
Descripción general
Crítica 3 Vulnerabilidades que permiten el compromiso inmediato
del dominio, incluyendo control total del controlador de
dominio y extracción de credenciales.
Alta 3 Vulnerabilidades que facilitan el movimiento lateral, la
escalada de privilegios y el debilitamiento de los
mecanismos de autenticación.
Media 2 Vulnerabilidades que contribuyen a la cadena de ataque y
dificultan la detección de actividades maliciosas.
Total 8 Conjunto de vulnerabilidades identificadas durante la
auditoría del dominio change
Análisis CIA
En este apartado he evaluado el impacto de las vulnerabilidades identificadas sobre los tres
pilares fundamentales de la seguridad: confidencialidad, integridad y disponibilidad, con el
objetivo de entender el alcance real del compromiso del dominio change.
Confidencialidad: CRÍTICA
El impacto sobre la confidencialidad es crítico, ya que durante la auditoría se ha producido el
compromiso total de las credenciales del dominio. Esto ha permitido el acceso no autorizado a
recursos compartidos y a datos sensibles del entorno.
Además, se ha detectado la exposición de contraseñas en texto claro tras el proceso de crackeo,
así como la capacidad de acceder a cualquier sistema del dominio mediante técnicas como
Pass-the-Hash, lo que supone una pérdida total de control sobre la información confidencial.
Integridad: CRÍTICA
La integridad del sistema también se ha visto comprometida de forma crítica. A lo largo del
análisis se ha demostrado la capacidad de modificar objetos dentro de Active Directory, así como
de alterar GPOs y configuraciones de seguridad del dominio.
Asimismo, ha sido posible obtener control total sobre servicios críticos del dominio y establecer
persistencia mediante la creación de tickets Kerberos forjados, lo que permitiría mantener el
control del entorno incluso a largo plazo.
Disponibilidad: ALTA
En cuanto a la disponibilidad, el impacto ha sido evaluado como alto. Las vulnerabilidades
identificadas permiten la interrupción de servicios de autenticación y la denegación de acceso a
recursos críticos del dominio.
Además, existe la posibilidad de ejecutar código malicioso dentro del entorno, incluyendo
ransomware, lo que podría afectar gravemente a la continuidad operativa de la infraestructura.
De acuerdo con este análisis, se determina que el 100 % de las vulnerabilidades identificadas
exponen riesgos en la confidencialidad y en la integridad del dominio, mientras que el 50 % de las
vulnerabilidades suponen un riesgo directo para la disponibilidad del sistema.
Resumen de Vulnerabilidades
Durante la auditoría del dominio CHANGE identifique 8 vulnerabilidades, clasificadas según su
impacto real en confidencialidad, integridad y disponibilidad.
🔴 Críticas (3)
● DCSync: Extracción de hashes del dominio desde el controlador.
Impacto: compromiso total del Active Directory.
● Golden Ticket: Persistencia indefinida con privilegios máximos.
Impacto: control completo y persistente del dominio.
● Object with Default Credentials: Acceso directo a cuentas válidas sin explotación
compleja.
Impacto: acceso inicial facilitado.
🟠 Altas (3)
● Password Spraying: Autenticación en múltiples cuentas usando contraseñas débiles.
● Password in Object Description: Exposición directa de credenciales en el dominio.
● LLMNR Poisoning: Captura de hashes NTLM para crackeo y acceso lateral.
🟡 Medias (2)
● Enumeración LDAP sin restricciones fuertes: Recopilación de información sensible del
dominio.
● Configuraciones débiles en SMB / comparticiones: Facilitó enumeración y movimiento
lateral.
📊 Resumen Final
● 🔴 Críticas: 3
● 🟠 Altas: 3
● 🟡 Medias: 2
Total: 8 vulnerabilidades
Conclusiones del informe ejecutivo
Tras finalizar la auditoría, concluyó que el análisis ha revelado la existencia de vulnerabilidades
críticas que han permitido el compromiso total del dominio. Los principales problemas
identificados durante el proceso han sido la falta de actualizaciones críticas de seguridad, la
presencia de sistemas operativos obsoletos, así como configuraciones inseguras en protocolos
y servicios.
Además, se ha detectado el uso de políticas de contraseñas débiles y la ausencia de controles
de seguridad avanzados, factores que han contribuido de forma directa a facilitar la explotación
del entorno y a acelerar el compromiso del dominio.
El nivel de compromiso alcanzado demuestra que un atacante con acceso inicial a la red podría
obtener control completo sobre toda la infraestructura en un periodo de tiempo reducido. Por
este motivo, resulta necesaria la implementación urgente de las recomendaciones de seguridad
con el objetivo de mejorar la postura de seguridad del dominio y reducir el riesgo de futuros
incidentes.
Durante la auditoría del dominio CHANGE he identificado un total de 8 vulnerabilidades,
clasificadas según su impacto real sobre la confidencialidad, integridad y disponibilidad del
entorno.
VULNERABILIDADES ENCONTRADAS
Vulnerabilidad 1 — LLMNR Poisoning
CVE: No aplica
Puntuación CVSS: 7.8 (Alta)
Descripción:
Detecté que la red permitía ataques de LLMNR / mDNS Poisoning, ya que los equipos respondían
a solicitudes de nombres que no podían resolver. Esto expone hashes NTLMv2 de los usuarios.
Impacto:
Un atacante podría capturar hashes de autenticación y:
● Realizar crackeo offline de contraseñas
● Ejecutar ataques de Pass-the-Hash
● Escalar privilegios dentro del dominio
Riesgo: Alto
Evidencia: Ejecuté responder -I eth0 -v para escuchar tráfico de la red. El sistema respondió a
resoluciones de nombres no autorizadas y pude capturar hashes NTLM que posteriormente
crackee con hashcat, obteniendo la contraseña de ADMINISTRADOR.
Vulnerabilidad 2 — Password in Object Description
CVE: No aplica
Puntuación CVSS: 8.0 (Alta) - Riesgo: Alto
Descripción:
Encontré que algunos usuarios habían almacenado sus contraseñas en el campo “Descripción” de
Active Directory, lo que permite obtener credenciales válidas sin necesidad de exploits complejos.
Impacto:
● Acceso autenticado al dominio
● Posibilidad de enumerar más usuarios y configuraciones
● Escalada de privilegios dentro de la red
Evidencia: Ejecuté enum4linux con credenciales válidas y pude leer los campos Description donde
estaban almacenadas contraseñas. Con estas credenciales, continué la enumeración de usuarios
y recursos.
Vulnerabilidad 3 — Object with Default Credentials
CVE: No aplica
Puntuación CVSS: 9.0 (Crítica)
Descripción:
Detecté varias cuentas que aún utilizaban la contraseña por defecto ChangeMe123!. Aunque
deberían haberla cambiado en el primer inicio de sesión, algunas permanecían activas.
Impacto:
● Acceso inicial al dominio sin restricciones
● Posibilidad de comprometer múltiples cuentas
● Base para ataques de escalada y movimiento lateral
Riesgo: Crítico
Evidencia:
Enumeré los usuarios del dominio y probé la contraseña por defecto. Varias cuentas se
autenticaron correctamente, confirmando la existencia de credenciales iniciales sin cambio.
Vulnerabilidad 4 — Password Spraying
CVE: No aplica
Puntuación CVSS: 9.2 (Crítica)
Descripción:
Realicé un ataque de password spraying usando la contraseña por defecto contra todos los
usuarios del dominio para comprobar la fuerza de las credenciales.
Impacto:
● Compromiso de varias cuentas con contraseñas débiles
● Facilita movimiento lateral y acceso a información sensible
Riesgo: Crítico
Evidencia:
Preparé un archivo con todos los usuarios y ejecuté el ataque desde Kali Linux. Varias cuentas
fueron autenticadas con éxito, confirmando la efectividad de la técnica.
Vulnerabilidad 5 — DCSync
CVE: No aplica
Puntuación CVSS: 10.0 (Crítica)
Descripción:
Ejecuté un ataque DCSync sobre el controlador de dominio para obtener todos los hashes NTLM,
incluyendo la cuenta krbtgt. Esto permite generar tickets Kerberos y mantener acceso
persistente.
Impacto:
● Compromiso total del dominio
● Generación de Golden Ticket
● Acceso a todas las cuentas del dominio
Riesgo: Crítico
Evidencia:
Usé Mimikatz / CrackMapExec para extraer los hashes. Confirmé acceso administrativo completo
al dominio y posibilidad de persistencia a largo plazo.
Vulnerabilidad 6 — Golden Ticket
CVE: No aplica
Puntuación CVSS: 10.0 (Crítica)
Descripción:
A partir del hash de krbtgt, generé un Golden Ticket que permite suplantar a cualquier usuario y
mantener persistencia ilimitada dentro del dominio.
Impacto:
● Suplantación de usuarios de forma indefinida
● Persistencia total ante cambios de contraseñas
● Control completo del Active Directory
Riesgo: Crítico
Evidencia:Generé el Golden Ticket con Mimikatz, accedí a recursos críticos y comprobé que los
privilegios se mantenían aun tras reinicios o cambios de contraseña de usuarios.
Vulnerabilidad 7 — LDAP accesible sin autenticación
CVE: No aplica
Puntuación CVSS: 6.0 (Media)
Descripción:
El servicio LDAP respondía a consultas anónimas, permitiendo recopilar información básica del
dominio sin necesidad de credenciales.
Impacto:
● Obtención de información del dominio y del controlador
● Facilita planificación de ataques posteriores
Riesgo: Medio
Evidencia:
Ejecuté ldapsearch -x -H ldap:/<IP> -s base y obtuve nombre del dominio, controlador, nivel
funcional y particiones del directorio.
Vulnerabilidad 8 — Enumeración de recursos compartidos SMB
CVE: No aplica
Puntuación CVSS: 5.5 (Media)
Descripción:
Algunos sistemas permitían la enumeración de recursos compartidos SMB y usuarios sin
necesidad de privilegios elevados.
Impacto:
● Facilita la fase de reconocimiento
● Permite mapear recursos y permisos de la red
Riesgo: Medio
Evidencia:
Ejecuté enum4linux -a <IP> y pude listar recursos compartidos y usuarios accesibles, obteniendo
información valiosa para el movimiento lateral.
Tabla de vulnerabilidades y evaluación CVSS — Red y AD
ID Vulnerabilidad
Sistema
afectado
Impacto
CIA
CVSS Severidad Evidencia resumida
V-01
LLMNR / mDNS
Poisoning
Red interna
Windows
C:Crítico,
I:Alta,
D:Media
7.8 Alta Capturé hashes NTLM con
responder -I eth0 -v,
crackeé ADMINISTRADOR.
V-02 Password en
campo
“Description”
Active
Directory
C:Crítico,
I:Alta,
D:Media
8.0 Alta Enumeré usuarios con
enum4linux y extraje
contraseñas de campos
Description.
V-03 Cuentas con
credenciales por
defecto
Active
Directory /
Servidores
C:Crítico,
I:Alta,
D:Alta
9.0 Crítica Probé ChangeMe123! en
usuarios del dominio, varias
funcionaron.
V-04 Password
Spraying
Active
Directory /
Servidores
C:Crítico,
I:Alta,
D:Alta
9.2 Crítica Ataque con archivo de
usuarios desde Kali, varias
cuentas comprometidas.
ID Vulnerabilidad
Sistema
afectado
Impacto
CIA
CVSS Severidad Evidencia resumida
V-05 DCSync Controlado
r de
dominio
C:Crítico,
I:Crítico,
D:Crítico
10.0 Crítica Extraje hashes NTLM con
Mimikatz / CrackMapExec,
acceso total al dominio.
V-06 Golden Ticket Active
Directory
C:Crítico,
I:Crítico,
D:Crítico
10.0 Crítica Generé Golden Ticket con
Mimikatz, persistencia y
suplantación de usuarios.
V-07 LDAP accesible
sin autenticación
Servidor
LDAP
C:Media,
I:Media,
D:Media
6.0 Media Ejecución de ldapsearch -x
anónimo, obtuve info del
dominio y controladores.
V-08 Enumeración de
recursos
compartidos
SMB
Servidores
Windows /
NAS
C:Media,
I:Media,
D:Media
5.5 Media enum4linux -a listó
recursos compartidos y
usuarios accesibles.
Recomendaciones
Como resultado de las vulnerabilidades identificadas, se proponen las siguientes medidas
correctivas:
Cambio inmediato de credenciales comprometidas
● Cambiar la contraseña del Administrador de Dominio y de todas las cuentas privilegiadas.
● Realizar dos cambios consecutivos de la contraseña de la cuenta krbtgt, separados por 24
horas, para invalidar cualquier Golden Ticket generado.
Implementar una política de contraseñas robusta
● Establecer un mínimo de 16 caracteres combinando mayúsculas, minúsculas, números y
símbolos.
● Configurar historial y caducidad periódica de contraseñas.
● Implementar detección de contraseñas débiles o comprometidas.
Desplegar autenticación multifactor (MFA)
● Priorizar MFA para cuentas con privilegios administrativos.
● Exigir MFA especialmente para accesos remotos y operaciones críticas.
Aplicar el principio de mínimo privilegio
● Revisar y reducir el número de cuentas con privilegios de dominio.
● Crear cuentas administrativas separadas de las cuentas de usuario estándar.
● Segmentar tareas administrativas con permisos específicos.
Implementar monitorización avanzada
● Configurar alertas para actividades sospechosas en cuentas privilegiadas.
● Revisar periódicamente los logs de eventos de seguridad.
● Monitorizar cambios no autorizados en objetos críticos de Active Directory.
Proteger credenciales
● Activar Windows Defender Credential Guard para proteger credenciales en memoria.
● Limitar el uso de herramientas que almacenen contraseñas en texto claro.
Implementar acceso privilegiado Just-In-Time (JIT)
● Otorgar privilegios administrativos sólo cuando sean necesarios y por tiempo limitado.
● Reducir la exposición permanente de credenciales administrativas.
Configurar segmentación de red administrativa
● Establecer redes dedicadas para tareas de gestión de Active Directory.
● Implementar controles de acceso a nivel de red para la administración de sistemas
críticos.
FASE DE RECONOCIMIENTO
Empezaremos con la fase de reconocimiento con comandos como: nmap, ldapsearch,
enum4linux, crackmapexec, BloodHound.
Tenemos que saber la IP de la máquina víctima con un ipconfig
Una vez obtenida nos vamos a la máquina atacante y ejecutamos el nmap con sus flags/opciones:
nmap: herramienta de escaneo de red. -T4: acelera el escaneo sin ser agresivo.
-sV: detecta servicios y versiones. -sC: ejecuta scripts básicos para la enumeración.
192.168.56.101: máquina objetivo del laboratorio. -Pn: escanea aunque no responda a ping.
-vvv: muestra más detalles durante el escaneo.
Resultado obtenido:
- Es un Windows Server / Controlador de Dominio (Active Directory).
- Tiene puertos clave abiertos: DNS, Kerberos, SMB, LDAP y WinRM.
- Se identifican servicios Microsoft y el nombre del host.
- El escaneo sirve para continuar la fase de enumeración.
Ejecutamos el enum4linux con sus flags:
Utilizamos la opción -a para realizar una enumeración completa del sistema mediante SMB.
Ta
Target: IP analizada (192.168.56.101)
Domain/Workgroup name: Dominio Active Directory → CHANGE
Domain SID: Identificador único del dominio
Host is part of a domain: El equipo pertenece a un dominio AD
Domain Controller: Servidor principal del dominio
Domain Master Browser: Rol de gestión del dominio
Null session allowed: Permite conexión sin usuario ni contraseña
Users enumeration: No permitido (Access Denied)
Groups enumeration: No permitido
Shares enumeration: No permitido
Password policy: No accesible sin credenciales
Ejecutamos el crackmapexec con sus flags:
Mediante CrackMapExec se identificó que el sistema objetivo es un servidor Windows Server
2019 perteneciente al dominio change.me.
El servicio SMB se encuentra activo con SMB Signing habilitado y SMBv1 deshabilitado.
● Dominio Active Directory: change.me (domain:change.me)
● SMB Signing activado protege contra ataques tipo SMB Relay (signing:True)
● SMBv1 deshabilitado (SMBv1:False) (evita exploits antiguos).
Ejecutamos el sudo arp-scan -l para detectar equipos activos:
Identificamos los dispositivos activos en la red local, detectándose tres hosts, incluyendo el
controlador de dominio objetivo y la puerta de enlace de la red.
192.168.56.1 → puerta de enlace / router
192.168.56.100 → equipo activo en la red
192.168.56.101 → servidor objetivo (Controlador de Dominio)
En la siguiente captura hemos realizado una consulta LDAP anónima al controlador de dominio,
obteniendo información base del Active Directory como el nombre del dominio, el controlador de
dominio, los niveles funcionales y las particiones del directorio, confirmando que el servicio LDAP
responde sin autenticación.
Su comando sirve para comprobar si LDAP responde sin credenciales:
-x → autenticación simple (anónima)
-H ldap:/ IP → servidor LDAP
-s base → solo información base del dominio
El resultado del comando resumidamente es lo siguiente:
LDAP accesible: Sí (anónimo)
Dominio: change.me
Controlador de Dominio: RAUL
Nivel funcional dominio: 7 (moderno)
Global Catalog: Activo
Versiones LDAP: v2 y v3
Métodos de autenticación: GSSAPI, SPNEGO, DIGEST-MD5
Particiones AD: Dominio, Configuración, Esquema
Nombre DNS del DC: raul.change.me
Resultado: Consulta correcta
INFORME TÉCNICO
LLMNR Poisoning
LLMNR Poisoning permite capturar hashes NTLM cuando un equipo Windows intenta acceder a
un recurso cuyo nombre no puede resolver.
Ejecutamos el sudo responder -l eth0 -v para escuchar el tráfico de la red y levantar servicios
falsos (SMB, HTTP, etc.), para detectar si la red es vulnerable a ataques de resolución de nombres
(LLMNR / NBT-NS).
Mediante el uso de la herramienta Responder se confirmó que el entorno es vulnerable a ataques
de envenenamiento LLMNR y mDNS, ya que el sistema respondió a resoluciones de nombres no
autorizadas, aunque no se capturaron credenciales durante esta fase.
Explicación de las opciones/flags:
Responder → herramienta de escucha/envenenamiento de red
-I eth0 → interfaz de red utilizada
-v → modo verbose (muestra toda la configuración)
Ahora realizaremos una prueba en la cual pues por x motivo la víctima desea entrar en su recurso
compartido que de ejemplo pondremos el DCCD:
Mientras el response escucha, observamos como hemos podido conseguir el hash del usuario con
el NTLM que es el protocolo de autenticación de Windows.
Nos lo copiaremos:
Para descifrar el hash usaremos la herramienta de hashcat, filtramos para que nos salgan los que
usen HTLM, tenemos la posibilidad de usar hashcat –examples-hashes | less, posteriormente
decimos con /HTLM nos aparecería todo lo que sale textualmente lo indicado:
O también con grep -i “NTML”:
En este caso necesitamos el NetNTMLv2, en la captura anterior se encuentra ese precisamente,
es cuando intentan acceder al recurso SMB, lo utiliza por red. Adjuntamos captura, en este caso
seleccionamos el ID 5600 que es el tipo de hash correspondiente que debemos de utilizar.
Si ejecutamos hashcat -m 5600 ~/Desktop/HASH /usr/share/wordlists/rockyou.txt
(rockyou.txt es un archivo txt en el cual hay millones de combinaciones de contraseñas y sirve
para filtrar las contraseñas por caracteres de ahí, viene guardado en el SO).
El resultado indica el estado Cracked, mostrando que la contraseña asociada al usuario
ADMINISTRADOR ha sido recuperada con éxito.
En la salida se observa claramente que la contraseña obtenida es root, confirmando que el hash
NT
LMv2 capturado ha sido crackeado correctamente mediante Hashcat.
Password in Object Description
En esta práctica aprovechó una mala práctica común en empresas, donde algunos empleados
almacenan su contraseña en la descripción del usuario dentro de Active Directory porque se les
olvida.
Este campo es visible para otros usuarios del dominio, por lo que sin realizar ningún ataque
complejo, simplemente revisando las descripciones, es posible obtener contraseñas en texto
plano.
Gracias a esto consigo credenciales válidas, que me permiten acceder al sistema y enumerar
más información del dominio, pudiendo llegar a comprometer más usuarios y potencialmente
toda la red corporativa.
La práctica demuestra que un fallo humano sencillo puede tener un impacto muy grande en la
seguridad de una empresa.
Además, en esta práctica aprovecho que ya tengo un usuario y una contraseña válidos de la
anterior vulnerabilidad. Gracias a eso puedo ejecutar la enumeración de forma autenticada, lo
que me da acceso a mucha más información de los usuarios del dominio.
Observamos que los usuarios algunos de ellos almacenan las contraseñas en la descripción:
Ejecuto enum4linux aprovechando que ya tengo un usuario y una contraseña válidos (obtenidos
antes), puedo enumerar información del dominio sin problema. Si no tuviera credenciales.
-u - Indica el usuario con el que me auténtico contra el dominio.
-p - Indica la contraseña del usuario anterior.
-a - Ejecuta todas las técnicas de enumeración disponibles:
En la salida del comando se listan los usuarios del dominio y, en algunos casos, aparece el campo
Description.
Ahí es donde se ve claramente que algunos usuarios tienen la contraseña escrita en la
descripción, lo cual es una mala práctica grave y permite comprometer más cuentas de la red.
Object with Default Credentials & Password Spray
Como estamos trabajando con un Windows Server 2019 el cual hemos agregado muchas
vulnerabilidadesm, en este caso observaremos los usuarios que por defecto vienen con la
contraseña por defecto(ChangeMe123!) a varios usuarios. Aunque esta contraseña debía
cambiarse en el primer inicio de sesión, comprobé que algunas cuentas nunca llegaron a hacerlo,
manteniendo las credenciales iniciales.
Primero realicé una enumeración de usuarios del dominio, con la que obtuve una lista completa
de cuentas válidas. Posteriormente, utilicé esa lista para realizar un ataque de password spraying,
probando la contraseña por defecto contra todos los usuarios enumerados.
Creamos en el Linux un archivo txt para agregar todos los usuarios ahí:
Como resultado, pude identificar varias cuentas que seguían utilizando la contraseña inicial,
confirmando la existencia de usuarios con credenciales por defecto activas dentro del dominio.
Mediante la enumeración de usuarios del dominio y un ataque de password spraying con la
contraseña por defecto “ChangeMe123!”, se identificaron múltiples cuentas que no habían
cambiado sus credenciales iniciales. Esto permitió la autenticación válida de varios usuarios,
confirmando la existencia de objetos con credenciales por defecto en el dominio.
Esto lo podemos ver en el archivo vulnad.ps1 que viene por defecto:
Explicación de posibles remediaciones.
Deshabilitar LLMNR mediante Group Policy (GPO).
La vulnerabilidad se mitiga deshabilitando LLMNR y NetBIOS, evitando que los equipos resuelvan
nombres mediante broadcast y eliminando la posibilidad de capturar hashes NTLM en la red.
En esta captura te enseño la desactivación de LLMNR mediante una directiva de grupo.
Al habilitar la política “Desactivar resolución de nombres de multidifusión”, se evita que los
equipos del dominio utilicen LLMNR, mitigando ataques de poisoning y la captura de hashes
NTLM en la red.
Eliminar datos personales y confidenciales dentro de las descripción
La vulnerabilidad existe porque los usuarios almacenan contraseñas en el campo “Descripción”
de Active Directory.
Esto permite que cualquier usuario autenticado pueda leerlas y comprometer más cuentas.
También forzar el cambio de contraseña con un cambio para cada 90 días.
Y por último, restringir quién puede ver las descripciones ACL, ya que ningún usuario debería de
poder.
También sobre la conciencia de la importancia de las contraseñas, además de incluir los requisitos
mínimos.
Remediación de credenciales por defecto y ataques de Password Spraying
Para mitigar esta vulnerabilidad, lo primero que haría sería eliminar el uso de contraseñas por
defecto en la creación de usuarios, evitando que varias cuentas compartan la misma credencial
inicial. En caso de ser necesario utilizar una contraseña temporal, esta debería ser única para cada
usuario y gestionada de forma segura.
Además, es fundamental forzar el cambio de contraseña en el primer inicio de sesión y
asegurarse de que dicha política se cumple realmente, revisando periódicamente las cuentas que
no hayan iniciado sesión o que permanezcan inactivas durante largos periodos de tiempo.
También se recomienda aplicar políticas de contraseñas más estrictas, como complejidad mínima,
caducidad y bloqueo de cuentas ante intentos repetidos, para reducir la efectividad de ataques de
password spraying.
Por último, limitar la enumeración de usuarios y monitorizar intentos de autenticación anómalos
permitiría detectar este tipo de ataques de forma temprana y reducir su impacto.
Uso de cuentas gMSA para servicios
En lugar de utilizar cuentas normales para ejecutar servicios, implementaría Group Managed
Service Accounts (gMSA).
Esto permite:
● Contraseñas largas generadas automáticamente.
● Rotación automática sin intervención manual.
Eliminación del riesgo de contraseñas estáticas en servicios.
Reduce muchísimo ataques como Kerberoasting y exposición de credenciales en servicios.
Deshabilitar almacenamiento de hashes débiles (LM Hash)
Configurar la política para evitar que el sistema almacene hashes LM antiguos.
Esto impide que, aunque se comprometa un sistema, existan hashes débiles que puedan
crackearse fácilmente.
Es una medida simple pero muy efectiva a nivel defensivo.
Protección de LSASS y credenciales en memoria
Activaría:
● Protección de LSASS como proceso protegido.
● Windows Defender Credential Guard.
Esto dificulta la extracción de credenciales en memoria y habría complicado bastante el uso de
herramientas como Mimikatz durante la auditoría.
Segmentación de red interna real
Implementaría segmentación mediante VLAN o firewall interno separando:
● Controladores de dominio
● Servidores
● Equipos de usuario
Durante mi práctica, al estar todo en el mismo segmento, el movimiento lateral fue mucho más
sencillo.
Una segmentación adecuada reduce drásticamente la superficie de ataque.
CONCLUSIONES
A lo largo del proceso he seguido una metodología estructurada que me ha permitido identificar,
explotar y analizar múltiples vulnerabilidades presentes en el dominio. No me he limitado
únicamente a detectar fallos, sino que he comprobado de forma práctica hasta qué punto podían
comprometer la infraestructura.
Durante la fase de reconocimiento confirmé que el controlador de dominio exponía servicios
críticos como LDAP, SMB, Kerberos y DNS. A partir de ahí, fui encadenando vulnerabilidades que,
combinadas entre sí, me permitieron escalar privilegios progresivamente hasta alcanzar el
compromiso total del dominio.
Uno de los aspectos que más me ha llamado la atención es cómo errores aparentemente simples
como contraseñas por defecto, almacenamiento de credenciales en descripciones o permitir
LLMNR— pueden derivar en un ataque crítico como DCSync y la generación de un Golden Ticket.
Esto demuestra que en ciberseguridad no solo importan las vulnerabilidades técnicas complejas,
sino también las malas prácticas operativas y la falta de controles básicos.
He podido comprobar que:
● La confidencialidad quedó completamente comprometida al extraer todos los hashes del
dominio.
● La integridad se vio afectada al poder modificar objetos y generar persistencia.
● La disponibilidad podría verse gravemente impactada si un atacante decidiera desplegar
ransomware o alterar servicios críticos.
El nivel de acceso alcanzado demuestra que un atacante con acceso inicial a la red interna podría
comprometer toda la infraestructura en un periodo muy reducido de tiempo.
Desde un punto de vista personal, este proyecto me ha permitido entender cómo funciona
realmente Active Directory a nivel ofensivo y defensivo. He aprendido no solo a explotar
vulnerabilidades, sino también a pensar como un administrador que debe proteger la
infraestructura.
En conclusión, considero que el entorno analizado presenta un nivel de riesgo crítico y requeriría
una remediación inmediata en un entorno real. La aplicación de controles de seguridad
avanzados, políticas robustas y monitorización activa sería esencial para reducir la superficie de
ataque y evitar un compromiso total como el demostrado en esta auditoría.
ANEXOS
Anexo I – Entorno de laboratorio
● Máquina atacante: Kali Linux
● Controlador de dominio basado en Windows Server 2019
● Dominio configurado mediante el script:
○ Vulnerable-AD
Anexo II – Herramientas utilizadas
Durante la auditoría he empleado las siguientes herramientas:
● Nmap – Escaneo y detección de servicios.
● Enum4linux – Enumeración SMB.
● CrackMapExec – Validación de credenciales.
● Responder – Captura de hashes NTLM.
● Hashcat – Crackeo de hashes.
● Mimikatz – Extracción de credenciales y generación de tickets.
● BloodHound – Análisis de relaciones de privilegios.
Anexo III – Evidencias técnicas
● Capturas de tráfico LLMNR.
● Hashes NTLMv2 capturados y crackeados.
● Extracción de hashes mediante DCSync.
● Generación y validación de Golden Ticket.
● Enumeración LDAP anónima.
● Evidencias de autenticación válida tras password spraying.
BIBLIOGRAFÍA
Para la realización de esta auditoría me he apoyado en las siguientes fuentes técnicas:
● Microsoft. Documentación oficial de Active Directory y políticas de seguridad.
https:/ learn.microsoft.com/
● Vulnerable-AD – Safebuffer.
https:/github.com/safebuffer/vulnerable-AD
● NIST – Framework de Ciberseguridad.
https:/www.nist.gov/cyberframework
● OWASP – Principios de seguridad y buenas prácticas.
https:/owasp.org/
● Hashcat – Documentación oficial.
https:/hashcat.net/
● Mimikatz – Documentación técnica.
https:/github.com/gentilkiwi/mimikatz
● Lucidchart – Diseño de topología de red.
https:/ lucid.app/
● ChatGPT – Para la descripción de algunos detalles para redactar pero también faltas de
ortografía.
https:/ chatgpt.com
