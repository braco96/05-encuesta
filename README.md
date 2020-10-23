# Encuesta (Frontend Angular)
Cliente Angular que consume un **socket-server** (Socket.IO) y una API REST para mostrar una **gráfica de barras en tiempo real** (ng2-charts) con resultados de encuestas.

## 🧩 Tech stack
- **Angular** (CLI)
- **ngx-socket-io** – conexión WebSocket a `Socket.IO`
- **ng2-charts / Chart.js** – rendering de la gráfica
- **Bootstrap 4** (CDN, en `index.html`)

## 📦 Requisitos
- Node.js 12+ (recomendado LTS)
- Angular CLI instalado globalmente:  
  ```bash
  npm i -g @angular/cli
  ```
- Backend socket corriendo en `http://localhost:5000`  
  > La app se conecta por defecto a esa URL (ver `app/app.module.ts` -> `SocketIoConfig`).

## 🚀 Puesta en marcha
Instala dependencias y levanta el servidor de desarrollo:
```bash
npm install
ng serve -o
```
La app estará en: `http://localhost:4200`

## 🔌 Configuración de sockets
Archivo: `src/app/app.module.ts`
```ts
import { SocketIoModule, SocketIoConfig } from 'ngx-socket-io';
const config: SocketIoConfig = { url: 'http://localhost:5000', options: {} };
// ...
imports: [
  // ...
  SocketIoModule.forRoot(config)
]
```
- Si tu backend corre en otra URL/puerto, cambia `url` en `config`.

Servicio que gestiona la conexión: `src/app/services/websocket.service.ts`
- Eventos de conexión/desconexión
- `emit(evento, payload?)`
- `listen(evento)` devuelve un observable de RxJS

## 📊 Componente principal
Ruta: `src/app/components/encuesta/encuesta.component.*`

- Carga inicial: `GET http://localhost:5000/grafica`
- En tiempo real: escucha `cambio-grafica` por Socket.IO y actualiza `barChartData`.

Plantilla (`encuesta.component.html`):
```html
<canvas baseChart
  [datasets]="barChartData"
  [labels]="barChartLabels"
  [options]="barChartOptions"
  [chartType]="barChartType"></canvas>
```

## 🔁 Flujo de datos
1. **Backend** expone `/grafica` y emite `cambio-grafica` cuando cambian los valores.
2. **Frontend** hace `GET /grafica` para estado inicial.
3. **Frontend** se suscribe a `cambio-grafica` y actualiza la gráfica en vivo.

## 🧪 Probar desde backend
Ejemplos de actualización (dependen de tu implementación backend):
```bash
# Ejemplo POST para incrementar valores
curl -X POST http://localhost:5000/grafica \
  -H "Content-Type: application/json" \
  -d '{"opcion":0,"unidades":5}'
```
> El backend debería emitir `cambio-grafica` y el frontend reflejará los cambios.

## 🗂 Estructura mínima (src/)
```
src/
├─ app/
│  ├─ app.module.ts
│  ├─ app.component.*
│  ├─ services/websocket.service.ts
│  └─ components/
│     └─ encuesta/
│        ├─ encuesta.component.ts
│        ├─ encuesta.component.html
│        └─ encuesta.component.css
├─ index.html        # incluye Bootstrap 4 (CDN)
├─ styles.css
├─ main.ts
└─ environments/
   ├─ environment.ts
   └─ environment.prod.ts
```

## ⚙️ Variables y ajustes
- **Etiquetas de barras**: `barChartLabels` en `encuesta.component.ts`
- **Opciones Chart.js**: `barChartOptions`
- **Endpoint inicial**: `GET /grafica`
- **Evento de socket**: `cambio-grafica`

## ❗ Problemas comunes
- **CORS**: asegúrate que el backend habilita CORS para `http://localhost:4200`.
- **URL backend**: si no es `http://localhost:5000`, actualiza `SocketIoConfig` y el endpoint del `HttpClient`.
- **Versiones**: ng2-charts/Chart.js deben ser compatibles con tu Angular.

## 🧹 Scripts útiles
```bash
# Linter (si está configurado en el proyecto raíz)
npm run lint

# Tests (Karma + Jasmine)
npm test
```

## 📝 Licencia
MIT – Utiliza y modifica libremente.
