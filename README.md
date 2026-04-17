# Pràctica: Proxies amb Nginx

Aquesta pràctica consisteix en el muntatge d'una infraestructura de servidors web Apache amb un proxy invers Nginx que realitza balanceig de càrrega i memòria cau.

## Estructura del projecte

- `docker-compose.yml`: Configuració dels contenidors.
- `html/`: Contingut web compartit (HTML, CSS, imatges, vídeos).
- `nginx-config/`: Configuració de Nginx.

## Fases de desenvolupament

### Fase 1 — Un sol node web
S'ha creat un contenidor Apache que serveix una pàgina web bàsica.
<img width="842" height="603" alt="imagen" src="https://github.com/user-attachments/assets/e8758d6a-12ae-44ec-a5b4-d1a6e9dfbdbf" />


### Fase 2 — Segon node web
S'ha afegit un segon contenidor Apache. Per distingir quin node respon, s'utilitza la capçalera `X-Upstream` configurada al proxy invers. Per diferenciar el node he afegit "Aquest node és... indicant el node actual
<img width="842" height="603" alt="imagen" src="https://github.com/user-attachments/assets/75b7b0b0-f54d-4f74-a329-0ade3d132d35" />
<img width="827" height="213" alt="imagen" src="https://github.com/user-attachments/assets/32fe34c7-e4de-4fdf-acf9-7cf0d6a08981" />


### Fase 3 — Volum compartit
Ambdós contenidors Apache comparteixen el directori `./html` del host, garantint que serveixen exactament el mateix contingut.
<img width="380" height="312" alt="imagen" src="https://github.com/user-attachments/assets/1a23d8ee-4dd9-4424-a2f1-1c832cfeb200" />

### Fase 4 — Proxy invers amb balanceig
S'ha configurat Nginx com a punt d'entrada únic. Utilitza un bloc `upstream` per distribuir les peticions entre `apache1` i `apache2` mitjançant l'algorisme Round Robin (per defecte).
<img width="360" height="396" alt="imagen" src="https://github.com/user-attachments/assets/b4f2fda3-53f1-4c5b-9444-b56c5ec6ac23" />


### Fase 5 — Memòria cau
S'ha habilitat la memòria cau de proxy a Nginx per als fitxers estàtics (imatges i vídeos).
- S'ha definit `proxy_cache_path`.
- S'utilitza la capçalera `X-Cache-Status` per verificar si la resposta ve de la memòria cau (`HIT`) o no (`MISS`).
<img width="484" height="468" alt="imagen" src="https://github.com/user-attachments/assets/4c7d3c26-bfa4-4e38-a998-f9be6ed8d2a0" />

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
