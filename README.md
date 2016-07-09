# pauls-electron-rpc

Features:

 - Supports RPC calls from the renderer or webview to the background process
 - Supports async, sync, and readable methods

Possible future additions:

 - Permissions
 - Methods which return event-emitters
 - Methods which return objects, with their own exported APIs
 - Methods which return writable and duplex streams
 - Monitors renderer/webview lifetime to automatically release memory, if needed (currently not needed)

## Example usage

In a shared `example-api-manifest.js`:

```js
module.exports = {
  // simple method-types
  readFile: 'async',
  readFileSync: 'sync',
  createReadStream: 'readable',
  createWriteStream: 'writable',
}
```

In the main electron process:

```js
var rpc = require('pauls-electron-rpc')
var manifest = require('./example-api-manifest')
var fs = require('fs')

// export over the 'example-api' channel
var api = rpc.exportAPI('example-api', manifest, {
  // the exported API behaves like normal calls:
  readFile: fs.readFile,
  readFileSync: fs.readFileSync,
  createReadStream: fs.createReadStream,
  createWriteStream: fs.createWriteStream
})

// log any errors
api.on('error', console.log)
```

In the renderer or webview process:

```js
var rpc = require('pauls-electron-rpc')
var manifest = require('./example-api-manifest')

// import over the 'example-api' channel
var api = rpc.importAPI('example-api', manifest)

// now use, as usual:
api.readFileSync('/etc/hosts') // => '...'
```

## API

### rpc.exportAPI(channelName, manifest, methods)

### rpc.importAPI(channelName, manifest)