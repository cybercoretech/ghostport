# GhostPort status IPC v1

GhostPort's daemon exposes a read-only, one-request JSON status API over its
Unix socket at `~/.local/state/ghostport/ghostport.sock`. The socket is created
inside a `0700` directory and has `0600` permissions. The connection closes
after one snapshot. This API is local to the daemon's operating-system account;
it is not an HTTP listener and should not be published to a network.

The same schema is available from `ghostport status --json`. Every snapshot
contains `schema_version: 1`; additive fields remain in v1, and an incompatible
schema change requires a new version. Example response:

```json
{
  "schema_version": 1,
  "role": "client",
  "uptime_secs": 3600,
  "control_connected": true,
  "control_connected_since_secs_ago": 120,
  "control_peer_addr": "192.0.2.10:9000",
  "control_peer_name": null,
  "links": [
    {
      "id": "holodrive",
      "mode": "reverse",
      "active_streams": 0,
      "total_streams": 12,
      "bytes_forward": 2048,
      "bytes_back": 65536
    }
  ]
}
```

`control_peer_addr` is diagnostic data and can reveal a peer's address. Keep
access to the socket owner-only. Integrations should expose status only to
their own authenticated users and must not proxy configuration, private keys,
or tunnel-control operations through this interface.
