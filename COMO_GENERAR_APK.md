# Cómo publicar / instalar tu tienda como APK

Tu sistema YA es una **PWA instalable** (tiene `manifest.webmanifest`, `sw.js` e iconos).
Tienes 3 caminos, de más fácil a más completo:

## 1) Instalar como app desde el navegador (sin APK, recomendado)
- Sube la carpeta a un hosting con HTTPS (GitHub Pages, Netlify, Vercel, Cloudflare Pages).
- En Android: abre el sitio en Chrome → menú → **Instalar aplicación**.
- En iPhone: Safari → Compartir → **Añadir a pantalla de inicio**.
- Se abre a pantalla completa, con icono propio, igual que una APK.

## 2) Generar APK real con PWABuilder (gratis, 5 minutos)
1. Publica el sitio (paso anterior, debe tener HTTPS).
2. Entra a https://www.pwabuilder.com
3. Pega la URL de tu tienda y pulsa **Start**.
4. Pestaña **Android** → **Generate Package** → descarga el ZIP.
5. Dentro vienen:
   - `app-release-signed.apk` → instálalo directo en cualquier Android.
   - `app-release-bundle.aab` → para subir a Google Play.
6. Guarda el archivo `signing.keystore` y la contraseña que te dé PWABuilder; los necesitarás para futuras actualizaciones.

## 3) Generar APK localmente con Bubblewrap (avanzado)
```bash
npm i -g @bubblewrap/cli
bubblewrap init --manifest=https://TU-DOMINIO/manifest.webmanifest
bubblewrap build
```
Genera `app-release-signed.apk`.

## Notas
- El `manifest.webmanifest` ya define nombre, iconos y `display: standalone`.
- Si cambias el nombre o el icono de la tienda, vuelve a generar el APK.
- Para que funcione offline en Android, mantén `sw.js` en la raíz.
