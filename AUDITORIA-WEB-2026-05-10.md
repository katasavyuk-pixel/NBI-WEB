# Auditoría Web NBI · Nexora Business Intelligence

**Fecha:** 2026-05-10
**Proyecto:** Landing pre-launch (4 páginas, HTML estático puro)
**Auditor:** Claude (Opus 4.7)
**Método:** Lighthouse 12 (headless Chrome) + análisis estático del código fuente

---

## 1. Resumen ejecutivo

### Veredicto deploy-ready: 🟡 **CASI** — 3 bloqueantes operacionales pendientes

La web está **técnicamente bien construida**: zero-dependency, ligera (240 KB total), SEO sólido, accesibilidad alta y performance aceptable. No hay ningún defecto de código grave. **Lo que falta para salir a producción es operacional**, no técnico.

### Tabla semáforo por categoría

| Categoría | Score | Estado | Comentario |
|-----------|-------|--------|------------|
| **SEO técnico** | 9/10 | 🟢 | Meta tags, OG, schema, canonicals impecables. Falta `sameAs` y dominio. |
| **Accesibilidad** | 7.5/10 | 🟡 | A11y 94/100. Contraste de texto secundario apenas por debajo de WCAG AA. |
| **Performance** | 7/10 | 🟡 | Lighthouse 86/100. LCP 3.3 s (objetivo <2.5 s). Mejorable con compresión + minificación. |
| **Seguridad** | 8/10 | 🟢 | Sin credenciales secretas; email público es OK; falta CSP (depende del host). |
| **Calidad de código** | 7/10 | 🟡 | HTML semántico ✓; CSS y JS inline en 1 archivo (refactor opcional). |
| **Responsive/Mobile** | 9/10 | 🟢 | Viewport, media queries, clamp(), menú móvil. Falta srcset. |
| **Contenido y conversión** | 9/10 | 🟢 | Propuesta de valor clara, CTAs jerarquizados, FAQ trabajada. |
| **Deploy readiness** | 4/10 | 🔴 | 3 bloqueantes: dominio, fotos founders, validación legal. |

### Métricas Lighthouse (las 4 páginas)

| Página | Performance | Accessibility | Best Practices | SEO |
|--------|:-----------:|:-------------:|:--------------:|:---:|
| `/` (index) | **86** | **94** | **93** | **100** |
| `/aviso-legal.html` | 94 | 95 | 100 | 66 ⚠️ |
| `/privacidad.html` | 92 | 96 | 100 | 66 ⚠️ |
| `/cookies.html` | 95 | 95 | 100 | 66 ⚠️ |

> ⚠️ **El SEO 66 en páginas legales NO es un fallo real.** Lighthouse penaliza el `<meta name="robots" content="noindex,follow">` que es intencional (no queremos indexar privacidad/cookies). Ignorar esa métrica.

### Top 5 hallazgos críticos

1. 🔴 **Fotos de founders ausentes** → `/images/katalin.jpg` y `/images/keoma.jpg` devuelven HTTP 404. Bloquea credibilidad.
2. 🔴 **Páginas legales en borrador** → marcadores `[pendiente]` para NIF, domicilio fiscal y titular en `aviso-legal.html:169-172` y `privacidad.html:165,228`. Riesgo legal.
3. 🔴 **Dominio `ianexora.com` sin contratar** → todos los canonicals, sitemap y OG apuntan a un dominio inexistente. Bloquea deploy.
4. 🟠 **Contraste insuficiente en texto secundario** → `--ink-mute: #6b7794` sobre `#ffffff` da ratio 4.47 (mínimo WCAG AA: 4.5). Afecta a >10 elementos en hero/live-panel/secciones.
5. 🟠 **LCP 3.3 s en index** → por encima del umbral "Good" de 2.5 s. Causa: render delay del 86 % (2.8 s) por CSS render-blocking + cantidad de animaciones.

---

## 2. Métricas Lighthouse detalladas

### Core Web Vitals (lab data, headless desktop)

