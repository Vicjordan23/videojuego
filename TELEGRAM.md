# Publicar Burbuja Cero como Mini App de Telegram con anuncios de Monetag

El juego ya detecta Telegram (`juego/index.html`) y funciona también en un navegador normal.

## Qué hace dentro de Telegram
- Se abre en pantalla completa en móvil y desactiva el gesto de bajar para cerrar, para que arrastrar no cierre el juego.
- Vibración de Telegram al recoger estrellas, chocar y perder.
- Guarda el progreso (mejor marca, estrellas, burbujas desbloqueadas) en la nube de Telegram, además de en el navegador.
- Saluda al jugador por su nombre y pide confirmación al cerrar en mitad de una partida.
- Botón **Compartir** al perder, si rellenas `SHARE_URL`.

## 1. Alojar el juego (HTTPS obligatorio)
Telegram solo abre páginas con HTTPS. Opciones:
- **GitHub Pages** (incluido): este repo trae `.github/workflows/pages.yml`. Pasos: mezcla la rama en `main`, ve a *Settings → Pages* y en *Source* elige **GitHub Actions**. La URL será `https://<usuario>.github.io/videojuego/`. Los repos privados necesitan un plan de pago para Pages.
- **Netlify / Vercel / Cloudflare Pages**: arrastra la carpeta `juego/`.

## 2. Crear el bot y la Mini App
1. En Telegram habla con [@BotFather](https://t.me/BotFather) y crea el bot con `/newbot`.
2. Con `/newapp` elige tu bot, pon el título, una descripción y una imagen, y pega la URL HTTPS del juego.
3. BotFather te da un enlace del tipo `https://t.me/tu_bot/tu_app`. Ese es tu enlace para compartir.
4. Opcional: `/setmenubutton` para que el botón de menú del bot abra el juego.

## 3. Conectar Monetag
1. En Monetag crea la zona para **Telegram Mini Apps** y pulsa **Get SDK**.
2. Copia el **ID de zona principal** (no uses las subzonas) y la URL del script `sdk.js` que te muestra el panel.
3. Rellena `juego/config.js`:
   ```js
   window.BC_CONFIG = {
     MONETAG_ZONE: '1234567',
     MONETAG_SDK_URL: 'https://…/sdk.js',
     INTERSTITIAL_EVERY: 4,
     SHARE_URL: 'https://t.me/tu_bot/tu_app'
   };
   ```
4. Sube los cambios. El juego carga el SDK y llama a `show_<ZONA>()` para cada anuncio.

## Dónde salen los anuncios
| Momento | Tipo | Quién lo decide |
|---------|------|-----------------|
| Botón **Revivir** al perder (una vez por partida) | Rewarded interstitial | El jugador |
| Botón **Estrellas x2** al perder (una vez por partida) | Rewarded interstitial | El jugador |
| Al pulsar **Otra vez**, cada `INTERSTITIAL_EVERY` partidas | Interstitial | Automático |

Los anuncios nunca aparecen en mitad de una partida. Si el anuncio falla o se cierra sin verlo, no se da la recompensa y sale el aviso "Anuncio no disponible".

Sin `MONETAG_ZONE` ni `MONETAG_SDK_URL`, el juego muestra un **anuncio de prueba** de 2,5 segundos para que puedas probar el flujo. Con eso, revivir y duplicar son gratis: rellena la configuración antes de publicar.

## Pendiente de tu lado
- Comprobar en tu panel de Monetag el formato exacto de la URL del SDK y que la zona esté aprobada.
- Revisar las normas de Monetag y de Telegram sobre frecuencia de anuncios antes de subir `INTERSTITIAL_EVERY`.
- Un ranking entre jugadores necesita un servidor pequeño para guardar las puntuaciones. Se puede añadir más adelante.
