# funkwhale-docker-traefik
A collection of files for a funkwhale setup with docker and traefik reverse proxy

The funkwhale-frontend has to be in the same network as traefik (as well as other containers, which should be reverse proxied with traefik).
And the funkwhale-frontend also has to be in the same network with at least the funkwhale api.
But I have all funkwhale containers in the same network, just in case.

What you have to / should edit:

**.env**
<pre>
FUNKWHALE_VERSION=0.16.2
FUNKWHALE_URL=https://music.example.com
MEDIA_ROOT=/var/data/funkwhale/data/media
STATIC_ROOT=/var/data/funkwhale/data/static
DJANGO_ALLOWED_HOSTS=music.example.com
DJANGO_SECRET_KEY=k9ZWwsHWUgDWQc8RwbXeEJkh5PhVdsGGwxGHamXH5HnG4
MUSIC_DIRECTORY_PATH=/var/data/funkwhale/data/music
MUSIC_DIRECTORY_SERVE_PATH=/music
</pre>  
**traefik.toml**
<pre>
users = ["user:passwordhashtogenerate"]
certFile = "/cert.pem"
keyFile = "/privkey.pem"
domain = "example.com"
</pre>  
**nginx.conf**
<pre>
server_name music.example.com;
</pre>  
**docker-compose.yml**
<pre>
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
</pre>
