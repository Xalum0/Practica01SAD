# Práctica 1 de SAD · SecureCorp — Respuestas

**Nombre y apellidos: Andrés Colón Troya**
**Usuario: acolon**

Responde con tus palabras, en 1-3 líneas. En la defensa te preguntaré lo mismo en voz alta.

**Contraseñas que has usado** (solo porque es un laboratorio; en una empresa, jamás en un fichero):

- Tu usuario: Andres2026 (repeti la estructura de las otras contraseñas para mayor regularidad y similitud)
- mtorres: Marta2026

---

**1. (A1)** ¿Quién es el `issuer` de tu `ca.crt`? ¿Hasta qué fecha es válido? ¿Por qué el `subject`
y el `issuer` de la CA son iguales y los de `ldap.crt` no?

el issuer es: C = ES, O = SecureCorp, CN = Andres Root CA - Andres Colon
la fecha de validad es: 7/10/2026-4/10/2036
son distintos porque el `subject` identifica al servidor, mientras que el `issuer` identifica a quien firmó su certificado por otro lado la ca se autofirma y para si misma el `subject` y el `issuer` son si mismo.

**2. (A3)** Pega el comando y el resultado de tus dos búsquedas:

```
a) miembros de rrhh:

dn: cn=rrhh,ou=groups,dc=securecorp,dc=local
member: uid=lromero,ou=people,dc=securecorp,dc=local
member: uid=mtorres,ou=people,dc=securecorp,dc=local


b) cn y mail de todas las personas:

dn: uid=lromero,ou=people,dc=securecorp,dc=local
uid: lromero
mail: lromero@securecorp.local

dn: uid=acolon,ou=people,dc=securecorp,dc=local
uid: acolon
mail: acolon@securecorp.local

dn: uid=mtorres,ou=people,dc=securecorp,dc=local
uid: mtorres
mail: mtorres@securecorp.local



```

**3. (A4)** ¿Por qué la clave `ldap.key` tiene que ser de `openldap` y tener permisos 600?

Porque `slapd` se ejecuta como el usuario `openldap` y necesita leer su clave privada para establecer conexiones LDAPS. Los permisos son necesarios para que nadie salvo el propietario pueda interactuar y 600 significa solo permiso de lectura y escritura para el usuario propietario y ya.

**4. (A4)** ¿Qué valor has puesto en `SLAPD_SERVICES` y por qué?

He puesto `SLAPD_SERVICES="ldaps:/// ldapi:///"`. Lo he hecho asi ya que añadiendo una s a la parte de `ldap:///` activas la version segura en el otro puerto y en la practica nos dice de no borrar `ldapi:///`.

**5. (A4)** Antes de añadir `TLS_CACERT` en el cliente, `ldaps://` no funcionaba. ¿Por qué?

Porque el cliente no conocía ni confiaba en la CA que firmó el certificado del servidor LDAP. Por eso no podía verificar el certificado y rechazaba la conexión TLS. Al añadir `TLS_CACERT /pki/ca/ca.crt`, el cliente puede verificarlo y conectarse mediante LDAPS.


**6. (B3)** Pega la salida de `klist` con tus dos tickets. ¿Para qué sirve cada uno? ¿Ha viajado tu
contraseña por la red?

```
Ticket cache: FILE:/tmp/krb5cc_0
Default principal: acolon@SECURECORP.LOCAL

Valid starting     Expires            Service principal
10/08/26 14:35:48  10/09/26 00:35:48  krbtgt/SECURECORP.LOCAL@SECURECORP.LOCAL
	renew until 10/15/26 14:35:48
10/08/26 14:35:58  10/09/26 00:35:48  host/web.securecorp.local@SECURECORP.LOCAL
	renew until 10/15/26 14:35:48
```

**7. (C)** En el `docker-compose.yml`, ¿qué diferencia hay entre `build:` e `image:`? ¿Qué significa la línea `- "8081:80"` del servicio `phpldapadmin`?

`build:` indica dónde está el Dockerfile para construir una imagen; por ejemplo, `build: ./web` construye la imagen de la máquina web. `image:` indica qué imagen debe usar el servicio, ya sea una que existe en el equipo o una que Docker puede descargar.

La línea `- "8081:80"` permite que redireccione la entrada del puerto 8081 escuchando desde alli hacia el puerto interno 80. Por eso se accede al servicio desde el ordenador usando el puerto 8081.

**8. (C)** ¿Por qué en la máquina `web` no has tenido que escribir a mano `TLS_CACERT`, y en el cliente sí? ¿Qué pasaría con esa línea del cliente si hicieras `./lab.sh reset`?

En `web`, la instrucción `RUN echo "TLS_CACERT /pki/ca/ca.crt" >> /etc/ldap/ldap.conf` está en su Dockerfile: la línea se añade al construir la imagen. En el cliente la añadí manualmente al fichero de configuración del contenedor.

Si hiciera `./lab.sh reset`, se borrarían y recrearían las máquinas. La modificación manual del cliente se perdería y tendría que volver a añadir `TLS_CACERT`; en cambio, la receta de `web/Dockerfile` seguiría incluyendo esa configuración.

