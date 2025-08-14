# BloomRPC

BloomRPC is a GUI client for testing and interacting with gRPC services. It allows developers to quickly test RPC
endpoints using `.proto` files without writing additional client code.

## Installation

```bash
brew install --cask bloomrpc
```

## Running BloomRPC

After installation, open BloomRPC from the Applications folder or via Spotlight search.

## Loading and Using `.proto` Files

1. Click on `File` → `Import Proto` or the `+` button in the left panel to add a `.proto` file.
2. BloomRPC will parse the services and display all available RPC methods.
3. Select a method to test, fill in the request fields, and click `Invoke` to send the RPC request.
4. The response will appear in the right panel.

## Connecting to a gRPC Server

1. Enter the server address (e.g., `localhost:50051`) in the target field.
2. If the server uses TLS, configure the certificate in the `Settings` panel.
3. Click `Connect` to establish the connection.

## Saving Requests

BloomRPC allows you to save your requests for reuse:

- Click `Save` after creating a request.
- Saved requests can be organized into collections for easier access.

## Updating BloomRPC

To update BloomRPC:

```bash
brew upgrade --cask bloomrpc
```

## Uninstalling BloomRPC

To uninstall via Homebrew:

```bash
brew uninstall --cask bloomrpc
```

## Conclusion

BloomRPC simplifies testing gRPC services, allowing you to load `.proto` files, send requests, and view responses with a
simple GUI. It is especially useful for developers working with multiple gRPC endpoints on macOS.

For more details, refer to the official GitHub
repository: [https://github.com/bloomrpc/bloomrpc](https://github.com/bloomrpc/bloomrpc).

## ⚠️ Note on BloomRPC

This project was archived in Jan 2023 and is no longer actively maintained. Its usage is no longer recommended.

### Why was it archived?

When BloomRPC was first released in Dec 2018, there were very few GUI gRPC tools available, hence the project tagline: "
The missing GUI client for gRPC services". It served its purpose for a few years. Unfortunately, development stalled and
issues accumulated, leaving users frustrated. The maintainers decided that BloomRPC no longer offered a good experience
and archived the project.

### Recommended Alternatives

For actively maintained and feature-rich gRPC GUI clients, check out the list of current tools
at [Awesome gRPC Tools](https://github.com/grpc-ecosystem/awesome-grpc#tools).
