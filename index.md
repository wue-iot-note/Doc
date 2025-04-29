---
title: (UDP 5683) CoAP
parent: Transport Layer
---

# Constrained Application Protocol
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

### Overview
The Constrained Application Protocol (CoAP) is a lightweight, RESTful protocol designed for resource-constrained devices and networks, such as those commonly found in the Internet of Things (IoT). It operates over UDP to reduce overhead and supports methods similar to HTTP (GET, POST, PUT, DELETE) for interacting with device resources via URI paths. CoAP includes features like confirmable and non-confirmable message types, multicast support for discovery, and a compact binary format that minimizes data transmission.

CoAP is designed for efficiency and reliability in low-power, lossy networks, making it ideal for use in smart homes, industrial sensors, and other M2M applications. It supports security through DTLS and OSCORE, and is often used as the transport layer for higher-level protocols like LwM2M. Despite its simplicity, it enables rich interactions between IoT devices in a scalable and standardized manner.

## Python Script
```
import asyncio
import sys
from aiocoap import Context, Message, GET

async def main():
    if len(sys.argv) != 3:
        print("Usage: python3 query_coap.py <ip> <resource-path>")
        print("Example: python3 query_coap.py 192.168.1.10 /.well-known/core")
        print("\nLwM2M Device Info Query Examples:")
        print("  /3/0/0 -> Manufacturer")
        print("  /3/0/1 -> Model number")
        print("  /3/0/3 -> Firmware version")
        sys.exit(1)

    ip = sys.argv[1]
    resource = sys.argv[2].lstrip('/')  # Remove leading slash if present

    # Build full CoAP URI
    uri = f'coap://{ip}/{resource}'

    # Create CoAP client context
    context = await Context.create_client_context()

    # Build GET request
    request = Message(code=GET, uri=uri)

    try:
        # Send request and await response
        response = await context.request(request).response
        print(f"Response from {uri}:\n{response.payload.decode()}")
    except Exception as e:
        print(f"Failed to fetch resource: {e}")

if __name__ == "__main__":
    asyncio.run(main())
```

## Sample Request and Response
Request
```
0000   42 01 55 be d0 43 b4 69 6e 66 6f                  B.U..C.info                                               .
```
Response
```
0000   62 45 55 be d0 43 c0 ff 7b 22 63 69 64 22 3a 22   bEU..C..{"cid":"
0010   XX XX XX XX XX XX XX XX 2d XX XX XX XX XX XX XX   XXXXXXXX-XXXX-XX
0020   XX XX 2d XX XX XX XX 2d XX XX XX XX XX XX XX XX   XX-XXXX-XXXXXXXX
0030   XX XX XX XX 22 2c 22 68 74 74 70 5f 70 6f 72 74   XXXX","http_port
0040   22 3a 38 30 2c 22 74 79 70 65 22 3a 22 72 6f 75   ":80,"type":"rou
0050   74 65 72 22 2c 22 6e 61 6d 65 22 3a 22 4b 65 65   ter","name":"Kee
0060   6e 65 74 69 63 20 4f 6d 6e 69 20 44 53 4c 22 2c   netic Omni DSL",
0070   22 76 65 72 73 69 6f 6e 22 3a 22 30 2e 33 2e 35   "version":"0.3.5
0080   22 7d                                             "}                                               .
```

## FOFA search 
```
protocol="coap""query/qlink/queryGwExt"
```