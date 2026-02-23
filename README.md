# 👓 OptiVision — Plataforma de Venta de Lentes en Línea

<div align="center">

![Next.js](https://img.shields.io/badge/Next.js-14-black?style=for-the-badge&logo=next.js)
![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-3ECF8E?style=for-the-badge&logo=supabase)
![Tailwind](https://img.shields.io/badge/Tailwind-CSS-38BDF8?style=for-the-badge&logo=tailwindcss)
![Stripe](https://img.shields.io/badge/Stripe-Payments-635BFF?style=for-the-badge&logo=stripe)
![Vercel](https://img.shields.io/badge/Deploy-Vercel-000000?style=for-the-badge&logo=vercel)
![Status](https://img.shields.io/badge/Status-En%20Desarrollo-orange?style=for-the-badge)

**Plataforma e-commerce fullstack para venta de armazones y lentes ópticos.**  
Catálogo con filtros, agendamiento de citas, pagos con Neonet y Credomatic, y probador virtual con IA.

[Ver Demo](#) · [Reportar Bug](#) · [Solicitar Feature](#)

</div>

---

## 📋 Tabla de Contenidos

- [Sobre el Proyecto](#-sobre-el-proyecto)
- [Funcionalidades](#-funcionalidades)
- [Tech Stack](#-tech-stack)
- [Arquitectura](#-arquitectura)
- [Base de Datos](#-base-de-datos)
- [Pasarela de Pago](#-pasarela-de-pago)
- [Probador Virtual](#-probador-virtual-ia)
- [Instalación](#-instalación)
- [Variables de Entorno](#-variables-de-entorno)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Flujo de Usuario](#-flujo-de-usuario)
- [Roadmap](#-roadmap)
- [Autor](#-autor)

---

## 🔭 Sobre el Proyecto

**OptiVision** es una plataforma de comercio electrónico especializada en la venta de armazones y lentes ópticos para el mercado guatemalteco. Combina una experiencia de compra moderna con funcionalidades únicas como el probador virtual de lentes usando inteligencia artificial y el agendamiento de citas para exámenes visuales.

El proyecto nació como respuesta a la falta de plataformas de e-commerce de óptica adaptadas al mercado local, integrando las pasarelas de pago guatemaltecas (Neonet y Credomatic) y un flujo diseñado para el comportamiento del consumidor local.

### ¿Por qué OptiVision?

La mayoría de las ópticas en Guatemala no tienen presencia digital sólida. OptiVision resuelve tres problemas concretos: la dificultad de explorar catálogos físicamente, la incertidumbre de cómo quedarán los lentes sin probarlos, y la falta de integración con pasarelas de pago locales.

---

## ✨ Funcionalidades

### Módulo de Catálogo
- Exploración de productos con filtros por estilo, material, género, marca y rango de precio
- Vista grid y vista lista intercambiable
- Scroll infinito con carga progresiva
- Búsqueda en tiempo real

### Módulo de Producto
- Galería de imágenes múltiples por producto
- Selector de color con preview visual
- **Probador virtual con IA** (cámara en vivo o subida de foto)
- Productos relacionados y recomendaciones

### Módulo de Carrito y Checkout
- Carrito persistente (localStorage + base de datos para usuarios autenticados)
- Aplicación de cupones de descuento
- Resumen de compra con IVA guatemalteco (12%)
- Checkout en un solo paso

### Módulo de Pagos
- Integración con **Neonet** (mercado guatemalteco)
- Integración con **Credomatic / VisaNet** (Guatemala)
- Webhooks para confirmación segura de pagos
- Manejo de estados: pendiente / aprobado / rechazado / error

### Módulo de Citas
- Calendario interactivo con disponibilidad en tiempo real
- Selección de sucursal con mapa integrado
- Confirmación por email automático
- Recordatorio 24 horas antes por WhatsApp (Twilio)

### Módulo de Cuenta
- Historial de pedidos con estado de envío
- Historial de citas agendadas
- Gestión de datos personales y dirección
- Autenticación con email o Google (Supabase Auth)

---

## 🛠️ Tech Stack

### Frontend
| Tecnología | Versión | Uso |
|-----------|---------|-----|
| Next.js | 14 (App Router) | Framework principal, SSR, rutas |
| React | 18 | UI components |
| Tailwind CSS | 3.4 | Estilos y diseño responsivo |
| Framer Motion | 11 | Animaciones y transiciones |
| Zustand | 4 | Estado global (carrito) |

### Backend & Servicios
| Tecnología | Uso |
|-----------|-----|
| Supabase | Base de datos PostgreSQL, Auth, Storage |
| Next.js API Routes | Endpoints para pagos, webhooks, citas |
| Resend | Envío de emails transaccionales |
| Twilio | WhatsApp recordatorios de citas |

### Pagos
| Tecnología | Uso |
|-----------|-----|
| Credomatic / VisaNet | Tarjetas de crédito/débito Guatemala |
| Neonet | Pasarela de pago local Guatemala |
| Stripe | Pagos internacionales (backup) |

### Probador Virtual
| Tecnología | Uso |
|-----------|-----|
| MediaPipe Face Mesh | Detección de rostro en tiempo real |
| TensorFlow.js | Modelos de IA en el navegador |
| face-api.js | Detección facial en foto estática |
| Canvas API | Renderizado de lentes sobre rostro |

### DevOps & Hosting
| Tecnología | Uso |
|-----------|-----|
| Vercel | Hosting del frontend (CI/CD automático) |
| Supabase | Hosting de base de datos y archivos |
| GitHub Actions | Pipeline de CI |

---

## 🏗️ Arquitectura

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENTE (Browser)                     │
│           Next.js 14 · Tailwind · Zustand               │
└──────────────────────┬──────────────────────────────────┘
                       │ HTTPS
┌──────────────────────▼──────────────────────────────────┐
│                  VERCEL (Edge Network)                   │
│                                                         │
│   ┌─────────────────┐    ┌──────────────────────────┐   │
│   │   App Router    │    │     API Routes           │   │
│   │   (SSR / SSG)   │    │  /api/payment/create     │   │
│   │                 │    │  /api/webhooks/payment   │   │
│   │  /              │    │  /api/appointments       │   │
│   │  /catalogo      │    │  /api/products           │   │
│   │  /producto/[id] │    └───────────┬──────────────┘   │
│   │  /carrito       │                │                  │
│   │  /checkout      │                │                  │
│   │  /citas         │                │                  │
│   └─────────────────┘                │                  │
└──────────────────────────────────────┼──────────────────┘
                                       │
         ┌─────────────────────────────┼─────────────────────────┐
         │                             │                         │
┌────────▼────────┐          ┌─────────▼──────────┐   ┌─────────▼────────┐
│    Supabase     │          │  Credomatic /       │   │   Servicios      │
│                 │          │  Neonet             │   │   Externos       │
│  PostgreSQL DB  │          │                     │   │                  │
│  Auth (JWT)     │          │  Portal de pago     │   │  Resend (email)  │
│  Storage        │◄─────────│  Webhook →          │   │  Twilio (WA)     │
│  (imágenes)     │          │  /api/webhooks      │   │  Google Maps     │
└─────────────────┘          └─────────────────────┘   └──────────────────┘
```

---

## 🗄️ Base de Datos

### Diagrama de Relaciones

```
users ──────────────┬──── orders ──── order_items ──── products
  │                 │                                      │
  └──── appointments│                                 categories
                    └──── (billing info embedded in orders)

products ──── product_images (array en JSONB)
products ──── product_colors (array en JSONB)
orders   ──── coupons (FK opcional)
```

### Tablas Principales

```sql
-- USUARIOS
CREATE TABLE users (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email         TEXT UNIQUE NOT NULL,
  full_name     TEXT,
  phone         TEXT,
  address       TEXT,
  avatar_url    TEXT,
  created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- CATEGORÍAS DE PRODUCTOS
CREATE TABLE categories (
  id     SERIAL PRIMARY KEY,
  name   TEXT NOT NULL,       -- "Cuadrados", "Redondos", "Aviador"
  slug   TEXT UNIQUE NOT NULL,
  icon   TEXT
);

-- PRODUCTOS
CREATE TABLE products (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name          TEXT NOT NULL,
  description   TEXT,
  price         NUMERIC(10,2) NOT NULL,
  stock         INTEGER DEFAULT 0,
  category_id   INTEGER REFERENCES categories(id),
  brand         TEXT,
  material      TEXT,          -- "Acetato", "Metal", "TR-90"
  gender        TEXT,          -- "Unisex", "Masculino", "Femenino"
  images        TEXT[],        -- Array de URLs en Supabase Storage
  colors        JSONB,         -- [{"name":"Negro","hex":"#000","stock":5}]
  try_on_image  TEXT,          -- Imagen PNG con fondo transparente para AR
  is_active     BOOLEAN DEFAULT TRUE,
  created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- CITAS MÉDICAS
CREATE TABLE appointments (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id       UUID REFERENCES users(id),
  branch        TEXT NOT NULL,
  date          DATE NOT NULL,
  time          TIME NOT NULL,
  status        TEXT DEFAULT 'pending',
  notes         TEXT,
  reminder_sent BOOLEAN DEFAULT FALSE,
  created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- PEDIDOS
CREATE TABLE orders (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         UUID REFERENCES users(id),
  status          TEXT DEFAULT 'pending',
  subtotal        NUMERIC(10,2),
  discount        NUMERIC(10,2) DEFAULT 0,
  tax             NUMERIC(10,2),    -- IVA 12% Guatemala
  total           NUMERIC(10,2),
  payment_method  TEXT,
  payment_ref     TEXT,
  shipping_addr   JSONB,
  coupon_id       INTEGER REFERENCES coupons(id),
  created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- ITEMS DEL PEDIDO
CREATE TABLE order_items (
  id          SERIAL PRIMARY KEY,
  order_id    UUID REFERENCES orders(id),
  product_id  UUID REFERENCES products(id),
  quantity    INTEGER,
  unit_price  NUMERIC(10,2),
  color       TEXT
);

-- CUPONES DE DESCUENTO
CREATE TABLE coupons (
  id              SERIAL PRIMARY KEY,
  code            TEXT UNIQUE NOT NULL,
  discount_pct    INTEGER,
  max_uses        INTEGER DEFAULT 100,
  used_count      INTEGER DEFAULT 0,
  expires_at      TIMESTAMPTZ
);
```

---

## 💳 Pasarela de Pago

### Flujo de Pago con Credomatic / Neonet

```
Usuario confirma pedido
        │
        ▼
[POST /api/payment/create]
  · Genera referencia única (OV-{orderId}-{timestamp})
  · Guarda pedido con status: 'awaiting_payment'
  · Firma payload con HMAC-SHA256
  · Retorna URL + campos del formulario
        │
        ▼
Usuario es redirigido al portal del banco
  · Ingresa datos de tarjeta en entorno seguro del banco
  · Banco procesa la transacción
        │
    ┌───┴───┐
    │       │
  APROBADO RECHAZADO
    │       │
    ▼       ▼
[POST /api/webhooks/payment]  ← Banco llama a tu servidor
  · Valida firma del webhook
  · APROBADO: status → 'paid', descuenta stock, envía email
  · RECHAZADO: status → 'payment_failed', notifica usuario
  · Responde 200 OK al banco (OBLIGATORIO)
```

### Manejo de Webhooks (código simplificado)

```javascript
// app/api/webhooks/payment/route.js
export async function POST(request) {
  const body   = await request.text();
  const params = new URLSearchParams(body);

  // 1. Verificar autenticidad (NUNCA omitir este paso)
  const isValid = verifyBankSignature(params, process.env.BANK_SECRET);
  if (!isValid) return new Response('Unauthorized', { status: 401 });

  const decision  = params.get('decision');   // ACCEPT | DECLINE | ERROR
  const reference = params.get('req_reference_number');

  if (decision === 'ACCEPT') {
    await supabase.from('orders')
      .update({ status: 'paid', payment_ref: params.get('transaction_id') })
      .eq('payment_ref', reference);

    await sendConfirmationEmail(reference); // Resend
  } else {
    await supabase.from('orders')
      .update({ status: 'payment_failed' })
      .eq('payment_ref', reference);
  }

  return new Response('OK', { status: 200 }); // El banco EXIGE 200
}
```

> **Nota importante:** La confirmación del webhook es la **fuente de verdad**. Nunca actualizar el estado del pedido solo basándose en el redirect del usuario de vuelta al sitio.

---

## 🕶️ Probador Virtual (IA)

### Funcionamiento

El probador virtual usa **MediaPipe Face Mesh** para detectar 468 puntos del rostro en tiempo real a través de la cámara web, y los usa para calcular la posición, tamaño e inclinación exacta donde superponer la imagen del armazón.

```
Cámara activada
      │
      ▼
MediaPipe detecta 468 landmarks del rostro
      │
      ▼
Se extraen puntos clave:
  · Punto 33  → Extremo izquierdo del ojo izquierdo
  · Punto 263 → Extremo derecho del ojo derecho
  · Punto 6   → Puente de la nariz
      │
      ▼
Se calculan:
  · eyeWidth  = distancia entre ojos × 1.4
  · centerX/Y = centro entre ambos ojos
  · angle     = inclinación de la cabeza (atan2)
      │
      ▼
Canvas API dibuja el armazón PNG (fondo transparente)
en la posición y ángulo calculados, en cada frame
```

### Modos disponibles
- **📷 Cámara en vivo** — Detección en tiempo real a 30fps
- **🖼️ Subir foto** — Carga una foto y prueba el armazón (sin necesidad de cámara)

### Requisito para los productos
Cada producto debe tener una imagen `try_on_image` en formato **PNG con fondo transparente** del armazón de frente, que es la que se superpone sobre el rostro detectado.

---

## 🚀 Instalación

### Requisitos previos
- Node.js 18+
- npm o yarn
- Cuenta en Supabase (gratuita)
- Cuenta en Vercel (gratuita)

### Pasos

```bash
# 1. Clonar el repositorio
git clone https://github.com/eddisonjl/optivision.git
cd optivision

# 2. Instalar dependencias
npm install

# 3. Configurar variables de entorno
cp .env.example .env.local
# Editar .env.local con tus credenciales (ver sección siguiente)

# 4. Configurar base de datos
# Ir a Supabase → SQL Editor → pegar contenido de /database/schema.sql

# 5. Correr en desarrollo
npm run dev
```

Abrir en: `http://localhost:3000`

---

## 🔐 Variables de Entorno

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGc...
SUPABASE_SERVICE_ROLE_KEY=eyJhbGc...

# Credomatic / VisaNet
CREDOMATIC_ACCESS_KEY=your_access_key
CREDOMATIC_PROFILE_ID=your_profile_id
CREDOMATIC_SECRET_KEY=your_secret_key

# Neonet
NEONET_MERCHANT_ID=your_merchant_id
NEONET_SECRET_KEY=your_secret_key
NEONET_API_URL=https://api.neonet.com.gt

# Email (Resend)
RESEND_API_KEY=re_xxxxxxxxxxxx

# WhatsApp recordatorios (Twilio)
TWILIO_ACCOUNT_SID=ACxxxxxxxxxxxx
TWILIO_AUTH_TOKEN=your_auth_token
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886

# App
NEXT_PUBLIC_URL=https://optivision.vercel.app
```

> ⚠️ Nunca subas el archivo `.env.local` a GitHub. Ya está incluido en `.gitignore`.

---

## 📁 Estructura del Proyecto

```
optivision/
├── app/                          # Next.js App Router
│   ├── (public)/
│   │   ├── page.jsx              # Home
│   │   ├── catalogo/
│   │   │   └── page.jsx          # Catálogo con filtros
│   │   ├── producto/[id]/
│   │   │   └── page.jsx          # Detalle de producto + probador
│   │   ├── carrito/
│   │   │   └── page.jsx          # Carrito de compras
│   │   ├── checkout/
│   │   │   └── page.jsx          # Proceso de pago
│   │   └── citas/
│   │       └── page.jsx          # Agendamiento de citas
│   ├── (auth)/
│   │   ├── login/page.jsx
│   │   └── cuenta/page.jsx
│   └── api/
│       ├── payment/
│       │   └── create/route.js   # Crear sesión de pago
│       ├── webhooks/
│       │   └── payment/route.js  # Recibir confirmación del banco
│       ├── appointments/
│       │   └── route.js          # CRUD de citas
│       └── products/
│           └── route.js          # API de productos
│
├── components/
│   ├── ui/                       # Componentes reutilizables
│   │   ├── Button.jsx
│   │   ├── Badge.jsx
│   │   └── Modal.jsx
│   ├── catalog/
│   │   ├── ProductCard.jsx
│   │   ├── FilterSidebar.jsx
│   │   └── ProductGrid.jsx
│   ├── cart/
│   │   ├── CartItem.jsx
│   │   └── CartSummary.jsx
│   ├── tryon/
│   │   ├── VirtualTryOn.jsx      # Probador con cámara (MediaPipe)
│   │   └── PhotoTryOn.jsx        # Probador con foto estática
│   └── appointments/
│       ├── Calendar.jsx
│       └── BranchSelector.jsx
│
├── lib/
│   ├── supabase.js               # Cliente de Supabase
│   ├── payment.js                # Lógica de firmas de pago
│   └── email.js                  # Templates de email (Resend)
│
├── store/
│   └── cartStore.js              # Estado global del carrito (Zustand)
│
├── database/
│   └── schema.sql                # Schema completo de la base de datos
│
├── public/
│   └── glasses/                  # PNGs de armazones para el probador
│
├── .env.example                  # Variables de entorno de ejemplo
├── .gitignore
├── next.config.js
├── tailwind.config.js
└── package.json
```

---

## 🔄 Flujo de Usuario

### Compra de producto
```
Home → Catálogo (filtra por estilo) → Detalle del producto
  → Activa probador virtual → Le gustan los lentes → Agrega al carrito
  → Checkout → Selecciona Credomatic → Portal del banco
  → Pago aprobado → Email de confirmación → Rastrea pedido en /cuenta
```

### Agendamiento de cita
```
NavBar "Agendar cita" → Selecciona sucursal → Elige fecha y hora
  → Ingresa datos → Confirmación por email → Recordatorio WhatsApp 24h antes
```

---

## 🔮 Roadmap

- [x] Arquitectura y diseño de base de datos
- [x] Diseño de flujos de usuario
- [x] Integración de pasarelas de pago GT (Neonet + Credomatic)
- [x] Probador virtual con MediaPipe
- [ ] Desarrollo del frontend (Next.js + Tailwind)
- [ ] Integración con Supabase
- [ ] Módulo de citas con calendario
- [ ] Emails transaccionales con Resend
- [ ] Recordatorios WhatsApp con Twilio
- [ ] Deploy en Vercel
- [ ] Panel de administración
- [ ] App móvil (React Native)

---

## 👨‍💻 Autor

**eddisonjl** — Fullstack Developer · Guatemala 🇬🇹

[![GitHub](https://img.shields.io/badge/GitHub-eddisonjl-181717?style=for-the-badge&logo=github)](https://github.com/eddisonjl)

---

## 📄 Licencia

Este proyecto está bajo la licencia MIT.

---

<div align="center">
  <i>Proyecto de portafolio — Diseñado y desarrollado por eddisonjl</i>
</div>
