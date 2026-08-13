# TestLink 1.9.20 con branding Masfazzil

Instancia local de TestLink 1.9.20 (XAMPP / PHP 7.4 / MySQL) adaptada con la identidad visual de Masfazzil QA.

## Branding aplicado

- **Login**: logo de la marca, sin versionado "1.9.20 [DEV]" en pantalla.
- **Navbar**: barra azul con logo y debajo el wordmark **Quality / Assurance** en franja blanca.
- **Estilos globales**: botones, tablas, títulos, campos de formulario y paneles en la paleta de la marca.
- **Favicon** con el logo.

## Requisitos

- XAMPP con PHP 7.3 o superior y MySQL.
- María base de datos llamada `testlink`.

## Instalación

1. Clorillar el repositorio dentro de `htdocs`:

   ```
   git clone https://github.com/Carlosz91/testlink-masfazzil.git
   ```

2. Crear la base de datos `testlink` en MySQL.
3. Copiar `config_db.inc.php.example` como `config_db.inc.php` y completar las credenciales:

   ```
   cp config_db.inc.php.example config_db.inc.php
   ```

4. Abrir `http://localhost/testlink/` e iniciar sesión.

   > `config_db.inc.php` está gitignoreado a propósito: contiene tus credenciales
   > de base de datos y no debe subirse al repositorio.

## Configuración destacada

- Colores de marca en `gui/themes/default/css/custom.css` (bloque `MASFAZZIL GLOBAL BRANDING`).
- Logos en `gui/themes/default/images/` (`masfazzil-logotipo.png`, `masfazzil-logotipo-blanco.png`, `favicon-masfazzil.png`).
- Layout de la barra de navegación en `gui/templates/tl-classic/navBar.tpl` y `gui/themes/default/css/frame.css`.

## Roles y permisos

TestLink maneja permisos por rol: un usuario tiene **un solo rol** (columna
`role_id` en `users`) y cada rol tiene asociados derechos en la tabla
`role_rights` (un role_id → muchos right_id). La interfaz muestra/oculta opciones
según los `right_id` que tenga el rol del usuario logueado.

Roles definidos en esta instalación (tabla `roles`):

| id  | rol              | Uso                                                    |
|-----|------------------|--------------------------------------------------------|
| 1-5 | `reserved` / `guest` | Roles del sistema TestLink (no tocar)              |
| 6   | `senior tester`   | Ejecuta pruebas, ve reportes                           |
| 7   | `tester`          | Ejecuta y registra resultados                          |
| 8   | `admin`           | Acceso total al sistema (53 derechos)                  |
| 9   | `leader`          | Coordina planes y equipos                              |
| 10  | `analista`        | Rol custom: diseño de casos (sin gestión de planes)    |
| 11  | `analista senior` | Rol custom: gestión completa de planes de prueba       |

