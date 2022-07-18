# filebrowser

This `docker-compose.yaml` file use alpine-based image of filebrowser.

Please create `/path/database.db` and `/path/settings.json` before you start the container.

And you shoud fill `settings.json` file with the following data:

```json
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/database.db",
  "root": "/data"
}
```
