Based on this video https://www.youtube.com/watch?v=kh1GMRFkzT4&t=547s
- set a `docker-compose.yaml`file
```yaml
services:
	dev:
		image: "rust:latest"
	volumes:
		- ./local_file:/container_file
	environment:
		- USER=scythe
```
- image just pulls from the latest rust official docker image. No dockerfile needed!
- volume defines which folder will overlap with the container.cle

with the compose file created, use the following command to run it:
```
docker-compose run --rm dev
```

If you're getting a permission denied error when you try to run the command, give it more permissions:
```bash
sudo chmod 666 /var/run/docker.sock
```

Also, if you are using a `build` (which is the service for a locally defined dockerfile) instead of an `image`,  use the following command:
```shell
docker-compose build --no-cache dev
```
