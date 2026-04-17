# Pràctica: Proxies amb Nginx

Aquesta pràctica consisteix en el muntatge d'una infraestructura de servidors web Apache amb un proxy invers Nginx que realitza balanceig de càrrega i memòria cau.

## Estructura del projecte

- `docker-compose.yml`: Configuració dels contenidors.
- `html/`: Contingut web compartit (HTML, CSS, imatges, vídeos).
- `nginx-config/`: Configuració de Nginx.

## Fases de desenvolupament

### Fase 1 — Un sol node web
S'ha creat un contenidor Apache que serveix una pàgina web bàsica.

### Fase 2 — Segon node web
S'ha afegit un segon contenidor Apache. Per distingir quin node respon, s'utilitza la capçalera `X-Upstream` configurada al proxy invers.

### Fase 3 — Volum compartit
Ambdós contenidors Apache comparteixen el directori `./html` del host, garantint que serveixen exactament el mateix contingut.

### Fase 4 — Proxy invers amb balanceig
S'ha configurat Nginx com a punt d'entrada únic. Utilitza un bloc `upstream` per distribuir les peticions entre `apache1` i `apache2` mitjançant l'algorisme Round Robin (per defecte).

### Fase 5 — Memòria cau
S'ha habilitat la memòria cau de proxy a Nginx per als fitxers estàtics (imatges i vídeos).
- S'ha definit `proxy_cache_path`.
- S'utilitza la capçalera `X-Cache-Status` per verificar si la resposta ve de la memòria cau (`HIT`) o no (`MISS`).

## Com provar-ho

1. Aixecar els contenidors:
   ```bash
   docker-compose up -d
   ```
2. Accedir a `http://localhost`.
3. Recarregar la pàgina per veure com canvia el node que respon (veure la secció "Aquest node és:").
4. Per verificar la memòria cau:
   - Obrir les eines de desenvolupador del navegador (F12).
   - Mirar la xarxa (Network).
   - Cercar el fitxer `test-image.png`.
   - La primera vegada ha de sortir `X-Cache-Status: MISS`.
   - La segona vegada ha de sortir `X-Cache-Status: HIT`.
