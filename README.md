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