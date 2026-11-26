# GUÍA DETALLADA PASO A PASO PARA LA TOMA Y DOCUMENTACIÓN DE EVIDENCIAS
**Evidencia SENA:** GA9-220501096-AA1-EV02 — Realiza plan pruebas de software  
**Proyecto:** Sistema de Información Librería Luz (`Libreria-Luz-AA3` & `LibreriaLuz-API`)  
**Aprendiz:** Ronald Fabián Mendoza Rodríguez — **Ficha:** ADSO 3235898  

---

## Convención General de Captura y Nomenclatura
- **Formato obligatorio:** PNG de alta resolución (mínimo `1280 × 720` px o `1280 × 800` px).
- **Carpeta destino:** `/GA9-EV02/07Evidencias/`
- **Nomenclatura:** `EV001.png` hasta `EV020.png` (sin espacios ni caracteres especiales).
- **Reglas de calidad visual:** Mostrar barra de URL/endpoint, estado HTTP o código fuente relevante, fecha/hora del entorno y datos coherentes con los casos `CP-001` a `CP-030`.

---

## Procedimiento Detallado por Evidencia (`EV001` – `EV020`)

| Código | Qué pantalla o herramienta abrir | Qué acción exacta ejecutar | Qué debe verse en pantalla | Cuándo tomar la captura | Nombre de archivo | Sección del Informe Final |
|---|---|---|---|---|---|---|
| **EV-001** | Swagger UI (`http://localhost:9090/swagger-ui.html`) o Postman | Desplegar el grupo **Autenticación** y probar `POST /api/auth/logout` y `/recuperar` | Endpoints reales `/registro` y `/login`, más respuesta `404` / ausencia de `/logout` y recuperación | Inmediatamente después de validar la respuesta y comprobar la brecha en `AuthController.java` | `EV001.png` | Sección 12 (Evidencias) y Sección 14 (Hallazgos) |
| **EV-002** | Postman (colección `LibreriaLuz-API`) | Ejecutar `1.4 Iniciar sesión correcto` (`POST /api/auth/login`) con `juan@correo.com` y `ClaveSegura123!` | Código `200 OK`, mensaje `"Autenticación satisfactoria."`, objeto `usuario` sin hash ni clave y aserciones en verde | Tras recibir la respuesta HTTP `200` y validar la pestaña *Test Results* | `EV002.png` | Sección 12 (Evidencias — `CP-001`) |
| **EV-003** | Postman | Ejecutar `1.5 Iniciar sesión con contraseña incorrecta` (`POST /api/auth/login`) con clave errada | Código `401 Unauthorized` y JSON `{"mensaje": "Error en la autenticación."}` | Al finalizar la petición y confirmarse la uniformidad del mensaje de error | `EV003.png` | Sección 12 (Evidencias — `CP-002`) |
| **EV-004** | Navegador Web (`http://localhost:8080/LibreriaWeb/index.html`) y Postman | Intentar enviar formulario vacío o enviar `POST /api/libros` con `precio: -5` | Formulario HTML de *Registro de Libros* con atributos `required`/`min` y respuesta `400 Bad Request` | Al activarse la validación del navegador y tras recibir el `400` del servidor | `EV004.png` | Sección 12 (Evidencias — `CP-014`) |
| **EV-005** | Postman | Ejecutar peticiones `1.1 Registrar usuario` y `1.2 Registrar usuario duplicado` | Primera petición `201 Created` con correo normalizado; segunda `409 Conflict` (`"El correo ya está registrado."`) | Después de ejecutar ambas peticiones consecutivas | `EV005.png` | Sección 12 (Evidencias — `CP-004`, `CP-005`) |
| **EV-006** | Navegador (`index.html`) y editor IDE en `LibroServlet.java` (líneas 355–363) | Diligenciar formulario de libro y contrastar validación `BigDecimal` con `DECIMAL(10,2)` | Campos de registro y fragmento de `validarDatosLibro()` mostrando ausencia de control de escala decimal (`INC-005`) | Con formulario diligenciado y bloque de validación visible en pantalla | `EV006.png` | Sección 12 (`CP-007`, `CP-015`) y Sección 16 (Incidencias) |
| **EV-007** | Navegador (`GET /LibreriaWeb/LibroServlet?accion=listar`) y Postman (`4.6 Buscar`) | Cargar listado general y consultar `GET /api/libros?buscar=clean` y `?categoriaId=1` | Tabla HTML de catálogo con columnas ID, Título, ISBN, Precio, Stock y resultado JSON filtrado (`200 OK`) | Una vez renderizada la tabla completa y devueltos los resultados filtrados | `EV007.png` | Sección 12 (`CP-010` a `CP-013`) |
| **EV-008** | Navegador (`GET /LibreriaWeb/LibroServlet?accion=editar&id=3`) y `LibroServlet.java` | Abrir edición de libro, modificar precio/stock y probar payload con `<script>` | Formulario *Editar Libro* con `ID readonly` y evidencia de `escapeHtml()` / `PreparedStatement` | Antes de enviar la actualización y con el escape XSS verificado | `EV008.png` | Sección 12 (`CP-008`, `CP-016`) |
| **EV-009** | IDE en `LibroServlet.java` (líneas 34–40, 132) y navegador sobre enlace *Eliminar* | Inspeccionar botón *Eliminar* en el listado y el `case "eliminar"` dentro de `doGet()` | Enlace `<a href='LibroServlet?accion=eliminar&id=...'>` ejecutando borrado por `GET` sin CSRF (`INC-002`) | Con las líneas del `doGet()` y el enlace HTML resaltados | `EV009.png` | Sección 12 (`CP-009`) y Sección 16 (`INC-002`) |
| **EV-010** | Postman | Ejecutar `5.1 Registrar venta` (`POST /api/ventas`) y luego `5.2 Verificar stock descontado` (`GET /api/libros/{id}`) | Venta `201 Created` con `total: 55000.50` y posterior `GET` mostrando stock reducido de `5` a `3` | Al confirmar la reducción exacta del stock en la segunda petición | `EV010.png` | Sección 12 (`CP-019`) |
| **EV-011** | Postman y árbol de modelos del repositorio | Ejecutar `5.3 Registrar venta con stock insuficiente` y verificar modelos en `src/main/java` | Respuesta `409 Conflict` (`"Stock insuficiente..."`) y verificación de ausencia de módulo *Préstamos* (`INC-008`) | Tras validar rollback de stock y confirmar ausencia de entidad `Prestamo` | `EV011.png` | Sección 12 (`CP-017`, `CP-018`) |
| **EV-012** | Editor IDE en `web.xml` (AA3) y `Usuario.java` (API) | Revisar configuración de seguridad, filtros Servlet y atributos de entidad `Usuario` | `web.xml` sin `<security-constraint>` ni `<filter>` y `Usuario.java` sin campo `rol` (`INC-003`, `INC-006`) | Al comprobar acceso anónimo permitido a `/LibroServlet` | `EV012.png` | Sección 12 (`CP-021`, `CP-022`) |
| **EV-013** | Postman (`5.5 Listar ventas de un usuario`) y navegador en `reporte-pruebas.html` | Consultar `GET /api/ventas?usuarioId=1` y abrir reporte Newman | JSON de ventas filtrado por usuario (`200 OK`) y resumen de `32` peticiones / `60` aserciones exitosas | Al cargar completamente el resumen del reporte HTML | `EV013.png` | Sección 12 (`CP-020`) y Sección 13 (Resultados) |
| **EV-014** | Postman Collection Runner y Swagger UI (`/v3/api-docs`) | Ejecutar *Run Collection* completo de `LibreriaLuz-API` y consultar `/v3/api-docs` | Lista de las 32 peticiones en estado `PASS` y contrato OpenAPI `200 OK` | Al finalizar el 100% de la corrida del Runner | `EV014.png` | Sección 12 (`CP-023`, `CP-025`, `CP-027`) |
| **EV-015** | MySQL Workbench o editor SQL sobre `Libreria-Luz-AA3/database.sql` | Abrir `database.sql` (líneas 16–29) y ejecutar auditoría frente a `LibroDAOImpl.java` | Columna corrupta `\titulo` (`[TAB]itulo`) en DDL/INSERT y error `Unknown column 'titulo'` (`INC-001`) | Con las líneas 18 y 26 del script y el mensaje de error SQL visibles | `EV015.png` | Sección 12 (`CP-024`) y Sección 16 (`INC-001`) |
| **EV-016** | Diagrama/Consola BD y `DatabaseConnection.java` / `LibroDAOImpl.java` | Revisar tablas del sistema, ausencia de `db.properties` y bloque `catch (SQLException)` | Tablas `libro`, `usuarios`, `categorias`, `ventas`, `detalles_venta` y código que silencia `SQLException` (`INC-004`) | Al inspeccionar `DatabaseConnection` y `obtenerTodos()` | `EV016.png` | Sección 12 (`CP-024`, `CP-026`) |
| **EV-017** | Terminal PowerShell / CMD | Ejecutar `javac --release 8` (AA3) y `mvn -o test` en `LibreriaLuz-API` y `ServicioAutenticacion` | `JAVAC_EXIT=0`, `Tests run: 10, Failures: 0` (API) y `Tests run: 9, Failures: 0` (Auth), `BUILD SUCCESS` | Al finalizar ambos comandos Maven con resumen verde | `EV017.png` | Sección 12 (`CP-025`, `CP-028`, `CP-029`) y Sección 13 |
| **EV-018** | Excel (`06DashboardQA.xlsx`, hoja `Dashboard_QA`) | Abrir libro con fórmulas calculadas y revisar caso pendiente `CP-030` | Tarjetas KPI (`30` casos, `19` aprobados, `5` fallidos, `5` bloqueados, `1` pendiente, `46.67%` calidad) | Con todas las tarjetas KPI y tablas visibles en pantalla | `EV018.png` | Sección 12 (`CP-030`) y Sección 18 (Dashboard QA) |
| **EV-019** | Excel (`05BitacoraPruebas.xlsx`) | Abrir hoja `Bitacora_Pruebas` tras registrar las 10 sesiones de prueba | Tabla cronológica `LOG-001` a `LOG-010` con fechas, tiempos (`115` min), hallazgos y evidencias | Al completar el registro de todas las sesiones ejecutadas | `EV019.png` | Sección 12 y Sección 13 (Resultados) |
| **EV-020** | Vista consolidada del paquete `/GA9-EV02` y Dictamen Final | Verificar listado completo de entregables y checklist de criterios de salida ISO 25010 | Resumen de los 10 artefactos verificados y dictamen *Aprobado con Condiciones* por `INC-001`..`003` | Al concluir la verificación integral previa a la entrega | `EV020.png` | Sección 12 y Sección 19 (Conclusiones) |

