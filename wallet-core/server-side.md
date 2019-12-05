# Server-side

If your server application (most likely on Linux) language supports [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface) to C, it is possible to call wallet core.

## Golang

Here is a step by step example of [cgo](https://golang.org/cmd/cgo/), we may simplify this with a prebuilt binary docker image in the future.

1. Run `docker run -it trustwallet/wallet-core bash`
2. Run `cd wallet-core` and then `./bootstrap.sh`
2. Download and install [go1.13.3](https://dl.google.com/go/go1.13.3.linux-amd64.tar.gz), configure `GOROOT` and append `GOROOT/bin` to `PATH`.
3. Save the sample code below to file called `main.go` and run `go build -o main`

```go
package main

// #cgo CFLAGS: -I/wallet-core/include
// #cgo LDFLAGS: -L/wallet-core/build -L/wallet-core/build/trezor-crypto -lTrustWalletCore -lprotobuf -lTrezorCrypto -lc++ -lm
// #include <TrustWalletCore/TWHDWallet.h>
// #include <TrustWalletCore/TWString.h>
import "C"

import "fmt"

func main() {
	fmt.Println("==> calling wallet core from go")
	str := C.TWStringCreateWithUTF8Bytes(C.CString("confirm bleak useless tail chalk destroy horn step bulb genuine attract split"))
	defer C.TWStringDelete(str)
	valid := C.TWHDWalletIsValid(str)
	fmt.Println("<== mnemonic is valid: ", valid)
}
```

5. Run `./main` and you will see the output: 

```shell
==> calling wallet core from go
<== mnemonic is valid:  true
```