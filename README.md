# docker-caddy

The official [Caddy](https://hub.docker.com/_/caddy) Docker image with the added [caddy-dns/cloudflare](https://github.com/caddy-dns/cloudflare) module for DNS-01 ACME validation support. This image does not change anything with Caddy except replacing the `caddy` binary. Built for all supported platforms!

```sh
# GHCR
docker pull ghcr.io/betaly/caddy:latest
```

## Tags

The following tags are available for the `betaly/caddy` image.

- `latest`
- `<version>` (eg: `2.6.4`, including: `2.6`, `2`, etc.)

## Usage

Since this is built off the official Docker image all of the same Volumes, Environment variables, etc. can be used with this container. Please refer to the official [Caddy](https://hub.docker.com/_/caddy) Docker image and [docs](https://caddyserver.com/docs/) for more information on using Caddy.

Simply create the container as usual and include your `CF_API_TOKEN` (email no longer required for API Tokens). We can utilizing Caddy's support for [Environment varaiables](https://caddyserver.com/docs/caddyfile/concepts#environment-variables) to pass these values into our `Caddyfile`.

```sh
docker run --rm -it \
  --name caddy \
  -p 80:80 \
  -p 443:443 \
  -v caddy_data:/data \
  -v caddy_config:/config \
  -v $PWD/Caddyfile:/etc/caddy/Caddyfile \
  -e CF_API_TOKEN=UhKLc...JD9jk \
  betaly/caddy:latest
```

Then set the global [acme_dns](https://caddyserver.com/docs/caddyfile/options#acme-dns) directive in your `Caddyfile`

```Caddyfile
{
  acme_dns cloudflare {env.CF_API_TOKEN}
}
```

or via JSON

```json
{
  "module": "acme",
  "challenges": {
    "dns": {
      "provider": {
        "name": "cloudflare",
        "api_token": "{env.CF_API_TOKEN}"
      }
    }
  }
}
```

See the [caddy-dns/cloudflare](https://github.com/caddy-dns/cloudflare) module and [`tls`](https://caddyserver.com/docs/caddyfile/directives/tls#tls) directive for advanced usage.

### Creating a Cloudflare API Token

You can generate a Cloudflare API token via the Cloudflare web console using the following steps:

1. Login to your [Dashboard](https://dash.cloudflare.com/)
2. Go to [Account Profile](https://dash.cloudflare.com/profile) > [API Tokens](https://dash.cloudflare.com/profile/api-tokens)
3. Click "Create token" (Use the "Create Custom Token" option)
4. Grant the following permissions:
   - `Zone > Zone > Read`
   - `Zone > DNS > Edit`

## Building

You can easily build the Docker image locally by doing

```sh
docker build -t docker-caddy .
```

## Container signatures

All container images will be automatically signed via [Cosign](https://docs.sigstore.dev/cosign/overview/) using [keyless signatures](https://docs.sigstore.dev/cosign/keyless/). You can use the following command to verify the integrity of these images yourself.

```sh
cosign verify \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  --certificate-identity-regexp https://github.com/betaly/docker-caddy/.github/workflows/ \
  betaly/caddy:latest
```

## Contributing

Feel free to contribute and make things better by opening an [Issue](https://github.com/betaly/docker-caddy/issues) or [Pull Request](https://github.com/betaly/docker-caddy/pulls).

## License

See [LICENSE](https://github.com/betaly/docker-caddy/blob/main/LICENSE).
