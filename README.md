# nginx-web

This is a simple example of a HAProxy load balancer serving two nginx backends with ssl termination.The build and push of the images is done by a GH actions pipeline.

## To run
(Optional) Generate a self-signed certificate for the load balancer - https://letsencrypt.org/docs/certificates-for-localhost/

```bash
openssl req -x509 -out localhost.crt -keyout localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```
Generate a pem file from the key and certificate
```bash
cat localhost.crt localhost.key > localhost.pem
```

Run the docker-compose file
```bash
docker-compose up
```

Access the load balancer on https://localhost
```console
➜  ~ curl -k https://localhost/
<h1>Server 1</h1>

➜  ~ curl -k https://localhost/
<h1>Server 2</h1>
```

## Considerations
- For the purposes of this example, the nginx backends are built and pushed to Docker Hub by github actions. The Dockerfile for the backends can be found in the `backend<1/2>` directory.
- HAProxy is pulled from the official Docker Hub image, and not pushed via the ci pipeline.
- Round robin load balancing is used for the backends
- This was run on m1 architecture however amd64 was specified in the docker-compose file