> El rol `analista senior` (id 11) fue creado con **los mismos 53 derechos que
> `admin`** (ver sección [Cambios de base de datos](#cambios-de-base-de-datos-no-versionados)),
> lo que hace aparecer todas las opciones del bloque "Current Test Plan"
> (gestión de planes, builds, hitos, ejecución, métricas, plataformas, etc.)
> en `mainPage.php`.

Cuentas creadas (tabla `users`):

| login   | password    | rol asignado    |
|---------|-------------|-----------------|
| `admin` | (la del instalador) | `admin`   |
| `tester`| `Tester2026@` | `analista senior` |

### Consultar / modificar permisos

```sql
-- Ver todos los roles
SELECT id, description FROM roles;

-- Ver a qué rol pertenece cada usuario
SELECT u.id, u.login, r.description AS role
FROM users u JOIN roles r ON r.id = u.role_id;

-- Ver cuántos derechos tiene cada rol
SELECT r.id, r.description, COUNT(rr.right_id) AS permisos
FROM roles r
LEFT JOIN role_rights rr ON rr.role_id = r.id
GROUP BY r.id;

-- Ver los derechos de un rol en particular (p. ej. analista senior = 11)
SELECT rt.id, rt.description
FROM role_rights rr
JOIN rights rt ON rt.id = rr.right_id
WHERE rr.role_id = 11
ORDER BY rt.id;
```

### Asignar un rol a un usuario

Desde la interfaz: **Administración → Asignar Roles a Usuarios**, o por SQL:

```sql
-- Cambiar el rol del usuario con login 'tester' a analista senior (11)
UPDATE users SET role_id = 11 WHERE login = 'tester';
```

### Campos custom en la pestaña Ejecutar Pruebas

Se agregaron 3 campos custom vinculados a casos de prueba (tabla `custom_fields`,
node_type `testcase` = 3) para replicar la pantalla de ejecución de la instancia
origen:

| name                | label                        | type | valores |
|---------------------|------------------------------|------|---------|
| `pruebas_de_tarifas`| Pruebas de Tarifas           | radio (9) | SI / NO |
| `jira_id`           | Jira(ID)                     | string (0) | texto libre |
| `fallo_celula`      | Fallo será atendido en Célula| radio (9) | SI / NO |

SQL para replicarlos en otra instalación (los `id` de la tabla `custom_fields` se
generan con `auto_increment`; usar los ids que resulten en los INSERTs de las
tablas de vínculo):

```sql
INSERT INTO custom_fields (name, label, type, possible_values, show_on_design,
                           enable_on_design, show_on_execution, enable_on_execution)
VALUES
('pruebas_de_tarifas', 'Pruebas de Tarifas', 9, 'SI|NO', 0, 0, 1, 1),
('jira_id', 'Jira(ID)', 0, '', 0, 0, 1, 1),
('fallo_celula', 'Fallo será atendido en Célula', 9, 'SI|NO', 0, 0, 1, 1);

-- Reemplazar <id> por los ids generados arriba
INSERT INTO cfield_node_types (field_id, node_type_id) VALUES (<id>, 3);
INSERT INTO cfield_testprojects (field_id, testproject_id, display_order, location, active)
VALUES (<id>, 1, <id>, 1, 1);
```

## Cambios de base de datos (no versionados)

El repositorio solo contiene código. Las personalizaciones que viven en MySQL **no se
clonean** y hay que aplicarlas manualmente en cada instalación.

- **Rol `analista senior`**: se le habilitó la gestión completa de planes de prueba
  copiando los mismos permisos que tiene el rol `admin` (53 entradas en `role_rights`),
  lo que hace aparecer todas las opciones del bloque "Current Test Plan" en
  `mainPage.php`.

Consulta para replicarlo (como usuario con credenciales de la BD):

```sql
-- En la otra PC, asociar al rol analista senior los mismos permisos que admin
INSERT IGNORE INTO role_rights (role_id, right_id)
SELECT 11, right_id FROM role_rights WHERE role_id = 8;

-- Verificar
SELECT COUNT(*) FROM role_rights WHERE role_id IN (8, 11);
```

### Instalar en otra PC

**Opción A – llevar la base completa** (recomendada si ya tenés datos/proyectos):

```bash
# En la PC origen
mysqldump -u <usuario> -p<password> testlink > testlink.sql

# En la PC destino, dentro de MySQL
mysql -u <usuario> -p<password> testlink < testlink.sql
```

**Opción B – partir de cero**: clonar el repo, crear la BD vacía y reproducir los
cambios (los permisos del SQL de arriba y las cuentas de usuario que necesites).

## Notas

- Entorno: Windows, XAMPP (Apache + MySQL), PHP 7.3.
- Cuenta de pruebas local: `tester` / `Tester2026@`.
- El fix de mojibake en `locale/es_ES/strings.txt` (doble codificación UTF-8) **sí** está
  versionado; al clonar ya viene corregido, no hace falta reaplicarlo.
  Backup local: `locale/es_ES/strings.txt.bak-doble-enc` (no se sube al repo).