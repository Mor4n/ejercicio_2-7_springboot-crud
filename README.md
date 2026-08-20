# Ejercicio 2  -  7: Spring Boot CRUD con Seguridad (JWT) y Swagger

Este repositorio contiene el ejercicio práctico #2 de una API RESTful (CRUD) con Spring Boot, protegida mediante Spring Security (JWT) y documentada con Swagger UI.
En este ejercicio también se realizaron algunas configuraciones, se documentó y probó la API utilizando Swagger UI y Postman

## Actividades realizadas

1. **Configuración de Base de Datos MySQL:**
   - Se usó la base de datos del ejercicio anterior (`db_jpa_crud`), colocando las mismas credenciales.
   - Conexión de Spring Boot con MySQL mediante `application.properties`.
   - Inserción de roles básicos (`ROLE_USER`, `ROLE_ADMIN`) requeridos por el sistema.

2. **Integración de Swagger (OpenAPI):**
   - Agregada la dependencia `springdoc-openapi-starter-webmvc-ui` en el `pom.xml`.
   - Configuración en Spring Security (`SpringSecurityConfig.java`) para permitir el acceso público a las rutas de la documentación (`/swagger-ui.html`, `/v3/api-docs/**`).


3. **Pruebas de Seguridad y Roles (JWT):**
   - Registro de usuarios normales mediante la ruta pública.
   - Generación de token JWT mediante Login.
   - Comprobación de acceso a rutas protegidas (`/api/products`) dependiendo de los permisos del usuario (Usuario normal vs. Administrador).

---

##  Evidencias de pruebas

A continuación se documenta todo el flujo de pruebas realizadas en la API y en base de datos utilizando Swagger UI y Postman.

- **Paso 1:** Comprobación de que Hibernate creó las tablas correctamente en MySQL.
  ![Tablas creadas](/capturas/1.Tablas_creadas_en_mysql.png)
- **Paso 2:** Intento inicial de consultar los productos (`GET /api/products`) fallando por falta de usuario autenticado.
  ![Error GET sin usuario](/capturas/2.GET_sin_user.png)
- **Paso 3:** Creación manual de los roles `ROLE_USER` y `ROLE_ADMIN` en la tabla `roles`.
  ![Creación de roles](/capturas/3.Creacion_de_roles_en_tabla_roles.png)
- **Paso 4.0:** Petición para registrar un nuevo usuario normal a través de la ruta pública de Swagger.
  ![Petición Registro Usuario Normal](/capturas/4.0.POST_users_register_usuario_no_admin.png)
- **Paso 4.1:** Respuesta exitosa confirmando la creación del usuario normal en la base de datos.
  ![Respuesta Registro Usuario Normal](/capturas/4.1.POST_users_register_usuario_no_admin_creado_respuesta.png)
- **Paso 5:** Realización del Login exitoso con el usuario recién creado usando Postman, obteniendo el Token JWT.
  ![Login usuario normal](/capturas/5.LOGIN_correcto_de_user_no_admin_en_postman.png)
- **Paso 6:** Elevación de permisos de un usuario a Administrador insertando directamente el rol en la base de datos MySQL.
  ![Agregando admin en SQL](/capturas/6.Agregado_en_SQL_de_usuario_admin.png)
- **Paso 7.0:** Solicitud GET para consultar la lista de usuarios.
  ![Listando Usuarios](/capturas/7.0.GET_Users.png)
- **Paso 7.1:** Respuesta a la solicitud GET (Parte 1), donde se ven los datos iniciales.
  ![Respuesta GET Usuarios 1](/capturas/7.1.GET_Users_Respuesta_parte_1.png)
- **Paso 7.2:** Respuesta a la solicitud GET (Parte 2), aquí en el último usuario se ve que el usuario modificado cuenta con ambos roles (USER y ADMIN).
  ![Respuesta GET Usuarios 2](/capturas/7.2.GET_Users_Respuesta_parte_2.png)