| Página | FCP | LCP | TBT | CLS | Speed Index |
|--------|:---:|:---:|:---:|:---:|:-----------:|
| index | 3.3 s 🟡 | 3.3 s 🟡 | 0 ms 🟢 | 0.019 🟢 | 3.3 s 🟡 |
| aviso-legal | 2.5 s 🟢 | 2.5 s 🟢 | 0 ms 🟢 | 0.013 🟢 | 2.5 s 🟢 |
| privacidad | 2.7 s 🟡 | 2.7 s 🟡 | 0 ms 🟢 | 0.004 🟢 | 2.7 s 🟢 |
| cookies | 2.4 s 🟢 | 2.4 s 🟢 | 0 ms 🟢 | 0.035 🟢 | 2.4 s 🟢 |

> **TBT 0 ms** y **CLS < 0.05** en las 4 páginas — excelente. El cuello de botella es **render-blocking CSS** (Google Fonts + 1.100 líneas de CSS inline).

**LCP element en index:** [`<h1 class="r d1">`](index.html:1461) — correcto semánticamente. El 86 % del LCP es "Render Delay", síntoma de CSS render-blocking, no de tamaño.

**Reportes Lighthouse en bruto** (HTML interactivo, abrir en navegador):
- [lighthouse-reports/index.report.html](lighthouse-reports/index.report.html)
- [lighthouse-reports/aviso-legal.report.html](lighthouse-reports/aviso-legal.report.html)
- [lighthouse-reports/privacidad.report.html](lighthouse-reports/privacidad.report.html)
- [lighthouse-reports/cookies.report.html](lighthouse-reports/cookies.report.html)

---

## 3. Hallazgos por categoría

### 3.1. SEO técnico y on-page

| Hallazgo | Severidad | Ubicación | Solución |
|----------|:---------:|-----------|----------|
| Schema.org `sameAs: []` vacío | 🟡 Medio | [index.html:62](index.html:62) | Añadir URLs LinkedIn de Katalin y Keoma cuando estén |
| Title de 89 chars (óptimo 50-60) | 🟢 Bajo | [index.html:6](index.html:6) | Acortar a "NBI · Agentes IA para PYMEs en España" (47 chars). Opcional. |
| Meta description 162 chars | 🟢 Bajo | [index.html:7](index.html:7) | Recortar 2-7 chars para evitar truncado en SERP |
| Sitemap apunta a dominio inexistente | 🔴 Crítico | [sitemap.xml](sitemap.xml) | Esperar contratación de `ianexora.com` |
| Canonicals apuntan a dominio inexistente | 🔴 Crítico | [index.html:11](index.html:11) y los 3 legales | Idem |
| Sin LocalBusiness schema | 🟢 Bajo | [index.html:37-64](index.html:37) | Añadir `@type: LocalBusiness` si interesa SEO local Gipuzkoa |
| **Aspectos correctos** | | | |
| Title único, OG completo, Twitter Cards | 🟢 OK | head | — |
| Schema.org Organization con founders | 🟢 OK | [index.html:37-64](index.html:37) | — |
| `lang="es"` + `og:locale="es_ES"` | 🟢 OK | [index.html:2,23](index.html:2) | — |
| `robots.txt` permisivo + sitemap | 🟢 OK | [robots.txt](robots.txt) | — |
| Páginas legales con `noindex,follow` | 🟢 OK | [aviso-legal.html:8](aviso-legal.html:8) | — |
| `og:image` 1200×630 declarado correctamente | 🟢 OK | [index.html:19-22](index.html:19) | — |

### 3.2. Accesibilidad (WCAG 2.1 AA)

