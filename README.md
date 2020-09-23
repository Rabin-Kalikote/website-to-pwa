# Change your Website to PWA
In this documentation, I have given you the step by step process to convert your static website into full functional Progressive Web Application (PWA).

You can watch the explanation and demo of this documentation and process [in this video on YouTube](https://youtu.be/novOo2cjWbs).

So, how do you convert your website into PWA?
### Here are what you have to do:
1. Create manifest.json and call it at index.html file.
    manifest.json file includes the information about the PWA like its name, theme, icons, and so on.
2. Generate app icons and add them in index.html and manifest.json files.
    We will generate the icons at [favicon-generator.org](https://www.favicon-generator.org).
3. Create service-worker file as sw.js.
    Servive Worker file works to communicate between your application and the network.
4. Register service-worker at main.js file.

### Let's dive into these steps.
1. Create manifest.json and call it at index.html file.
  - Create manifest.json file at the root directory and add the following details.
  ```
  {
    "name": "App Name",
    "short_name": "App",
    "start_url": "/index.html",
    "display": "standalone",
    "background_color": "#527cec",
    "theme_color": "#f8f9fa",
    "orientation": "portrait-primary",
    "icons": [
      {
        "src": "/img/favicon/android-icon-72x72.png",
        "type": "image/png",
        "sizes": "72x72"
      },
      {
        "src": "/img/favicon/android-icon-96x96.png",
        "type": "image/png",
        "sizes": "96x96"
      },
      {
       "src": "/img/favicon/android-icon-36x36.png",
       "sizes": "36x36",
       "type": "image/png",
       "density": "0.75"
      },
      {
       "src": "/img/favicon/android-icon-48x48.png",
       "sizes": "48x48",
       "type": "image/png",
       "density": "1.0"
      },
      {
       "src": "/img/favicon/android-icon-72x72.png",
       "sizes": "72x72",
       "type": "image/png",
       "density": "1.5"
      },
      {
       "src": "/img/favicon/android-icon-96x96.png",
       "sizes": "96x96",
       "type": "image/png",
       "density": "2.0"
      },
      {
       "src": "/img/favicon/android-icon-144x144.png",
       "sizes": "144x144",
       "type": "image/png",
       "density": "3.0"
      },
      {
       "src": "/img/favicon/android-icon-192x192.png",
       "sizes": "192x192",
       "type": "image/png",
       "density": "4.0"
     },
      {
        "src": "/img/favicon/apple-icon-152x152.png",
        "type": "image/png",
        "sizes": "152x152"
      },
      {
        "src": "/img/favicon/apple-icon-384x384.png",
        "type": "image/png",
        "sizes": "384x384"
      },
      {
        "src": "/img/favicon/apple-icon-512x512.png",
        "type": "image/png",
        "sizes": "512x512"
      }
    ]
  }
  ```
  - In the head section of index.html, call manifest.json file with following tag.
  ```
  <link rel="manifest" href="/manifest.json">
  ```

2. Generate app icons and add them in index.html and manifest.json files.
  - Go to [favicon-generator.org](https://www.favicon-generator.org) and generate your icons.
  - Add them to your index.html. Note that they are already added to manifest.json file.
  ```
  <meta name="apple-mobile-web-app-status-bar" content="#e8edfc">
  <link rel="apple-touch-icon" sizes="57x57" href="img/favicon/apple-icon-57x57.png">
  <link rel="apple-touch-icon" sizes="60x60" href="img/favicon/apple-icon-60x60.png">
  <link rel="apple-touch-icon" sizes="72x72" href="img/favicon/apple-icon-72x72.png">
  <link rel="apple-touch-icon" sizes="76x76" href="img/favicon/apple-icon-76x76.png">
  <link rel="apple-touch-icon" sizes="114x114" href="img/favicon/apple-icon-114x114.png">
  <link rel="apple-touch-icon" sizes="120x120" href="img/favicon/apple-icon-120x120.png">
  <link rel="apple-touch-icon" sizes="144x144" href="img/favicon/apple-icon-144x144.png">
  <link rel="apple-touch-icon" sizes="152x152" href="img/favicon/apple-icon-152x152.png">
  <link rel="apple-touch-icon" sizes="180x180" href="img/favicon/apple-icon-180x180.png">
  <link rel="icon" type="image/png" sizes="192x192"  href="img/favicon/android-icon-192x192.png">
  <link rel="icon" type="image/png" sizes="32x32" href="img/favicon/favicon-32x32.png">
  <link rel="icon" type="image/png" sizes="96x96" href="img/favicon/favicon-96x96.png">
  <link rel="icon" type="image/png" sizes="16x16" href="img/favicon/favicon-16x16.png">
  <meta name="msapplication-TileColor" content="#527cec">
  <meta name="msapplication-TileImage" content="img/favicon/ms-icon-144x144.png">
  <meta name="theme-color" content="#f8f9fa">
  ```
3. Create service-worker file as sw.js.
  - Create sw.js at the root directory and define caching and different events.
  ```
  const staticCacheName = 'site-static-v1';
  const assets = [
    '/',
    '/index.html',
    '/js/functions.js',
    '/css/styles.css',
    '/img/images.jpg',
    'https://fonts.googleapis.com/css2?family=ifanyfont',
  ];

  // install event
  self.addEventListener('install', evt => {
    evt.waitUntil(
      caches.open(staticCacheName).then((cache) => {
        console.log('caching shell assets');
        cache.addAll(assets);
      })
    );
  });

  // activate event
  self.addEventListener('activate', evt => {
    evt.waitUntil(
      caches.keys().then(keys => {
        return Promise.all(keys
          .filter(key => key !== staticCacheName)
          .map(key => caches.delete(key))
        );
      })
    );
  });

  // fetch event
  self.addEventListener('fetch', evt => {
    evt.respondWith(
      caches.match(evt.request).then(cacheRes => {
        return cacheRes || fetch(evt.request).then(fetchRes => {
          return caches.open(dynamicCacheName).then(cache => {
            cache.put(evt.request.url, fetchRes.clone());
            return fetchRes;
          })
        });
      })
    );
  });

  ```

4. Register service-worker at main.js file.
  - Call sw.js at main.js file.
  ```
  if('serviceWorker' in navigator){
    navigator.serviceWorker.register('/sw.js')
      .then(reg => console.log('service worker registered'))
      .catch(err => console.log('service worker not registered', err));
  }
  ```
