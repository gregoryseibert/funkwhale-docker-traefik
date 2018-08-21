# funkwhale-docker-traefik
A collection of files for a funkwhale setup with docker and traefik reverse proxy

What you have to / should edit:

.env
  FUNKWHALE_VERSION=0.16.2
  FUNKWHALE_URL=https://music.example.com
  MEDIA_ROOT=/var/data/funkwhale/data/media
  STATIC_ROOT=/var/data/funkwhale/data/static
  DJANGO_ALLOWED_HOSTS=music.example.com
  DJANGO_SECRET_KEY=k9ZWwsHWUgDWQc8RwbXeEJkh5PhVdsGGwxGHamXH5HnG4
  MUSIC_DIRECTORY_PATH=/var/data/funkwhale/data/music
  MUSIC_DIRECTORY_SERVE_PATH=/music
  
traefik.toml
  users = ["user:passwordhashtogenerate"]
  certFile = "/cert.pem"
  keyFile = "/privkey.pem"
  domain = "example.com"
  
nginx.conf
  server_name music.example.com;
  
docker-compose.yml
  traefik:
    - /etc/letsencrypt/live/example.com/fullchain.pem:/cert.pem:ro
    - /etc/letsencrypt/live/example.com/privkey.pem:/privkey.pem:ro
  postgres:
    - /var/data/funkwhale/data/postgres:/var/lib/postgresql/data
  redis:
    - /var/data/funkwhale/data/redis:/data
  funkwhale-frontend:
    - /var/data/funkwhale/data/media:/srv/funkwhale/data/media
    - /var/data/funkwhale/data/static:/srv/funkwhale/data/static
    - /var/data/funkwhale/data/music:/srv/funkwhale/data/music:ro
    - traefik.backend=music.example.com
    - traefik.frontend.rule=Host:music.example.com