| Hallazgo | Severidad | Ubicación | Solución |
|----------|:---------:|-----------|----------|
| **Contraste insuficiente** en `.l`, `.ml`, `<small>`, `<h5>` (ratio 4.47, mínimo 4.5) | 🟠 Alto | CSS `--ink-mute: #6b7794` — uso masivo en cards, footer, live-panel | Cambiar a `#666f8a` (ratio ≥ 4.5) o subir tamaño de fuente a 14 px |
| **Contraste `.live-label`** (`#0ea271` sobre `#f5f7fb` = 3.04) | 🟠 Alto | [index.html:1507](index.html:1507) | Usar verde más oscuro `#0a7a4f` o cambiar fondo |
| **Contraste en `<code>` de páginas legales** | 🟡 Medio | aviso-legal/privacidad/cookies | Ajustar color del código inline |
| **Heading-order**: `<h5>` en footer sin `<h4>` previo | 🟡 Medio | [index.html:2145,2151,2158,2164](index.html:2145) | Cambiar a `<h4>` (es lo apropiado en jerarquía del footer) |
| **Label-content-name-mismatch** en brand link | 🟡 Medio | [index.html:1418](index.html:1418) (aria-label "NBI · Nexora..." mientras texto visible es "NBI") | Quitar `aria-label` y dejar el texto visible, o sincronizar |
| Formulario sin `<label>` visible | 🟢 Bajo | [index.html:2110-2117](index.html:2110) | El `aria-label="Tu email"` es válido para a11y; añadir label visible mejora UX |
| Sin skip-link "Saltar a contenido" | 🟢 Bajo | global | Añadir `<a class="skip-link" href="#top">Saltar al contenido</a>` |
| **Aspectos correctos** | | | |
| 27 atributos `aria-*` (aria-label, aria-expanded, aria-live) | 🟢 OK | global | — |
| `role="region"` en cookie banner, `role="alert"` en form error | 🟢 OK | [index.html:2117,2185](index.html:2117) | — |
| `aria-hidden="true"` en SVGs decorativos | 🟢 OK | varios | — |
| `aria-label` en burger menu y scroll-to-top | 🟢 OK | [index.html:1439,2178](index.html:1439) | — |
| Imágenes con `alt` descriptivo | 🟢 OK | [index.html:2028,2041](index.html:2028) | — |
| `:focus-visible` con outline visible | 🟢 OK | CSS de páginas legales | — |

### 3.3. Performance

| Hallazgo | Severidad | Métrica | Solución |
|----------|:---------:|---------|----------|
| **Sin compresión de texto (gzip/brotli)** | 🟠 Alto | Saving 450 ms | Vercel/Netlify la activan automáticamente al deployar — **no es problema** una vez en producción |
| **Render-blocking CSS** (Google Fonts CSS + `<style>` inline) | 🟠 Alto | Render Delay 2.8 s del LCP | (a) Inline el CSS crítico above-the-fold, (b) cargar Fonts con `<link rel="preload" as="style" onload="this.rel='stylesheet'">` ya parcialmente implementado |
| **CSS no minificado** (1.100+ líneas inline) | 🟡 Medio | ~30 KB ahorrables | Si se mantiene CSS inline: minificar via build step (PostCSS/lightningcss). Opcional para zero-build. |
| **JS no minificado** (305 líneas inline + image-slot.js 32 KB) | 🟡 Medio | ~10 KB ahorrables | Idem |
| **Unused CSS** (50 % score) | 🟡 Medio | Lighthouse | Hay reglas para componentes que no se usan en index. Tree-shake si se moderniza el build |
| **Sin cache headers configurados** (TTL ineficiente) | 🟡 Medio | Score 50% | Vercel/Netlify configuran cache de 1 año en static assets — se resuelve automático al deployar |
| **Sin lazy loading en imágenes founders** | 🟡 Medio | [index.html:2028,2041](index.html:2028) | Añadir `loading="lazy"` y `decoding="async"` |
| **Sin formatos modernos (WebP/AVIF)** | 🟢 Bajo | 2 imágenes JPG | Convertir o usar `<picture>` con fallback. Bajo impacto: solo 2 imágenes |
| **Sin width/height en imágenes** | 🟢 Bajo | [index.html:2028,2041](index.html:2028) | Añadir `width="1200" height="1500"` para evitar CLS |
| **Aspectos correctos** | | | |
| TBT 0 ms en las 4 páginas | 🟢 OK | — | JS no bloquea el hilo principal |
| CLS muy bajo (0.004 - 0.035) | 🟢 OK | — | Layout estable |
| `preconnect` a fonts.googleapis y fonts.gstatic | 🟢 OK | [index.html:66-67](index.html:66) | — |
| `preload` del CSS de Google Fonts | 🟢 OK | [index.html:68](index.html:68) | — |
| `async` en Calendly, `defer` en image-slot.js | 🟢 OK | [index.html:73,76](index.html:73) | — |
| `display=swap` en fuentes | 🟢 OK | [index.html:69](index.html:69) | — |
| 240 KB total descargado | 🟢 OK | — | Muy ligero |

