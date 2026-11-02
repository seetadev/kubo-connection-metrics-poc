# Kubo Inbound Connection Resource Manager Metrics PoC

A proof of concept to reproduce and investigate the inbound connection reservation leak reported in [kubo#3564](https://github.com/ipfs/kubo/issues/3564).

## Objective

Monitor and log changes in Kubo's libp2p Resource Manager and Swarm connection metrics to identify discrepancies between inbound connection reservations and live inbound connections.

The investigation focuses on connections that are successfully upgraded but closed before reaching `Swarm.addConn` / `listener.Accept()`, potentially leaving resource manager reservations unreleased.

## Metrics to Monitor

- `libp2p_rcmgr_connections` — inbound reservations by scope (`system`, `transient`).
- `libp2p_rcmgr_fds` — file descriptor reservations.
- `libp2p_swarm_connections_opened_total` — inbound connections opened.
- `libp2p_swarm_connections_closed_total` — inbound connections closed.
- `libp2p_rcmgr_blocked_resources` — blocked inbound connection and FD allocations.

## Investigation

- Track inbound connection reservations against live Swarm connections.
- Monitor FD reservation changes alongside connection lifecycle events.
- Observe metric behaviour across TCP, WebSocket, QUIC and other transports.
- Investigate connections closed before being accepted by the Swarm.
- Compare behaviour before and after the proposed upgrader fix in [go-libp2p#3565](https://github.com/libp2p/go-libp2p/pull/3565).

## Expected Outcome

Identify the conditions responsible for leaked inbound connection reservations, validate the suspected resource cleanup path, and verify that the proposed fix prevents resource exhaustion over time.

**Related issue:** [ipfs/kubo#3564](https://github.com/ipfs/kubo/issues/3564)  
**Related PR:** [go-libp2p#3565](https://github.com/libp2p/go-libp2p/pull/3565)



