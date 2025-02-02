## Web Socket Handler for Shelf

`shelf_web_socket` is a [Shelf][] handler for establishing [WebSocket][]
connections. It exposes a single function, [webSocketHandler][], which calls an
`onConnection` callback with a [WebSocketChannel][] object for every
connection that's established.

[Shelf]: https://pub.dev/packages/shelf

[WebSocket]: https://tools.ietf.org/html/rfc6455

[webSocketHandler]: https://pub.dev/documentation/shelf_web_socket/latest/shelf_web_socket/webSocketHandler.html

[WebSocketChannel]: https://pub.dev/documentation/web_socket_channel/latest/web_socket_channel/WebSocketChannel-class.html

```dart
import 'package:shelf/shelf_io.dart' as shelf_io;
import 'package:shelf_web_socket/shelf_web_socket.dart';
import 'package:web_socket_channel/web_socket_channel.dart';

void main() {
  var handler = webSocketHandler((webSocket) {
    webSocket.stream.listen((message) {
      webSocket.sink.add("echo $message");
    });
  });

  shelf_io.serve(handler, 'localhost', 8080).then((server) {
    print('Serving at ws://${server.address.host}:${server.port}');
  });
}
```

### Example with Shelf Router
```dart

import 'package:shelf/shelf.dart';
import 'package:shelf/shelf_io.dart' as io;
import 'package:shelf_router/shelf_router.dart';
import 'package:shelf_web_socket/shelf_web_socket.dart';

void main(List<String> arguments) {
  final app = Router();
  
  app.get(
      '/ws',
      (Request request, [__]) => webSocketHandler((webSocket) {
            serviceRegistry.setHandler(() {
              webSocket.sink.add("reload");
            });
          })(request));
          
  io
      .serve(app, 'localhost', 3000)
      .then((server) => print('Server listen on ${server.address.host} ${server.port}'));  
}
```
