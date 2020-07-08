# Files API <!-- omit in toc -->
> The files API enables users to use the File System abstraction of IPFS. There are two Files API, one at the top level, the original `add`, `cat`, `get` and `ls`, and another behind the [`files`, also known as MFS](https://docs.ipfs.io/guides/concepts/mfs/)
- [The Regular API](#the-regular-api)
  - [`ipfs.add(data, [options])`](#ipfsadddata-options)
    - [Parameters](#parameters)
    - [Options](#options)
    - [Returns](#returns)
    - [Notes](#notes)
      - [Chunking options](#chunking-options)
      - [Hash algorithms](#hash-algorithms)
      - [Importing files from the file system](#importing-files-from-the-file-system)
      - [Importing a file from a URL](#importing-a-file-from-a-url)
  - [`ipfs.cat(ipfsPath, [options])`](#ipfscatipfspath-options)
    - [Parameters](#parameters-1)
    - [Options](#options-1)
    - [Returns](#returns-1)
    - [Example](#example)
  - [`ipfs.get(ipfsPath, [options])`](#ipfsgetipfspath-options)
    - [Parameters](#parameters-2)
    - [Options](#options-2)
    - [Returns](#returns-2)
  - [`ipfs.ls(ipfsPath)`](#ipfslsipfspath)
    - [Parameters](#parameters-3)
    - [Options](#options-3)
    - [Returns](#returns-3)
    - [Example](#example-1)
- [The Mutable Files API](#the-mutable-files-api)
  - [`ipfs.files.chmod(path, mode, [options])`](#ipfsfileschmodpath-mode-options)
    - [Parameters](#parameters-4)
    - [Options](#options-4)
    - [Returns](#returns-4)
    - [Example](#example-2)
  - [`ipfs.files.cp(...from, to, [options])`](#ipfsfilescpfrom-to-options)
    - [Parameters](#parameters-5)
    - [Options](#options-5)
    - [Returns](#returns-5)
    - [Example](#example-3)
    - [Notes](#notes-1)
  - [`ipfs.files.mkdir(path, [options])`](#ipfsfilesmkdirpath-options)
    - [Parameters](#parameters-6)
    - [Options](#options-6)
    - [Returns](#returns-6)
    - [Example](#example-4)
  - [`ipfs.files.stat(path, [options])`](#ipfsfilesstatpath-options)
    - [Parameters](#parameters-7)
    - [Options](#options-7)
    - [Returns](#returns-7)
    - [Example](#example-5)
  - [`ipfs.files.touch(path, [options])`](#ipfsfilestouchpath-options)
    - [Parameters](#parameters-8)
    - [Options](#options-8)
    - [Returns](#returns-8)
    - [Example](#example-6)
  - [`ipfs.files.rm(...paths, [options])`](#ipfsfilesrmpaths-options)
    - [Parameters](#parameters-9)
    - [Options](#options-9)
    - [Example](#example-7)
  - [`ipfs.files.read(path, [options])`](#ipfsfilesreadpath-options)
    - [Parameters](#parameters-10)
    - [Options](#options-10)
    - [Returns](#returns-9)
  - [`ipfs.files.write(path, content, [options])`](#ipfsfileswritepath-content-options)
    - [Parameters](#parameters-11)
    - [Options](#options-11)
    - [Returns](#returns-10)
  - [`ipfs.files.mv(...from, to, [options])`](#ipfsfilesmvfrom-to-options)
    - [Parameters](#parameters-12)
    - [Options](#options-12)
    - [Returns](#returns-11)
    - [Example](#example-8)
  - [`ipfs.files.flush(path, [options])`](#ipfsfilesflushpath-options)
    - [Parameters](#parameters-13)
    - [Options](#options-13)
    - [Returns](#returns-12)
  - [`ipfs.files.ls(path, [options])`](#ipfsfileslspath-options)
    - [Parameters](#parameters-14)
    - [Options](#options-14)
    - [Returns](#returns-13)
    - [Example](#example-9)

## The Regular API
The regular, top-level API for add, cat, get and ls Files on IPFS

### `ipfs.add(data, [options])`
#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| data | Object | Data to import (see below) |
`data` may be:
#### Options
An optional object which may have the following keys:

| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| chunker | `String` | `'size-262144` | chunking algorithm used to build ipfs DAGs |
| cidVersion | `Number` | `0` | the CID version to use when storing the data |
| enableShardingExperiment | `boolean` | `false` |  allows to create directories with an unlimited number of entries currently size of unixfs directories is limited by the maximum block size. Note that this is an experimental feature |
| hashAlg | `String` | `'sha2-256'` | multihash hashing algorithm to use |
| onlyHash | `boolean` | `false` | If true, will not add blocks to the blockstore |
| pin | `boolean` | `true` | pin this object when adding |
| progress | function | `undefined` | a function that will be called with the byte length of chunks as a file is added to ipfs |
| rawLeaves | `boolean` | `false` | if true, DAG leaves will contain raw file data and not be wrapped in a protobuf |
| shardSplitThreshold | `Number` | `1000` | Directories with more than this number of files will be created as HAMT-sharded directories |
| trickle | `boolean` | `false` | if true will use the [trickle DAG](https://godoc.org/github.com/ipsn/go-ipfs/gxlibs/github.com/ipfs/go-unixfs/importer/trickle) format for DAG generation |
| wrapWithDirectory | `boolean` | `false` | Adds a wrapping node around the content |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |

#### Returns
#### Example
#### Notes

##### Chunking options

The `chunker` option can be one of the following formats:
  - size-{size}
  - rabin
  - rabin-{avg}
  - rabin-{min}-{avg}-{max}

`size-*` will result in fixed-size chunks, `rabin(-*)` will use [rabin fingerprinting](https://en.wikipedia.org/wiki/Rabin_fingerprint) to potentially generate variable size chunks.

##### Hash algorithms

See the [multihash](https://github.com/multiformats/js-multihash/blob/master/src/constants.js#L5-L343) module for the list of all possible values.

##### Importing files from the file system
//options specific to globSource
const globSourceOptions = {
  recursive: true
};

//example options to pass to IPFS
const addOptions = {
  pin: true,
  wrapWithDirectory: true,
  timeout: 10000
};

for await (const file of ipfs.add(globSource('./docs', globSourceOptions), addOptions)) {
##### Importing a file from a URL
### `ipfs.cat(ipfsPath, [options])`
#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| ipfsPath | String or [CID][] | An [IPFS path][] or CID to export |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| offset | `Number` | `undefined` | An offset to start reading the file from |
| length | `Number` | `undefined` | An optional max length to read from the file |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |

#### Returns
#### Example
### `ipfs.get(ipfsPath, [options])`
#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| ipfsPath | String or [CID][] | An [IPFS path][] or CID to export |

#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example

### `ipfs.ls(ipfsPath)`
#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| ipfsPath | String or [CID][] | An [IPFS path][] or CID to list |

#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
## The Mutable Files API
### `ipfs.files.chmod(path, mode, [options])`
#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| path | String or [CID][] | An [MFS Path][], [IPFS path][] or CID to modify |
| mode | String or Number | An integer (e.g. `0o755` or `parseInt('0755', 8)`) or a string modification of the existing mode, e.g. `'a+x'`, `'g-w'`, etc |
#### Options
An optional object which may have the following keys:

| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| recursive | `boolean` | `false` | If true `mode` will be applied to the entire tree under `path` |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |

#### Returns
#### Example
### `ipfs.files.cp(...from, to, [options])`
> Copy files from one location to another
#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| from | One or more Strings or [CID][]s | An [MFS path][], [IPFS path][] or CID |
| to | `String` | An [MFS path][] |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| parents | `boolean` | `false` | If true, create intermediate directories |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
#### Notes
If `from` has multiple values then `to` must be a directory.
If `from` has a single value and `to` exists and is a directory, `from` will be copied into `to`.
If `from` has a single value and `to` exists and is a file, `from` must be a file and the contents of `to` will be replaced with the contents of `from` otherwise an error will be returned.
If `from` is an IPFS path, and an MFS path exists with the same name, the IPFS path will be chosen.

If `from` is an IPFS path and the content does not exist in your node's repo, only the root node of the source file with be retrieved from the network and linked to from the destination. The remainder of the file will be retrieved on demand.
### `ipfs.files.mkdir(path, [options])`

> Make a directory in your MFS

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` | The [MFS path][] to create a directory at |

#### Options

An optional object which may have the following keys:

| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| parents | `boolean` | `false` | If true, create intermediate directories |
| mode | `Number` | `undefined` | An integer that represents the file mode |
| mtime | `Object` | `undefined` | A Date object, an object with `{ secs, nsecs }` properties where `secs` is the number of seconds since (positive) or before (negative) the Unix Epoch began and `nsecs` is the number of nanoseconds since the last full second, or the output of [`process.hrtime()`](https://nodejs.org/api/process.html#process_process_hrtime_time) |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |

#### Returns
#### Example
### `ipfs.files.stat(path, [options])`

> Get file or directory statistics

#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` | The [MFS path][] return statistics from |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| hash | `boolean` | `false` | If true, return only the CID |
| size | `boolean` | `false` | If true, return only the size |
| withLocal | `boolean` | `false` | If true, compute the amount of the DAG that is local and if possible the total size |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
### `ipfs.files.touch(path, [options])`
#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` | The [MFS path][] to update the mtime for |

#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| mtime | `Object` | Now | Either a ` Date` object, an object with `{ sec, nsecs }` properties or the output of `process.hrtime()` |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
### `ipfs.files.rm(...paths, [options])`
#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` or `Array<String>` | One or more [MFS path][]s to remove |
#### Options
An optional object which may have the following keys:

| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| recursive | `boolean` | `false` | If true all paths under the specifed path(s) will be removed |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |

#### Returns
#### Example
### `ipfs.files.read(path, [options])`
#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` or [CID][] | An [MFS path][], [IPFS Path][] or [CID][] to read |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| offset | `Number` | `undefined` | An offset to start reading the file from |
| length | `Number` | `undefined` | An optional max length to read from the file |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |

#### Returns
#### Example
### `ipfs.files.write(path, content, [options])`

> Write to an MFS path

#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` | The [MFS path] where you will write to |
| content | `String`, `Buffer`, `AsyncIterable<Buffer>` or [`Blob`][blob] | The content to write to the path |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| offset | `Number` | `undefined` | An offset to start writing to file at |
| length | `Number` | `undefined` | Optionally limit how many bytes are read from the stream |
| create | `boolean` | `false` | Create the MFS path if it does not exist |
| parents | `boolean` | `false` | Create intermediate MFS paths if they do not exist |
| truncate | `boolean` | `false` | Truncate the file at the MFS path if it would have been larger than the passed `content` |
| rawLeaves | `boolean` | `false ` | If true, DAG leaves will contain raw file data and not be wrapped in a protobuf |
| mode | `Number` | `undefined` | An integer that represents the file mode |
| mtime | `Object` | `undefined` | A Date object, an object with `{ secs, nsecs }` properties where `secs` is the number of seconds since (positive) or before (negative) the Unix Epoch began and `nsecs` is the number of nanoseconds since the last full second, or the output of [`process.hrtime()`](https://nodejs.org/api/process.html#process_process_hrtime_time) |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
### `ipfs.files.mv(...from, to, [options])`
> Move files from one location to another#### Parameters
#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| from | `String` or `Array<String>` | One or more [MFS path][]s to move |
| to | `String` | The location to move files to |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| parents | `boolean` | `false` | Create intermediate MFS paths if they do not exist |
| flush | `boolean` | `true` | If true the changes will be immediately flushed to disk |
| hashAlg | `String` | `'sha2-256'` | The hash algorithm to use for any updated entries |
| cidVersion | `Number` | `0` | The CID version to use for any updated entries |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
#### Notes

If `from` has multiple values then `to` must be a directory.

If `from` has a single value and `to` exists and is a directory, `from` will be moved into `to`.

If `from` has a single value and `to` exists and is a file, `from` must be a file and the contents of `to` will be replaced with the contents of `from` otherwise an error will be returned.

If `from` is an IPFS path, and an MFS path exists with the same name, the IPFS path will be chosen.

If `from` is an IPFS path and the content does not exist in your node's repo, only the root node of the source file with be retrieved from the network and linked to from the destination. The remainder of the file will be retrieved on demand.

All values of `from` will be removed after the operation is complete unless they are an IPFS path.

### `ipfs.files.flush(path, [options])`
#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` | The [MFS path][] to flush |

#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
### `ipfs.files.ls(path, [options])`

> List directories in the local mutable namespace

#### Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| path | `String` | The [MFS path][] to list |
#### Options
An optional object which may have the following keys:
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| timeout | `Number` | `undefined` | A timeout in ms |
| signal | [AbortSignal][] | `undefined` |  Can be used to cancel any long running requests started as a result of this call |
#### Returns
#### Example
[IPFS Path]: https://www.npmjs.com/package/is-ipfs#isipfspathpath
[MFS Path]: https://docs.ipfs.io/guides/concepts/mfs/
[AbortSignal]: https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal