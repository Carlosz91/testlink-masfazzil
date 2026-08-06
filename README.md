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

## Notas

- Entorno: Windows, XAMPP (Apache + MySQL), PHP 7.3.
- Cuenta de pruebas local: `tester` / `Tester2026@`.