### 3.4. Seguridad

| Hallazgo | Severidad | Ubicación | Solución |
|----------|:---------:|-----------|----------|
| **Calendly cookie issue** (SameSite warning) | 🟡 Medio | Calendly widget | Es de Calendly, no del sitio. Asegurarse de mencionarlo en `cookies.html` (ya está mencionado ✓) |
| **Sin Content-Security-Policy header** | 🟡 Medio | depende del host | Añadir CSP en `vercel.json` o `_headers` (Netlify) tras deploy. Plantilla recomendada incluida en sección 5 |
| **Sin X-Frame-Options / X-Content-Type-Options** | 🟡 Medio | depende del host | Idem |
| **Validación email solo cliente** | 🟢 Bajo | [index.html:2435](index.html:2435) | Adecuado: el form solo abre Calendly, no envía a servidor propio |
| **Aspectos correctos** | | | |
| **Sin credenciales/API keys hardcodeadas** | 🟢 OK | — | Email público (`nexoraprocesos@gmail.com`) es un dato de contacto intencional, no un secreto |
| **`target="_blank"` + `rel="noopener"`** | 🟢 OK | [privacidad.html:213,cookies.html:182](privacidad.html:213) | Verificado |
| **localStorage con try/catch** (modo privado) | 🟢 OK | [index.html:2486-2493](index.html:2486) | — |
| **Sin innerHTML con datos de usuario** | 🟢 OK | — | El único innerHTML usa template literals con eventos predefinidos (sin XSS) |
| **HTTPS forzado en CDNs externos** | 🟢 OK | Google Fonts, Calendly | — |
| **Sin mixed content** | 🟢 OK | — | Todos los recursos son `https://` |

### 3.5. Calidad de código

| Hallazgo | Severidad | Ubicación | Solución |
|----------|:---------:|-----------|----------|
| **`index.html` monolítico** (2.500 líneas: HTML + 1.100 CSS + 305 JS) | 🟡 Medio | [index.html](index.html) | Mantener mientras sea zero-dependency. Si crece: extraer `styles.css` y `app.js` |
| `console.warn` en `image-slot.js` | 🟢 Bajo | [image-slot.js:384,492](image-slot.js:384) | Son advertencias legítimas para dev. Aceptable mantener |
| Email regex simple (no RFC completo) | 🟢 Bajo | [index.html:2435](index.html:2435) | Suficiente para validación frontend; aceptable |
| **Aspectos correctos** | | | |
| HTML5 semántico (`<header>`, `<nav>`, `<section>`, `<article>`, `<footer>`) | 🟢 OK | global | — |
| Variables CSS (`--ink`, `--cyan`, etc.) bien organizadas | 🟢 OK | [index.html:87-115](index.html:87) | — |
| Web component nativo (`image-slot`) en lugar de framework | 🟢 OK | [image-slot.js](image-slot.js) | Decisión arquitectónica coherente |
| Comentarios estructurales (`/* ───── tokens ─────  */`) | 🟢 OK | global | Excelente legibilidad |
| Sin `console.log` en producción del index | 🟢 OK | — | — |
| Zero dependencias externas (excepto fonts y Calendly) | 🟢 OK | — | — |

### 3.6. Responsive y mobile