---

## Instrucciones Rápidas para Re-ejecutar Pruebas en Vivo (Opcional para el Aprendiz)
1. **Pruebas Automatizadas API REST (`LibreriaLuz-API`):**
   ```powershell
   cd LibreriaLuz-API
   .\mvnw.cmd test
   ```
2. **Arranque de API REST y Swagger UI:**
   ```powershell
   .\mvnw.cmd spring-boot:run
   # Abrir http://localhost:9090/swagger-ui.html
   ```
3. **Pruebas Colección Postman vía Newman (CLI):**
   ```powershell
   npx newman run postman/LibreriaLuz-API.postman_collection.json -e postman/LibreriaLuz-Local.postman_environment.json
   ```
4. **Despliegue Módulo Web (`Libreria-Luz-AA3`) en Tomcat 9 + MySQL 8:**
   - Corregir primero el defecto crítico `INC-001` en `database.sql` cambiando `\titulo` (`	itulo`) por `titulo`.
   - Ejecutar `database.sql` en MySQL 8.
   - Copiar `src/main/resources/db.properties.example` a `src/main/resources/db.properties` y completar `db.url`, `db.user` y `db.password`.
   - Ejecutar `mvn clean package` y desplegar `target/LibreriaWeb-1.0-SNAPSHOT.war` en Tomcat 9 (`http://localhost:8080/LibreriaWeb/`).