- **Paso 8.0:** Intento fallido de utilizar la ruta `POST /api/users` para crear un nuevo usuario administrativo sin los permisos correspondientes (antes del login de superusuario).
  ![Error POST usuarios sin permisos](/capturas/8.0.POST_users_no_autorizado.png)
- **Paso 8.1:** Login exitoso del superusuario (que es administrador).
  ![Login como Superusuario](/capturas/8.1.Login_superusuario.png)
- **Paso 8.2:** Inserción del Token de administrador en la configuración de Autorización de Swagger UI para continuar las pruebas.
  ![Autenticando en Swagger](/capturas/8.2.Token_agregado_para_autenticar.png)
- **Paso 8.3:** Creación exitosa de un segundo usuario administrador mediante la ruta protegida utilizando el token.
  ![POST Crear usuario Admin](/capturas/8.3.POST_users_usuario_admin_creado.png)
- **Paso 8.4:** Consulta al endpoint de usuarios para verificar que el nuevo administrador ya aparece registrado en el sistema.
  ![Verificando nuevo usuario creado](/capturas/8.4.GET_users_nuevo_usuario_creado.png)
- **Paso 9.0:** Intento de consultar el catálogo completo de productos fallando intencionalmente por no proporcionar token en la petición.
  ![Falla sin login](/capturas/9.0.GET_products_no_funciona_sin_login.png)
- **Paso 9.1:** Consulta exitosa del catálogo completo de productos enviando el token de autenticación (puede ser esta ya sea user normal o admin)
  ![Éxito con login](/capturas/9.1.GET_products_con_auth_funcionando.png)
- **Paso 10:** Consulta exitosa de un producto específico utilizando su ID (`GET /api/products/{id}`).
  ![GET producto por ID](/capturas/10.GET_products_id_funciona_solo_con_auth.png)
- **Paso 11.0:** Falla al intentar crear un producto (`POST /api/products`) al ser un usuario sin rol de administrador.
  ![Falla POST sin ser admin](/capturas/11.0.POST_products_solo_funciona_con_admin.png)
- **Paso 11.1:** Creación exitosa del producto tras enviar la petición con el rol de administrador.
  ![Éxito POST siendo admin](/capturas/11.1POST_productos_funcionando_correctamente.png)
- **Paso 11.2:** Comprobación rápida del catálogo (`GET`), confirmando que el nuevo producto fue añadido.
  ![Verificación GET tras POST](/capturas/11.2.GET_productos_producto_añadido_correctamente.png)
- **Paso 12.0:** Falla al intentar actualizar el producto (`PUT /api/products/{id}`) por falta de permisos de administrador.
  ![Falla PUT sin ser admin](/capturas/12.0.PUT_productos_id_falla_si_no_es_admin.png)
- **Paso 12.1:** Actualización exitosa del producto enviando el request PUT con el usuario administrador.
  ![Éxito PUT siendo admin](/capturas/12.1.PUT_productos_id_funcionando_como_admin.png)
- **Paso 12.2:** Comprobación `GET` para verificar que la base de datos si reflejó la actualización del producto de manera consistente.
  ![Verificación GET tras PUT](/capturas/12.2.GET_productos_producto_actualizado_correctamente.png)
- **Paso 13.0:** Error al intentar eliminar el producto (`DELETE /api/products/{id}`) utilizando credenciales de usuario normal.
  ![Falla DELETE sin ser admin](/capturas/13.0.DELETE_producto_id_error_usuario_normal.png)
- **Paso 13.1:** Petición exitosa eliminando el producto utilizando credenciales de administrador.
  ![Éxito DELETE siendo admin](/capturas/13.1.DELETE_producto_id_eliminado_con_admin.png)
- **Paso 13.2:** Consulta final al catálogo (`GET`) para asegurar que el producto ha sido eliminado de la base de datos de manera definitiva.
  ![Verificación GET tras DELETE](/capturas/13.2.GET_products_producto_eliminado_correctamente.png)
