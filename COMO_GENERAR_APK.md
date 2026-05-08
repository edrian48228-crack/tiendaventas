# Cómo generar tu APK de Android sincronizada con tu sistema

Tu sistema MegaTienda funciona como PWA. Para empaquetarlo como APK real
con sincronización en vivo al servidor (GitHub Pages), tienes 3 opciones,
ordenadas de **más fácil → más profesional**:

---

## Opción 1 (RECOMENDADA – 5 minutos): PWABuilder
Sin instalar nada. Solo necesitas tu URL pública (ej. `https://tuusuario.github.io/tutienda/`).

1. Abre https://www.pwabuilder.com
2. Pega tu URL y pulsa **Start**.
3. PWABuilder analizará tu `manifest.webmanifest` y `sw.js` (ya están listos).
4. Pulsa **Package for Stores → Android**.
5. Configuración recomendada:
   - **Package ID**: `com.tuusuario.megatienda`
   - **App name**: el nombre de tu tienda
   - **Display mode**: `standalone`
   - **Signing key**: deja "Generate new" (guarda el `.keystore` que descarga)
6. Descarga el ZIP. Dentro encontrarás `app-release-signed.apk`.
7. Instálalo en Android (activa "Orígenes desconocidos").

✅ **La APK queda apuntando a tu URL real**, así que cualquier cambio que hagas en GitHub se ve en la APK al instante (gracias al Service Worker `sw.js` v22 con offline total).

---

## Opción 2 (técnica – control total): Capacitor
Para personalizar más (íconos nativos, splash, push):

```bash
npm i -g @capacitor/cli
mkdir mi-tienda-apk && cd mi-tienda-apk
npm init -y
npm i @capacitor/core @capacitor/android
npx cap init "MegaTienda" "com.tuusuario.megatienda" --web-dir=www
mkdir www && cp ../repotienda-main/* www/
npx cap add android
npx cap sync
npx cap open android   # abre Android Studio → Build → Build APK
```

En `capacitor.config.json` añade para que cargue desde tu servidor (sync en vivo):
```json
"server": { "url": "https://tuusuario.github.io/tutienda/", "cleartext": true }
```

---

## Opción 3: TWA con Bubblewrap (Google oficial)
```bash
npm i -g @bubblewrap/cli
bubblewrap init --manifest=https://tuusuario.github.io/tutienda/manifest.webmanifest
bubblewrap build
```
Genera un APK firmado conectado a tu PWA con sincronización transparente.

---

## Sincronización servidor ↔ APK

Tu sistema ya implementa:
- **Pull cada 20s** mientras la app está visible.
- **Pull al volver al primer plano** (`visibilitychange`).
- **Pull al recuperar conexión** (`online`).
- **Push inmediato** al cambiar logo/avatar/configuración.
- **Cache offline TOTAL** de imágenes, fuentes, iconos y recursos (CacheFirst en sw.js v22).

Por eso, sea cual sea la opción que elijas, los cambios entre Windows y Android se reflejan en ambos sentidos.

---

## Aislamiento por repositorio (importante)

A partir de v21 cada repositorio tiene su propio `__REPO_KEY` y namespace
independiente para imágenes (`mt_img_cache__owner__repo`). Esto significa
que si tienes **varias tiendas en GitHub bajo el mismo usuario**, cada APK
verá ÚNICAMENTE las imágenes de su propio repo. Nunca se mezclarán.

Para que la APK apunte al repo correcto, simplemente apunta la URL del
empaquetador (PWABuilder/Capacitor/Bubblewrap) al repo deseado:

- Tienda A: `https://tuusuario.github.io/tienda-a/` → APK A
- Tienda B: `https://tuusuario.github.io/tienda-b/` → APK B

Cada APK se sincroniza solo con SU repositorio.
