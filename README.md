# Página Web NBI — Nexora Business Intelligence

Landing page oficial de NBI publicada en **`ianexora.com`**. HTML estático autocontenido — sin framework, sin build step, deploy con `drag & drop`.

---

## Archivos

| Archivo | Para qué |
|---|---|
| `index.html` | Landing principal (HTML + CSS + JS en un solo archivo) |
| `aviso-legal.html` | Aviso legal LSSI — borrador |
| `privacidad.html` | Política de privacidad RGPD — borrador |
| `cookies.html` | Política de cookies — informativa, no requiere consentimiento bloqueante |
| `image-slot.js` | Web component para slots de imágenes en productos |
| `favicon.svg` | Icono del sitio (gradient cyan→púrpura con "N") |
| `og-image.svg` | Imagen Open Graph 1200×630 para WhatsApp / LinkedIn / Twitter |
| `robots.txt` | Permite indexación + apunta al sitemap |
| `sitemap.xml` | Mapa del sitio para buscadores |
| `contexto-empresa.md` | Brief maestro: copy, productos, bios, contacto |
| `images/katalin.jpg` | Foto founder Katalin — **pendiente añadir** |
| `images/keoma.jpg` | Foto founder Keoma — **pendiente añadir** |

---

## Previsualizar en local

Abrir directamente en el browser:

```bash
# macOS
open pagina-web/index.html

# Windows (PowerShell)
start pagina-web/index.html
```

O arrastrar `index.html` al browser. No necesita servidor.

---

## Pendientes antes de lanzar

### Bloqueantes (no se puede deployar sin esto)
- [ ] Contratar dominio `ianexora.com` y configurar DNS apuntando al hosting
- [ ] Validar `aviso-legal.html`, `privacidad.html` y `cookies.html` con asesoría jurídica — son borradores
- [ ] Completar campos `[pendiente]` en aviso legal y privacidad cuando Katalin esté de alta como autónoma (NIF, domicilio fiscal)
- [ ] Verificar que `https://calendly.com/nexoraprocesos/30min` está activo

### Recomendados (mejor con esto antes de pasarlo a leads)
- [ ] Subir `images/katalin.jpg` (ver `images/README.md` para especificaciones — formato 4:5, ~1200×1500px)
- [ ] Subir `images/keoma.jpg` (mismas especificaciones)
- [ ] Actualizar `sameAs` del Schema.org en `index.html` con URLs reales de LinkedIn cuando estén listas
- [ ] Activar Plausible o Umami para analítica (snippet ya está comentado en `<head>` del index)

### Cosméticos (post-launch ok)
- [ ] Generar `apple-touch-icon.png` 180×180 si se quiere icono específico para iOS
- [ ] Validar Open Graph en `https://www.opengraph.xyz/` con la URL final
- [ ] Validar Schema.org en `https://validator.schema.org/`
- [ ] Lighthouse audit en producción (objetivo: a11y ≥90, perf ≥85)

---

## Cómo deployar

### Opción A — Vercel (recomendado, gratis para sitios estáticos)
```bash
cd pagina-web
vercel deploy --prod
```
O conectar el repo a Vercel y marcar `pagina-web/` como root directory. Configurar dominio `ianexora.com` desde Vercel → Project Settings → Domains.

### Opción B — Netlify drag & drop
1. Ir a `app.netlify.com`
2. Arrastrar la carpeta `pagina-web/` completa al área de deploy
3. En 30 segundos hay URL pública
4. Configurar `ianexora.com` desde Site Settings → Domain management

### Opción C — GitHub Pages
1. Crear repo separado `nbi-web` con el contenido de `pagina-web/` en la raíz
2. Activar GitHub Pages desde Settings → Pages → branch `main` / root
3. Configurar custom domain en Settings → Pages → Custom domain

---

## Activar la analítica

El snippet de Plausible está en el `<head>` del `index.html` comentado. Para activar:

1. Crear cuenta en `plausible.io` (~9€/mes) y añadir el dominio `ianexora.com`
2. Descomentar la línea correspondiente en `<head>`
3. Re-deploy

Alternativa gratuita: levantar `Umami` self-hosted (Docker en VPS) y descomentar la línea de Umami en su lugar.

Ambas opciones son **sin cookies** y **RGPD-compliant**: no requieren consentimiento bloqueante. La política de cookies ya las menciona.

---

## Cómo actualizar el contenido

Todo el copy de la home está en `index.html`. Las secciones están marcadas con comentarios:

```
<!-- ───────── nav ───────── -->
<!-- ───────── hero ───────── -->
<!-- ───────── problema ───────── -->
<!-- ───────── proceso ───────── -->
<!-- ───────── productos ───────── -->
<!-- ───────── resultados ───────── -->
<!-- ───────── founders ───────── -->
<!-- ───────── faq ───────── -->
<!-- ───────── final cta ───────── -->
<!-- ───────── footer ───────── -->
<!-- ───────── scroll to top ───────── -->
<!-- ───────── cookie banner ───────── -->
```

Para cambios de contexto de empresa (precios internos, productos, bios, dominio), editar primero `contexto-empresa.md` y luego reflejar los cambios en `index.html`.

---

## Estado

| Item | Estado |
|---|---|
| Landing diseñada y funcional | ✅ |
| Páginas legales (borradores) | ✅ |
| SEO básico (meta, OG, schema, sitemap) | ✅ |
| Banner cookies informativo | ✅ |
| Favicon + OG image | ✅ |
| Fotos founders | ❌ Pendiente |
| LinkedIn URLs | ❌ Pendiente |
| Dominio ianexora.com contratado | ❌ Pendiente |
| Analítica activada | ❌ Pendiente (snippet listo) |
| Deploy en producción | ❌ Pendiente |

*Última revisión: 9 de mayo de 2026*
