# go-salvium

[![GoDoc](https://img.shields.io/static/v1?label=godoc&message=reference&color=blue)](https://pkg.go.dev/github.com/mxhess/go-salvium)


A multi-platform Go library for interacting with Salvium nodes
either on clearnet or not, supporting daemon and wallet RPC,
p2p commands and ZeroMQ.

## Quick start

### Library

To consume `go-salvium` as a library for your Go project:

```bash
go get -u -v github.com/mxhess/go-salvium
```

`go-salvium` exposes an high-level package: `rpc`.

The package `rpc`, is used to communicate with `salviumd` and `salvium-wallet-rpc` via its HTTP
endpoints. Note that not all endpoints/fields are exposed on a given port - if
it's being served in a restricted manner, you'll have access to less endpoints
than you see in the documentation
([daemon RPC](https://docs.salvium.io/THE%20PROTOCOL/Daemon%20RPC), )

`rpc` itself is subdivided in two other packages: `wallet` and `daemon`, exposing `salvium-wallet-rpc` and `salviumd` RPCs accordingly.

For instance, to get the the height of the main chain:

```go
package daemon_test

import (
	"context"
	"fmt"

	"github.com/mxhess/go-salvium/rpc"
	"github.com/mxhess/go-salvium/rpc/daemon"
)

func ExampleGetHeight() {
	ctx := context.Background()
	addr := "http://localhost:19081"

	// instantiate a generic RPC client
	client, err := rpc.NewClient(addr)
	if err != nil {
		panic(fmt.Errorf("new client for '%s': %w", addr, err))
	}

	// instantiate a daemon-specific client and call the `get_height`
	// remote procedure.
	height, err := daemon.NewClient(client).GetHeight(ctx)
	if err != nil {
		panic(fmt.Errorf("get height: %w", err))
	}

	fmt.Printf("height=%d hash=%s\n", height.Height, height.Hash)
}
```

And to get the height from `salvium-wallet-rpc`:
```go
package wallet_test

import (
	"context"
	"fmt"

	"github.com/mxhess/go-salvium/rpc"
	"github.com/mxhess/go-salvium/rpc/wallet"
)

func ExampleGetHeight() {
	ctx := context.Background()
	addr := "http://localhost:19086"

	// instantiate a generic RPC client
	client, err := rpc.NewClient(addr)
	if err != nil {
		panic(fmt.Errorf("new client for '%s': %w", addr, err))
	}

	// instantiate a wallet-specific client and call the `get_height`
	// remote procedure.
	height, err := wallet.NewClient(client).GetHeight(ctx)
	if err != nil {
		panic(fmt.Errorf("get height: %w", err))
	}

	fmt.Printf("height=%d\n", height.Height)
}
```

## License

See [LICENSE](./LICENSE).


## Thanks

Huge thanks to [Ciro Costa](https://github.com/cirocosta/go-monero) for writing the original implementation and [duggavo](https://github.com/duggavo/go-monero) for their improvements!
