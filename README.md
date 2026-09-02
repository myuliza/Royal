# Royal — Tienda de fundas para iPhone

## 1. Resumen del proyecto

**Royal** es una tienda de fundas para iPhone (modelos 13 en adelante), con una app multiplataforma (web + Android) hecha en **Flutter**, backend en **Firebase**, y pedidos que se cierran por **WhatsApp** (sin pago integrado dentro de la app).

**Identidad de marca:**
- Colores: dorado y azul
- Productos: fundas color sólido/transparente y personalizadas
- Sin ofertas ni descuentos generales, pero sí sistema de cupones/códigos promocionales
- Formas de pago: efectivo y transferencia (se coordinan por WhatsApp al cerrar el pedido)

**Precios:**
| Tipo | Precio |
|---|---|
| Color sólido / transparente | 25.000 Gs |
| Personalizada | 30.000 Gs |

---

## 2. Módulos de la app (cliente)

| Módulo | Función |
|---|---|
| Catálogo | Fundas organizadas por categoría (sólido, transparente, personalizada), con filtro por modelo de iPhone |
| Ficha de producto | Foto, modelo(s) compatible(s), precio, opción de personalización |
| Carrito | Selección de productos y cantidad, resumen del pedido |
| Checkout | Arma el pedido y abre WhatsApp con un mensaje prellenado (productos, cantidad, datos del cliente) |
| Cupones | Campo para ingresar código promocional que aplica el descuento antes de confirmar |
| FAQ | Preguntas frecuentes (envíos, formas de pago, tiempos de personalización, etc.) |

---

## 3. Estructura de datos en Firestore

La referencia de "sistema de ventas y facturación" que se compartió es más propia de un local físico con caja y POS. Para una tienda que cierra pedidos por WhatsApp, se simplifica así, dejando espacio para crecer más adelante:

**Colecciones principales:**
- `productos` → nombre, categoría, modelo(s) compatibles, precio, color, personalizable (bool), stock, URLs de fotos
- `categorias` → sólido, transparente, personalizada (para los filtros)
- `modelos` → iPhone 13, 13 Pro, 14, 14 Pro, 15, etc. (para el filtro por modelo)
- `cupones` → código, tipo (% o monto fijo), vigencia, usos máximos, usos actuales
- `pedidos` *(opcional, para registro interno aunque se cierre por WhatsApp)* → cliente, productos, total, cupón aplicado, método de pago, estado, fecha
- `faq` → pregunta, respuesta

**Queda afuera por ahora** (de la referencia original): inventario con entradas/salidas físicas, caja, vendedores, devoluciones, facturación fiscal — eso aplica más a un local físico con POS. Si Royal llega a tener local físico o varios encargados, se puede sumar después sin romper lo que ya está armado.

---

## 4. Stack técnico

- **Frontend:** Flutter (Android + Web con un solo código)
- **Backend:** Firebase → Firestore (datos), Storage (fotos), Hosting (opcional, para la versión web)
- **Editor:** VS Code + extensión Flutter (reemplaza a NetBeans para este proyecto)
- **WhatsApp:** paquete `url_launcher` para abrir `wa.me` con el pedido ya armado

**Costos:** Firebase (plan Spark) y Flutter son gratuitos para este volumen de uso. Los únicos costos reales serían la cuenta de desarrollador de Google Play (~$25 USD, pago único, solo si se publica en Android) y un dominio propio (opcional, si no se usa el subdominio gratis de Firebase Hosting).

---

## 5. Paso a paso

1. **Instalar Flutter SDK** — descargarlo de flutter.dev, agregarlo al PATH, y correr `flutter doctor` para verificar la instalación.
2. **Instalar VS Code + extensión Flutter** — instala automáticamente la extensión de Dart también.
3. **Crear el proyecto Flutter** — `flutter create royal_app` genera la estructura base con soporte Android y Web.
4. **Crear el proyecto en Firebase Console** — nuevo proyecto "Royal", activar Firestore Database, Storage y (opcional) Hosting, todo en plan gratuito Spark.
5. **Conectar Flutter con Firebase** — instalar Firebase CLI, correr `flutterfire configure`, y agregar `firebase_core`, `cloud_firestore` y `firebase_storage` al `pubspec.yaml`.
6. **Diseñar la base de datos en Firestore** — crear las colecciones `productos`, `categorias`, `modelos`, `cupones` y `faq`.
7. **Construir el catálogo** — pantalla principal que trae los productos desde Firestore, con filtros por categoría y modelo de iPhone.
8. **Armar carrito y checkout** — selección de productos, campo de cupón (validado contra la colección `cupones`), botón "Confirmar pedido" que arma el mensaje de WhatsApp.
9. **Agregar FAQ** — pantalla con preguntas y respuestas (desde Firestore o hardcodeadas al inicio).
10. **Aplicar identidad visual** — definir el tema (colores dorado y azul) en `ThemeData`, y tipografía consistente en toda la app.
11. **Probar en dispositivo real o emulador** — celular por USB con modo desarrollador, o emulador de Android Studio, corriendo `flutter run`.
12. **Publicar** — Android: generar APK/AAB firmado y subir a Google Play. Web: `flutter build web` y subir a Firebase Hosting.