| Hallazgo | Severidad | Ubicación | Solución |
|----------|:---------:|-----------|----------|
| Sin `srcset`/`<picture>` para imágenes | 🟢 Bajo | [index.html:2028,2041](index.html:2028) | Solo 2 imágenes; ofrecer 2 tamaños mejora mobile |
| **Aspectos correctos** | | | |
| `<meta viewport>` correcto | 🟢 OK | [index.html:5](index.html:5) | — |
| `clamp()` en tipografía responsive | 🟢 OK | global | — |
| Media queries `@media (max-width: 880px)` | 🟢 OK | global | — |
| Menú móvil (`#burgerBtn`, `.mobile-menu`) | 🟢 OK | [index.html:1439-1450](index.html:1439) | — |
| SVG escalable para logo | 🟢 OK | — | — |
| Padding responsive `clamp(20px, 4vw, 56px)` | 🟢 OK | — | — |

### 3.7. Contenido y conversión

| Hallazgo | Severidad | Comentario |
|----------|:---------:|------------|
| **Aspectos correctos** | | |
| Propuesta de valor clara en hero | 🟢 OK | "Agentes IA que trabajan por tu empresa. Sin contratar más personas." |
| 3 productos diferenciados (Sistema, Diagnóstico, Custom) | 🟢 OK | Estructura de embudo: Diagnóstico (entrada) → Sistema/Custom (cierre) |
| FAQ aborda 6 objeciones reales | 🟢 OK | Tiempo, conocimientos, ROI, integraciones, seguridad, sectores |
| CTAs jerarquizados | 🟢 OK | "Solicitar diagnóstico" como CTA primario en hero, nav, sticky y final |
| Animación demo en vivo | 🟢 OK | Genera credibilidad operativa |
| Métricas objetivo, no aspiracionales | 🟢 OK | "96 % reducción", "<0.5 % error", "24/7" — verificables |

> El contenido es **muy bueno**. No hay hallazgos negativos relevantes en esta categoría.

### 3.8. Deploy readiness

| Hallazgo | Severidad | Ubicación | Solución |
|----------|:---------:|-----------|----------|
| **Dominio `ianexora.com` no contratado** | 🔴 Bloqueante | global | Contratar y configurar DNS en Vercel/Netlify |
| **Fotos founders ausentes** (HTTP 404) | 🔴 Bloqueante | [index.html:2028,2041](index.html:2028) → `/images/katalin.jpg` y `/images/keoma.jpg` | Subir 2 JPG (~1200×1500, ratio 4:5) |
| **Marcadores `[pendiente]` en aviso-legal.html** | 🔴 Bloqueante | [aviso-legal.html:169-172](aviso-legal.html:169) | Completar NIF, domicilio, titular tras alta autónoma + revisión jurídica |
| **Marcadores `[pendiente]` en privacidad.html** | 🔴 Bloqueante | [privacidad.html:165,228](privacidad.html:165) | Completar NIF y proveedor hosting |
| Plausible/Umami comentados | 🟡 Medio | [index.html:78-83](index.html:78) | Activar tras deploy — recomendado Plausible (no requiere consentimiento RGPD) |
| Sin página 404 personalizada | 🟢 Bajo | — | Crear `404.html` con CTA "volver al inicio". Vercel/Netlify la usan automáticamente |
| Sin `manifest.json` (PWA) | 🟢 Bajo | — | Opcional. Solo si planeáis instalación móvil |
| Sin `vercel.json` / `_headers` | 🟢 Bajo | — | Crear para forzar headers de seguridad (CSP, HSTS) post-deploy |

---

## 4. Quick wins (< 30 min cada uno, impacto alto)

| # | Quick win | Esfuerzo | Impacto |
|---|-----------|----------|---------|
| 1 | Cambiar `--ink-mute` de `#6b7794` a `#5a6580` (ratio 5.5:1) | 5 min | A11y 94 → 100 |
| 2 | Cambiar `<h5>` por `<h4>` en footer (4 ocurrencias) | 5 min | Heading-order ✓ |
| 3 | Quitar `aria-label` del brand link y dejar texto natural | 2 min | label-content-name-mismatch ✓ |
| 4 | Añadir `loading="lazy"` y `width/height` a fotos founders | 3 min | Mejora LCP y elimina CLS futuro |
| 5 | Acortar title a "NBI · Agentes IA para PYMEs en España" | 1 min | Title óptimo en SERP |
| 6 | Añadir skip-link al inicio del `<body>` | 5 min | A11y teclado ✓ |
| 7 | Cambiar `.live-label` color a verde más oscuro `#0a7a4f` | 2 min | Contraste pasa |
| 8 | Subir 2 fotos founders a `/images/` | 5 min | Elimina 2 errores 404 |

