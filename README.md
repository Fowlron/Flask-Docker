# Flask Docker
A starting point to develop flask websites. Made mostly thanks to [this](https://testdriven.io/blog/dockerizing-flask-with-postgres-gunicorn-and-nginx/) post. 

# Technology Stack
The development environment uses flask's built-in server to serve both dynamic and static pages and postgres with sqlalchemy for databases.

The production environment uses nginx, gunicorn and flask, with postgres and sqlalchemy for databases.

# Development
On run, the development environment will recreate and seed the databases by executing `python manage.py create_db` and `python manage.py seed_db`. Make sure those functions exist.

Note that uploaded files while in development will be saved on the host's media folder, since the entire project is a volume. If you then execute the production build without deleting those files, files uploaded during development can bleed into production. 

To start and stop the development environment, use the commands
```
docker-compose up --build
docker-compose down -v
```
Make sure you use the `--build` flag if last execution was of the production server, or it won't build the web service and use the built production docker. 

# Production
To start and stop the production environment, use the commands
```
docker-compose -f docker-compose.prod.yml up -d --build
docker-compose -f docker-compose.prod.yml down
```
Note that postgres data and uploaded files are persistent on the production environment. To delete them, execute the `down` command with the `-v` flag.

# Notes
You must create the `.env.prod` and `.env.prod.db` files. Sample files are provided. Make sure you change the database password. 

You might want to change the `services/nginx/nginx.conf` and `services/web/project/__init__.py` files so that files uploaded to `/services/web/project/media` aren't statically served if users are expected to upload private files. 

Nginx's `client_max_body_size` is left at default. For larger uploads, you can add `client_max_body_size 2M;` (or whatever size you need) to the `server` context on `services/nginx/nginx.conf`
