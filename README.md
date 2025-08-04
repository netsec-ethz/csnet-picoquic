# csnet-picoquic

This is a fork of [picoquic](https://github.com/private-octopus/picoquic) that enables QUIC over SCION using [csnet](https://github.com/scionproto-contrib/csnet).

## Requirements

1. Install a release build of csnet: [csnet - Building and Installation](https://github.com/scionproto-contrib/csnet?tab=readme-ov-file#building-and-installation)

## Building

To build the picoquic fork run:
```bash
cmake -DPICOQUIC_FETCH_PTLS=Y \
      -DUSE_SCION=ON \
      -DSCION_LIBRARIES=<CSNET_INSTALL_DIR>/lib/libscion.a;<CSNET_INSTALL_DIR>/lib/libprotobuf-c.a;<CSNET_INSTALL_DIR>/lib/libnghttp2.a;<CSNET_INSTALL_DIR>/lib/libz.a \
      -DSCION_INCLUDE_DIR=<CSNET_INSTALL_DIR>/include \
      -DENABLE_ASAN=OFF \
      -DENABLE_UBSAN=OFF \
      .
      
cmake --build .
```

## Running the Example

1. **Start the local SCION network**  
   In the cloned `csnet` repository:
   ```bash
   sudo ./scripts/run-testnet.sh
   ```
2. **Custom Topology**  
   If you are not using the default topology, overwrite the `topology.json` file in this repository with your modified version.
3. **Start the sample server**
    ```bash
   ./picoquic_sample server 4443 certs/cert.pem certs/key.pem doc
   ```
4. **Start the sample client**
   ```bash
   ./picoquic_sample client 127.0.0.1 4443 /tmp socket_loop.md
   ```
   
## Future Work

- Make the topology path dynamically configurable in `picoquic_packet_loop_open_socket()`
  - possibly provide two topology files: one for IPv4 sockets and one for IPv6 sockets
- Properly clean up the topology and network objects when closing the socket
- Make the destination IA a dynamic parameter in `picoquic_sendmsg()`
- Implement the control message `UDP_SEGMENT` in csnet and use it in `picoquic_sendmsg()` and `picoquic_recvmsg()`
  - see https://github.com/scionproto-contrib/csnet/issues/73