**Si haces solo los 8 quick wins anteriores, los scores Lighthouse de index pasarían a aproximadamente:**
- Performance: 86 → 88
- Accessibility: 94 → 100 ✅
- Best Practices: 93 → 100 ✅
- SEO: 100 → 100 ✅

---

## 5. Plan de acción priorizado

### 🔴 P0 — Bloqueantes deploy (no salir sin esto)

- [ ] **Contratar dominio `ianexora.com`** y configurar DNS apuntando a Vercel/Netlify
- [ ] **Subir fotos founders** (`/images/katalin.jpg`, `/images/keoma.jpg`, ~1200×1500, JPG/WebP)
- [ ] **Validar páginas legales con asesoría jurídica** y completar marcadores `[pendiente]`:
  - `aviso-legal.html`: titular, NIF, domicilio fiscal
  - `privacidad.html`: NIF, proveedor hosting (Vercel/Netlify)
- [ ] **Deploy en Vercel o Netlify** (drag & drop de la carpeta) y verificar que las 4 URLs cargan sobre HTTPS

### 🟠 P1 — Pre-launch recomendado (mejora calidad antes de salir)

- [ ] Aplicar los **8 quick wins** de la sección 4 (≈ 30 min en total)
- [ ] **Activar Plausible Analytics** (descomentar línea 79 de `index.html`)
- [ ] **Crear `vercel.json` o `_headers`** con security headers:
  ```json
  {
    "headers": [{
      "source": "/(.*)",
      "headers": [
        { "key": "Content-Security-Policy", "value": "default-src 'self'; script-src 'self' 'unsafe-inline' https://assets.calendly.com https://plausible.io; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://assets.calendly.com; font-src https://fonts.gstatic.com; img-src 'self' data:; frame-src https://calendly.com; connect-src https://plausible.io" },
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" },
        { "key": "Permissions-Policy", "value": "camera=(), microphone=(), geolocation=()" }
      ]
    }]
  }
  ```
- [ ] **Crear `404.html` personalizada** (mismo layout que páginas legales, con CTA al home)
- [ ] **Completar `sameAs`** en JSON-LD con URLs LinkedIn de los founders
- [ ] **Test cross-browser** mínimo: Chrome, Safari (iOS y macOS), Firefox

### 🟢 P2 — Post-launch (mejoras continuas)

- [ ] Convertir fotos founders a **WebP/AVIF** con `<picture>` fallback
- [ ] Generar variantes responsive con `srcset` (480w, 1200w)
- [ ] Considerar **minificación CSS/JS** si se añade un build step
- [ ] Revisar **unused CSS** (Lighthouse marca 50 % score) y purgar
- [ ] Añadir **BreadcrumbList schema** si se publican posts/casos de uso
- [ ] Considerar **HSTS preload** una vez confirmado HTTPS estable
- [ ] Añadir **`manifest.json`** + service worker si se quiere PWA
- [ ] Configurar **alerts de uptime** (UptimeRobot, Better Stack)
- [ ] **A/B test** del CTA primario tras 1–2 meses con datos reales

---

## 6. Recomendaciones estratégicas

### SEO a medio plazo (3–6 meses)

