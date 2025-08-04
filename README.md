# Vamos atualizar o projeto para ser um PWA funcional básico e incluir algumas funcionalidades dinâmicas.
# Isso incluirá:
# - Um manifest.json para definir o app como PWA
# - Um service-worker.js para funcionamento offline
# - Pequenas melhorias no JavaScript para interações mais realistas

# Adicionar manifest.json
manifest_json = """
{
  "name": "App Turística",
  "short_name": "Turismo",
  "start_url": "index.html",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#1976d2",
  "icons": [
    {
      "src": "icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
"""

# Adicionar service-worker.js
service_worker_js = """
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('app-cache').then(function(cache) {
      return cache.addAll([
        '/',
        '/index.html',
        '/style.css',
        '/app.js'
      ]);
    })
  );
});

self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request);
    })
  );
});
"""

# Atualizar app.js com funcionalidades dinâmicas e registo do service worker
app_js_pwa = app_js + """
// Registo do Service Worker
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('service-worker.js')
    .then(() => console.log('Service Worker registado com sucesso!'))
    .catch(err => console.error('Erro ao registar Service Worker:', err));
}
"""

# Guardar os novos ficheiros
with open(f"{project_path}/manifest.json", "w") as f:
    f.write(manifest_json)

with open(f"{project_path}/service-worker.js", "w") as f:
    f.write(service_worker_js)

with open(f"{project_path}/app.js", "w") as f:
    f.write(app_js_pwa)

# Adicionar também uma pasta 'icons' com imagens genéricas
icons_path = os.path.join(project_path, "icons")
os.makedirs(icons_path, exist_ok=True)

# Criar dois ícones em branco (simulados) como placeholders
from PIL import Image

for size in [192, 512]:
    icon = Image.new("RGBA", (size, size), (25, 118, 210, 255))  # Azul padrão
    icon.save(f"{icons_path}/icon-{size}.png")

# Atualizar o ZIP com os novos ficheiros
with ZipFile(zip_path, 'w') as zipf:
    for foldername, subfolders, filenames in os.walk(project_path):
        for filename in filenames:
            file_path = os.path.join(foldername, filename)
            arcname = os.path.relpath(file_path, project_path)
            zipf.write(file_path, arcname=arcname)

zip_path
