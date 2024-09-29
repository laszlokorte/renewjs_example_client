# Demo Client for Renew Web Editor

![Demo Client for Renew Web Editor](./images/logo.png)

This is an example implementation of a simple client for the Renew Web editor.
It makes use of the [live_state](https://hexdocs.pm/live_state/readme.html) protocol to subscribe to a Phoenix channel via websocket.

## Run

Install dependencies:

```sh
yarn install
```

Run dev http server:

```sh
yarn dev --open
```

The paste the Server address and a document id into the form and connect.

This requires a renew web editor server to be running.

---

[www.laszlokorte.de](//www.laszlokorte.de)