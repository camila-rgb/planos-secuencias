# Planos Secuencias — Cine al Aire Libre (React + Vite)

Cartelera semanal (una película por día, 19:00 h), reservas con pago de prueba y entrada con QR, membresía con perfil, calificaciones, comentarios y votación comunitaria.

## Cómo correrlo
```bash
npm install      # instala React, Vite y firebase
npm run dev      # desarrollo en http://localhost:5173
npm run build    # genera dist/
npm run preview  # prueba el build
```
Requiere Node 18 o superior.

## Estructura
- `index.html`: entrada de Vite (fuentes y `#root`).
- `public/img/`: pósters de las 14 películas.
- `src/main.jsx` / `src/App.jsx`: arranque y selección de vista.
- `src/context/AppContext.jsx`: estado global (sesión, base de datos local, navegación, modales, avisos) y acciones.
- `src/firebase.js`: inicialización de Firebase (app, auth anónima, Firestore, Analytics).
- `src/data/`: películas, planes, FAQ, votación y perfil inicial.
- `src/components/`: Header, Footer, Card, Poster, Ficha, PayModal, TicketModal, Toast.
- `src/views/`: Home, Cartelera, Votación, El espacio, FAQ, Contacto, Quiénes somos, Membresía, Perfil, Auth.
- `src/styles/estilos.css`: estilos originales (modo claro/oscuro).
- `firestore.rules`, `.env.example`: listos para la etapa con Firebase.

## Datos y Firebase
La app usa **Firebase Authentication (anónima)** y **Firestore**:
- Al abrir, inicia sesión anónima y lee el perfil guardado en `users/{uid}`. Cada cambio del perfil (reservas, votos, favoritas, plan, actividad) se guarda ahí.
- Los mensajes de Contacto se guardan en la colección `messages`.
- `localStorage` (clave `ps_db`) se mantiene como respaldo: si Firebase no responde o faltan las variables, la app funciona igual con datos locales. El pie de página muestra el estado de la conexión.
- La configuración está en `src/firebase.js` y se lee de las variables `VITE_FIREBASE_*` (archivo `.env`).
- Limitación: con autenticación anónima el perfil queda atado al navegador/dispositivo (no se puede entrar desde otro con el email). Para eso habría que sumar Email/Password o Google.
- Siguiente etapa: pasar reservas, calificaciones, comentarios y votos a sus propias colecciones (las reglas ya están en `firestore.rules`) y controlar el cupo de 50 con una transacción.

## Configuración de Firebase
1. Authentication → Sign-in method → habilitar **Anonymous**.
2. Firestore Database → crear base `(default)`.
3. Pestaña Reglas → pegar el contenido de `firestore.rules` y **publicar** (hace falta para `users` y `messages`).
4. `.env` ya trae los valores del proyecto `tp2---frontend-ii`; si usás otro proyecto, copiá `.env.example` a `.env` y completalo. Reiniciar `npm run dev` tras cualquier cambio.
5. Authentication → Settings → Authorized domains: agregar el dominio de Vercel al publicar.

## Notas
- Las variables `VITE_` llegan al navegador: usar solo la configuración web de Firebase, nunca claves privadas.
- Las reglas de Firestore se publican en Firebase; desplegar en Vercel no las actualiza.
- Las reglas no pueden controlar el cupo de 50 por función: eso se resuelve en la app con una transacción.

## Publicación
Subir a GitHub, importar el repositorio en Vercel (framework Vite, `npm run build`, salida `dist`), cargar las 7 variables `VITE_FIREBASE_*` (las de `.env`) en Production y desplegar. Si cambian las variables, hacer Redeploy.
