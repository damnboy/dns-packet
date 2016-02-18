# dns-packet

An [abstract-encoding](https://github.com/mafintosh/abstract-encoding) compliant module for encoding / decoding DNS packets.
Lifted out of [multicast-dns](https://github.com/mafintosh/multicast-dns) as a separate module.

```
npm install dns-packet
```

## Usage

``` js
var packet = require('dns-packet')
var dgram = require('dgram')

var socket = dgram.createSocket('udp4')

var buf = packet.encode({
  type: 'query',
  id: 1,
  questions: [{
    type: 'A',
    name: 'google.com'
  }]
})

socket.on('message', function (message) {
  console.log(packet.decode(message)) // prints out a response from google dns
})

socket.send(buf, 0, buf.length, 53, '8.8.8.8')
```

## API

#### `var buf = packets.encode(packet, [buf], [offset])`

Encodes a DNS packet into a buffer.

#### `var packet = packets.decode(buf, [offset])`

Decode a DNS packet from a buffer

#### `var len = packets.encodingLength(packet)`

Returns how many bytes are needed to encode the DNS packet

## Packets

Packets look like this

``` js
{
  type: 'query|response',
  id: optionalIdNumber,
  questions: [...],
  answers: [...],
  additionals: [...],
  authorities: [...]
}
```

A question looks like this

``` js
{
  type: 'A', // or SRV, AAAA, etc
  name: 'google.com' // which record are you looking for
}
```

And an answers, additional, or authority looks like this

``` js
{
  type: 'A', // or SRV, AAAA, etc
  name: 'google.com', // which name is this record for
  ttl: optionalTimeToLiveInSeconds,
  (record specific data, see below)
}
```

Currently the different available records are

#### `A`

``` js
{
  data: 'IPv4 address' // fx 127.0.0.1
}
```

#### `AAAA`

``` js
{
  data: 'IPv6 address' // fx fe80::1
}
```

#### `TXT`

``` js
{
  data: Buffer('some text')
}
```

#### `NULL`

``` js
{
  data: Buffer('any binary data')
}
```

#### `SRV`

``` js
{
  data: {
    port: servicePort,
    target: serviceHostName,
    priority: optionalServicePriority,
    weight: optionalServiceWeight
  }
}
```

#### `HINFO`

``` js
{
  data: {
    cpu: 'cpu info',
    os: 'os info'
  }
}
```

#### `PTR`

``` js
{
  data: 'points.to.another.record'
}
```

#### `CNAME`

``` js
{
  data: 'cname.to.another.record'
}
```

#### `DNAME`

``` js
{
  data: 'dname.to.another.record'
}
```

If you need another one, open an issue and we'll try to add it.

## License

MIT
