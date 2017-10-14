Guia - Workshop: Progressive Web Apps

Preparando el Entorno

Instalamos Google Chrome
Instalamos Ligthouse, como complemento de google
Instalamos Git
Instalamos Node
Instalamos Atom que usaremos como editor de código
Verificando el entorno

Iniciamos Git Bash
Verificamos que se haya instalado correctamente Node ejecutando node --version
Verficamos que se haya instalado correctamente nmp ejecutando npm --version
Iniciamos Atom
Iniciamos Google Chrome
Hacemos un recorrido sobre los paneles del Web Developers Tools
Instalamos live-server

Ejecutamos el comando npm install -g live-server
Verificamos que se haya instalado correctamente ejecutando liver-server --version
Creando el entorno de trabajo

Creamos una carpeta con nombre pwatz2017 en el escritorio
Abrimos la carpeta con el explorador de windows y creamos dentro 3 carpetas con los siguientes nombres images, css y js
Abrir la carpeta con el editor de código Atom.
Creamos nuestra PWA

Creamos el archivo HTML index.html con una plantilla básica

Creamos el archivo HTML en la ruta raíz
Creamos la estructura básica del documento HTML
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>TZ 2017 by Jala - PWA</title>
  </head>
  <body>
    <h1>Mi PWA</h1>
  </body>
</html>
Iniciamos el servidor y verificamos nuestra PWA

Desde Git Bash y con la ruta en la carpeta donde se encuentra nuestro proyecto ejecutamos el comando live-server
Ejecutamos la herramienta Lighthouse para verificar la evaluación que realiza entorno a los parámetros relacionados con PWA
Añadimos algunas etiquetas meta a nuestro documento HTML

    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- Informa al navegador de que se trata de una PWA -->
    <meta name="mobile-web-app-capable" content="yes">
    <!-- Informa al navegador de iOS que se trata de una PWA -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <!-- Define el color del template -->
    <meta name="theme-color" content="#ff00ff">
Volvemos a evaluar con Ligthouse

Creando y enlazando el archivo manifest.json

Creamos el archivo con el siguiente contenido
{
  "short_name": "TZ 2017",
  "name": "TechZone 2017 by Jala",
  "icons": [
    {
      "src": "/images/512x512.png",
      "type": "image/png",
      "sizes": "512x512"
    },
    {
      "src": "/images/192x192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "/images/128x128.png",
      "type": "image/png",
      "sizes": "128x128"
    }
  ],
  "start_url": "/",
  "orientation": "portrait",
  "display": "standalone",
  "theme_color": "#ff00ff",
  "background_color": "#000000"
}
Descargamos los íconos y los ubicamos en la carpeta images para descargar seguir el siguiente link íconos
Enlazamos el archivo manifest.json desde el header de nuestro documento HTML, utilizando la etiqueta:
  <link rel="manifest" href="manifest.json">
Analizamos con Ligthouse
Service Workers

Creamos el archivo app.js dentro del directorio js para registrar nuestro service worker, a continuación el contenido del archivo:
if ('serviceWorker' in navigator) {

  navigator.serviceWorker
    .register('./service-worker.js', { scope: './' })
    .then(function(registration) {
      console.log("Service Worker Registered");
    })
    .catch(function(err) {
      console.log("Service Worker Failed to Register", err);
    })

}
Creamos el archivo service-worker.js en la raiz de nuestro proyecto y añadimos el siguiente contenido:
// Set a name for the current cache
var cacheName = 'v1';

// Default files to always cache
var cacheFiles = [
	'./',
	'./index.html',
	'./js/app.js'
]


self.addEventListener('install', function(e) {
    console.log('[ServiceWorker] Installed');

    // e.waitUntil Delays the event until the Promise is resolved
    e.waitUntil(

    	// Open the cache
	    caches.open(cacheName).then(function(cache) {

	    	// Add all the default files to the cache
			console.log('[ServiceWorker] Caching cacheFiles');
			return cache.addAll(cacheFiles);
	    })
	); // end e.waitUntil
});


self.addEventListener('activate', function(e) {
    console.log('[ServiceWorker] Activated');

    e.waitUntil(

    	// Get all the cache keys (cacheName)
		caches.keys().then(function(cacheNames) {
			return Promise.all(cacheNames.map(function(thisCacheName) {

				// If a cached item is saved under a previous cacheName
				if (thisCacheName !== cacheName) {

					// Delete that cached file
					console.log('[ServiceWorker] Removing Cached Files from Cache - ', thisCacheName);
					return caches.delete(thisCacheName);
				}
			}));
		})
	); // end e.waitUntil

});


self.addEventListener('fetch', function(e) {
	console.log('[ServiceWorker] Fetch', e.request.url);

	// e.respondWidth Responds to the fetch event
	e.respondWith(

		// Check in cache for the request being made
		caches.match(e.request)


			.then(function(response) {

				// If the request is in the cache
				if ( response ) {
					console.log("[ServiceWorker] Found in Cache", e.request.url, response);
					// Return the cached version
					return response;
				}

				// If the request is NOT in the cache, fetch and cache

				var requestClone = e.request.clone();
				fetch(requestClone)
					.then(function(response) {

						if ( !response ) {
							console.log("[ServiceWorker] No response from fetch ")
							return response;
						}

						var responseClone = response.clone();

						//  Open the cache
						caches.open(cacheName).then(function(cache) {

							// Put the fetched response in the cache
							cache.put(e.request, responseClone);
							console.log('[ServiceWorker] New Data Cached', e.request.url);

							// Return the response
							return response;

				        }); // end caches.open

					})
					.catch(function(err) {
						console.log('[ServiceWorker] Error Fetching & Caching New Data', err);
					});


			}) // end caches.match(e.request)
	); // end e.respondWith
});
Enlazamos nuestro documento HTML con el archivo app.js usando el siguiente código
<script src="js/app.js"></script>
Analizamos con Ligthouse
Cargamos nuestra PWA a Github Pages

Se necesita una cuenta en GitHub
Creamos una nueva organización
Creamos un repositorio con el siguiente nombre nombreorganizacion.github.io
Cargamos los archivos de nuestra PWA.
Accedemos a nuestra PWA dese nuestros dispositivos móviles usando la URL de nuestra págian en GitHub.