- **Crear sección /casos-de-uso/** con 3-5 estudios de caso reales (logística, atención cliente, back-office). Cada caso con `Article` schema. Mejora long-tail.
- **Blog técnico ligero** — 1 post/mes sobre integración con TMS/ERP españoles (Holded, Sage, Odoo). Captura búsquedas "integrar IA con [ERP]".
- **Schema `LocalBusiness`** si quieres rankear en búsquedas locales (Gipuzkoa/País Vasco).

### Performance escalable

- Mientras la web esté **bajo 3 páginas reales + landing**, el setup zero-dependency es óptimo.
- Si crece a >10 páginas, considerar **Astro** (preserva HTML estático, añade componentización) — minimal disruption del estilo actual.
- Mantener **TBT 0 ms y CLS < 0.05** como invariantes.

### Mejoras de conversión

- Añadir **proof social** cuando exista: 2-3 logos de clientes reales (con permiso) sustituyen 100 líneas de copy.
- **Calendly embed inline** en vez de popup en la sección final puede subir la conversión (menos clics).
- Considerar un **lead magnet** ("Plantilla: Calculadora ROI de automatización") para capturar emails sin compromiso de demo.

---

## 7. Anexos

### 7.1. Lista de archivos analizados

| Archivo | Líneas | Tamaño | Estado |
|---------|-------:|-------:|--------|
| [index.html](index.html) | 2.500 | 112 KB | ✅ Auditado completo |
| [image-slot.js](image-slot.js) | 641 | 32 KB | ✅ Auditado completo |
| [aviso-legal.html](aviso-legal.html) | 220 | 16 KB | ⚠️ Borrador con `[pendiente]` |
| [privacidad.html](privacidad.html) | 265 | 16 KB | ⚠️ Borrador con `[pendiente]` |
| [cookies.html](cookies.html) | 249 | 16 KB | ✅ Completa |
| [sitemap.xml](sitemap.xml) | 26 | 1 KB | ✅ Estructura correcta, dominio pendiente |
| [robots.txt](robots.txt) | 4 | <1 KB | ✅ Correcto |
| [favicon.svg](favicon.svg) | — | 1.5 KB | ✅ |
| [og-image.svg](og-image.svg) | — | 5.1 KB | ✅ 1200×630 |
| `/images/katalin.jpg` | — | — | ❌ Ausente (HTTP 404) |
| `/images/keoma.jpg` | — | — | ❌ Ausente (HTTP 404) |

### 7.2. Reportes Lighthouse (en bruto)

Los HTMLs interactivos están en `/lighthouse-reports/`:

```bash
open "/Users/katamv/Documents/NBI Empresa/Pagina WEB/NBI-WEB-main/lighthouse-reports/index.report.html"
open "/Users/katamv/Documents/NBI Empresa/Pagina WEB/NBI-WEB-main/lighthouse-reports/aviso-legal.report.html"
open "/Users/katamv/Documents/NBI Empresa/Pagina WEB/NBI-WEB-main/lighthouse-reports/privacidad.report.html"
open "/Users/katamv/Documents/NBI Empresa/Pagina WEB/NBI-WEB-main/lighthouse-reports/cookies.report.html"
```

### 7.3. Checklist final pre-deploy

```
🔴 P0 — BLOQUEANTES (todos en ✅ antes de hacer deploy):
[ ] Dominio ianexora.com contratado y DNS apuntando al host
[ ] Foto katalin.jpg subida a /images/
[ ] Foto keoma.jpg subida a /images/
[ ] aviso-legal.html sin marcadores [pendiente]
[ ] privacidad.html sin marcadores [pendiente]
[ ] Validación de las 3 páginas legales por asesor jurídico
[ ] Deploy en Vercel/Netlify y HTTPS verificado en las 4 URLs

🟠 P1 — ALTAMENTE RECOMENDADOS:
[ ] 8 quick wins aplicados (sección 4)
[ ] Plausible Analytics activo
[ ] vercel.json / _headers con CSP y security headers
[ ] 404.html personalizada
[ ] sameAs en JSON-LD con LinkedIn URLs
[ ] Test manual cross-browser (Chrome, Safari, Firefox)

🟢 P2 — POST-LAUNCH:
[ ] WebP/AVIF + srcset en imágenes
[ ] Casos de uso / blog mínimo
[ ] Alerts uptime
[ ] Auditoría Lighthouse en producción confirma scores ≥ 90
```

---

**Fin del reporte.** Para detalle audit-by-audit, abrir los HTMLs de Lighthouse en `/lighthouse-reports/`.
