# LSPS6 LSP meta-data

| Name    | `meta-data` |
| ------- |---------------|
| Version | 1             |
| Status  | Draft         |

LSPS6 requires [BOLT8][] as a transport layer.

[BOLT8]: https://github.com/lightning/bolts/blob/master/08-transport.md

## Motivation

Sometimes the client wants to obtain extra information from the LSP.
There might be information that is not available through regular LSPS messages.
An example for such information could be the pricing strategy for LSPS1.
This extra information can be in the form of a URL or an email address.
We need a way to prevent spoofing.

### Actors

The 'LSP' is the API provider, and acts as the server.
The 'client' is the API consumer.

## DNS Public Key Verification

[BIP353][] provides a way to discover payment instructions over DNS.
Analogous to this the LSP SHOULD create a DNS TXT record in the form of
`lsps.acme.com. 3600 IN TXT "public_key:0326e6...2833a3"` for public key verification.

[BIP353]: https://github.com/bitcoin/bips/blob/master/bip-0353.mediawiki

## API

### lsps6.get_info

`lsps6.get_info` provides generic information about the LSP like icon, logo, etc.

It is possible to confirm authority over DNS when there are URLs (or an email address) in the response.
To confirm authority the client MUST verify the public key(s) from the domain's DNS record(s)
with the public key that sends the [Bolt8][] messages.

**Request** No parameters needed.

**Response**

```JSON
{
  "url": "https://acme.com/lsp",
  "dns": true,
  "icon": "https://acme.com/lsp-icon.png",
  "logo": "https://acme.com/lsp-logo.png",
  "email": "lsp-support@acme.com"
}
```

- `url` LSP SHOULD include the URL of its website.
- `dns` LSP MAY include a boolean to indicate if the LSP has DNS Public Key Verification.
The default is that it does not support it.
- `icon` LSP MAY include the URL of its icon.
Icon is a squarely shaped smaller representation of the logo.
Often the icon is the logo without the text to fit in a square shape.
- `logo` LSP MAY include the URL of its logo.
- `email` LSP MAY include its support email.

### lsps6.get_lsp_info

`lsps6.get_lsp_info` provides generic additional information about an LSPS spec.

**Request**
```JSON
{
  "spec": "LSPS1"
}
```

**Response**

```JSON
{
  "short": "Short description",
  "long": "Longer description of the details"
}
```

LSP MUST always respond even when there is no support for a spec. LSP MUST include the following fields
(field values can be empty):
- `short` is a short description of the additional details with no formatting. With a maximum of 64 characters.
- `long` is a long description of the additional details with md formatting. With a maximum of 1024 characters.
