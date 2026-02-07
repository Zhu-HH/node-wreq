# node-wreq

🚀 High-performance browser fingerprint bypass library using Rust for native TLS/HTTP2 impersonation.

> **Node.js wrapper for [wreq](https://github.com/0x676e67/wreq)** — A powerful Rust HTTP client with browser impersonation capabilities.

## ✨ Features

- ⚡ **Native Performance** — 50-100x faster than curl-impersonate (no process spawning)
- 🔒 **TLS Fingerprinting** — Perfect JA3/JA4 signatures matching real browsers
- 🌐 **HTTP/2 Fingerprinting** — Authentic SETTINGS frames, PRIORITY, and header ordering
- 🎭 **Multiple Browser Profiles** — Chrome, Firefox, Safari, Edge, Opera, OkHttp (78+ profiles)
- 🔌 **WebSocket Support**
- 📦 **Zero Dependencies** — Pure Rust with BoringSSL under the hood
- 💎 **TypeScript Support** — Full type definitions included
- 🛡️ **Protection Bypass** — Cloudflare, Akamai, and other anti-bot systems

## 🔧 How It Works

The library is a Node.js wrapper for **[wreq](https://github.com/0x676e67/wreq)** (Rust HTTP client) with **BoringSSL** (Google's TLS library — the same one used in Chrome) to create requests that are indistinguishable from real browsers at the network level.

### Why It Works

Traditional HTTP clients (axios, fetch, curl) have differences in:
- **TLS handshake signatures** — Different cipher suites and extensions
- **HTTP/2 frame ordering** — Different SETTINGS and PRIORITY patterns
- **Header ordering** — Different sequence and values

This library precisely replicates the network behavior of real browsers.

## 📦 Installation

```bash
# npm
npm install node-wreq

# yarn
yarn add node-wreq

# pnpm
pnpm add node-wreq
```

That's it! 🎉 Pre-built native modules are included for all major platforms:
- 🍎 macOS (Intel & Apple Silicon)
- 🐧 Linux (x64 & ARM64)
- 🪟 Windows (x64)

## 💻 Usage

### Basic Request

```typescript
import { request } from 'node-wreq';

const response = await request({
  url: 'https://example.com/api',
  browser: 'chrome_137',
});

console.log(response.status);  // 200
console.log(response.body);    // Response body
console.log(response.headers); // Response headers
console.log(response.cookies); // Cookies
```

### With Custom Headers

```typescript
import { request } from 'node-wreq';

const response = await request({
  url: 'https://api.example.com/data',
  browser: 'firefox_139',
  headers: {
    'Authorization': 'Bearer token123',
    'Custom-Header': 'value',
  },
});
```

### POST Request

```typescript
import { post } from 'node-wreq';

const response = await post(
  'https://api.example.com/submit',
  JSON.stringify({ foo: 'bar' }),
  {
    browser: 'chrome_137',
    headers: {
      'Content-Type': 'application/json',
    },
  }
);
```

### Convenience Methods

```typescript
import { get, post } from 'node-wreq';

// GET request
const data = await get('https://api.example.com/users');

// POST request
const result = await post(
  'https://api.example.com/users',
  JSON.stringify({ name: 'John' })
);
```

### With Proxy

```typescript
import { request } from 'node-wreq';

const response = await request({
  url: 'https://example.com',
  browser: 'chrome_137',
  // proxy: 'http://proxy.example.com:8080',
  // proxy: 'http://username:password@proxy.example.com:8080',
  // proxy: 'socks5://proxy.example.com:1080',
});
```

### WebSocket Connection

```typescript
import { websocket } from 'node-wreq';

const ws = await websocket({
  url: 'wss://echo.websocket.org',
  browser: 'chrome_137',
  onMessage: (data) => {
    console.log('Received:', data);
  },
  onClose: () => {
    console.log('Connection closed');
  },
  onError: (error) => {
    console.error('Error:', error);
  },
});

// Send text message
await ws.send('Hello!');

// Send binary message
await ws.send(Buffer.from([1, 2, 3]));

// Close connection
await ws.close();
```

## 📚 API Reference

### `request(options:` [`RequestOptions`](#requestoptions)`): Promise<`[`Response`](#response)`>`

Main function for making HTTP requests with browser impersonation.

**Options:**
<a name="requestoptions"></a>

```typescript
interface RequestOptions {
  url: string;                    // Required: URL to request
  browser?: BrowserProfile;       // Default: 'chrome_137'
  method?: HttpMethod;            // Default: 'GET'
  headers?: Record<string, string>;
  body?: string;
  proxy?: string;                 // HTTP/HTTPS/SOCKS5 proxy URL
  timeout?: number;               // Default: 30000ms
}
```

**Response:**
<a name="response"></a>

```typescript
interface Response {
  status: number;
  headers: Record<string, string>;
  body: string;
  cookies: Record<string, string>;
  url: string;  // Final URL after redirects
}
```

### `get(url: string, options?): Promise<`[`Response`](#response)`>`

### `post(url: string, body?: string, options?): Promise<`[`Response`](#response)`>`

### `websocket(options:` [`WebSocketOptions`](#websocketoptions)`): Promise<WebSocket>`


**Options:**
<a name="websocketoptions"></a>

```typescript
interface WebSocketOptions {
  url: string;                                  // Required: WebSocket URL (ws:// or wss://)
  browser?: BrowserProfile;                     // Default: 'chrome_137'
  headers?: Record<string, string>;
  proxy?: string;                               // HTTP/HTTPS/SOCKS5 proxy URL
  onMessage: (data: string | Buffer) => void;   // Required: Message callback
  onClose?: () => void;                         // Optional: Close callback
  onError?: (error: string) => void;            // Optional: Error callback
}
```

**WebSocket Methods:**

```typescript
class WebSocket {
  send(data: string | Buffer): Promise<void>;
  close(): Promise<void>;
}
```

### `getProfiles():` [`BrowserProfile[]`](#browser-profiles)

Get list of available browser profiles.

```typescript
import { getProfiles } from 'node-wreq';

const profiles = getProfiles();

console.log(profiles);
// ['chrome_100', 'chrome_101', ..., 'chrome_137', 'edge_101', ..., 'safari_18', ...]
```

## 🎭 Browser Profiles
<a name="browser-profiles"></a>

Available browser profiles (78+ profiles):

### Chrome
29 versions from Chrome 100 to Chrome 137:
- `chrome_100`, `chrome_101`, `chrome_104`, `chrome_105`, `chrome_106`, `chrome_107`, `chrome_108`, `chrome_109`, `chrome_110`
- `chrome_114`, `chrome_116`, `chrome_117`, `chrome_118`, `chrome_119`, `chrome_120`, `chrome_123`, `chrome_124`, `chrome_126`
- `chrome_127`, `chrome_128`, `chrome_129`, `chrome_130`, `chrome_131`, `chrome_132`, `chrome_133`, `chrome_134`, `chrome_135`, `chrome_136`, `chrome_137`

### Edge
5 versions: `edge_101`, `edge_122`, `edge_127`, `edge_131`, `edge_134`

### Safari
19 versions including iOS and iPad:
- Desktop: `safari_15_3`, `safari_15_5`, `safari_15_6_1`, `safari_16`, `safari_16_5`, `safari_17_0`, `safari_17_2_1`, `safari_17_4_1`, `safari_17_5`, `safari_18`, `safari_18_2`, `safari_18_3`, `safari_18_3_1`, `safari_18_5`
- iOS: `safari_ios_16_5`, `safari_ios_17_2`, `safari_ios_17_4_1`, `safari_ios_18_1_1`
- iPad: `safari_ipad_18`

### Firefox
10 versions including private and Android:
- `firefox_109`, `firefox_117`, `firefox_128`, `firefox_133`, `firefox_135`, `firefox_136`, `firefox_139`
- Private: `firefox_private_135`, `firefox_private_136`
- Android: `firefox_android_135`

### Opera
4 versions: `opera_116`, `opera_117`, `opera_118`, `opera_119`

### OkHttp (Android HTTP client)
8 versions: `okhttp_3_9`, `okhttp_3_11`, `okhttp_3_13`, `okhttp_3_14`, `okhttp_4_9`, `okhttp_4_10`, `okhttp_4_12`, `okhttp_5`

> Use `getProfiles()` to get the complete list programmatically.

## 📖 Documentation

- **[Architecture Guide](docs/ARCHITECTURE.md)** — Technical details about TLS/HTTP2 fingerprinting, how browser impersonation works
- **[Build Instructions](docs/BUILD.md)** — Developer guide for building from source
- **[Publishing Guide](docs/PUBLISHING.md)** — How to publish the package

## 🤝 Contributions are welcome!

Please read [Contributing Guide](CONTRIBUTING.md).

## 🙏 Acknowledgments

Built with:
- [wreq](https://github.com/0x676e67/wreq) — Rust HTTP client with browser impersonation
- [Neon](https://neon-bindings.com/) — Rust ↔ Node.js bindings


# Secondary development notes

## 环境搭建
安装rust https://zhuanlan.zhihu.com/p/655386777
安装成功后验证 rustc --version
更新/卸载 rustup update/rustup self uninstall 
不要使用普通的 CMD，在 Windows 开始菜单里找一个叫 "Developer Command Prompt for VS 2022" 的快捷方式。这个终端会自动加载所有 C++ 编译器的路径。在这个专门的终端里运行 npm run build 几乎 100% 能过。
安装cmake nasm llvm 

## 项目理解
rust/ 文件下就是项目的核心
rust/Cargo.toml 类似 python的pip那个要求文件 依赖库的版本信息等等
rust/src/lib.rs 核心入口文件类似于将rust的函数暴露给nodejs
rust/src/client.rs 封装wreq请求 请求头设置功能。

运行 npm run build  napi-rs 读取 rust/src/lib.rs 会生成一个.node 二进制模块
src/test 的ts文件就是测试demo文件

## 项目编译日志
https://gemini.google.com/app/494ec88320598743
```
E:\cnb\node-wreq>npm run build

> node-wreq@0.2.0 build
> npm run build:rust && npm run build:ts


> node-wreq@0.2.0 build:rust
> napi build --platform --release --cargo-cwd rust rust

   Compiling proc-macro2 v1.0.101
   Compiling quote v1.0.41
   Compiling unicode-ident v1.0.19
   Compiling getrandom v0.3.3
   Compiling cfg-if v1.0.3
   Compiling version_check v0.9.5
   Compiling smallvec v1.15.1
   Compiling windows-link v0.2.1
   Compiling windows_x86_64_msvc v0.52.6
   Compiling windows_x86_64_msvc v0.53.1
   Compiling shlex v1.3.0
   Compiling stable_deref_trait v1.2.1
   Compiling find-msvc-tools v0.1.9
   Compiling bytes v1.10.1
   Compiling parking_lot_core v0.9.12
   Compiling pin-project-lite v0.2.16
   Compiling scopeguard v1.2.0
   Compiling itoa v1.0.15
   Compiling glob v0.3.3
   Compiling futures-core v0.3.31
   Compiling windows-sys v0.61.2
   Compiling zerocopy v0.8.27
   Compiling typenum v1.19.0
   Compiling libc v0.2.177
   Compiling winapi v0.3.9
   Compiling lock_api v0.4.14
   Compiling regex-syntax v0.8.7
   Compiling memchr v2.7.6
   Compiling generic-array v0.14.7
   Compiling minimal-lexical v0.2.1
   Compiling libloading v0.8.9
   Compiling either v1.15.0
   Compiling clang-sys v1.8.1
   Compiling futures-sink v0.3.31
   Compiling windows-targets v0.52.6
   Compiling fnv v1.0.7
   Compiling bindgen v0.72.1
   Compiling writeable v0.6.2
   Compiling litemap v0.8.1
   Compiling windows-sys v0.59.0
   Compiling windows-targets v0.53.5
   Compiling http v1.3.1
   Compiling itertools v0.13.0
   Compiling jobserver v0.1.34
   Compiling windows-sys v0.60.2
   Compiling nom v7.1.3
   Compiling crc32fast v1.5.0
   Compiling icu_properties_data v2.1.2
   Compiling bitflags v2.9.4
   Compiling cc v1.2.55
   Compiling parking_lot v0.12.5
   Compiling pkg-config v0.3.32
   Compiling icu_normalizer_data v2.1.1
   Compiling regex-automata v0.4.12
   Compiling syn v2.0.106
   Compiling rustc-hash v2.1.1
   Compiling socket2 v0.6.2
   Compiling adler2 v2.0.1
   Compiling fs_extra v1.3.0
   Compiling simd-adler32 v0.3.7
   Compiling slab v0.4.11
   Compiling rand_core v0.9.3
   Compiling httparse v1.10.1
   Compiling block-buffer v0.10.4
   Compiling crypto-common v0.1.6
   Compiling mio v1.0.4
   Compiling miniz_oxide v0.8.9
   Compiling cmake v0.1.57
   Compiling thiserror v2.0.17
   Compiling pin-utils v0.1.0
   Compiling serde_core v1.0.228
   Compiling futures-task v0.3.31
   Compiling digest v0.10.7
   Compiling cexpr v0.6.0
   Compiling fslock v0.2.1
   Compiling ahash v0.8.12
   Compiling once_cell v1.21.3
   Compiling thiserror v1.0.69
   Compiling foreign-types-shared v0.3.1
   Compiling zstd-safe v7.2.4
   Compiling num-conv v0.1.0
   Compiling flate2 v1.1.4
   Compiling cpufeatures v0.2.17
   Compiling alloc-no-stdlib v2.0.4
   Compiling time-core v0.1.6
   Compiling powerfmt v0.2.0
   Compiling compression-core v0.4.29
   Compiling regex v1.12.1
   Compiling ppv-lite86 v0.2.21
   Compiling alloc-stdlib v0.2.2
   Compiling time-macros v0.2.24
   Compiling zstd-sys v2.0.16+zstd.1.5.7
   Compiling deranged v0.5.4
   Compiling sha1 v0.10.6
   Compiling compression-codecs v0.4.31
   Compiling http-body v1.0.1
   Compiling cookie v0.18.1
   Compiling equivalent v1.0.2
   Compiling utf8_iter v1.0.4
   Compiling rand_chacha v0.9.0
   Compiling serde v1.0.228
   Compiling tower-layer v0.3.3
   Compiling percent-encoding v2.3.2
   Compiling tower-service v0.3.3
   Compiling linkme-impl v0.3.35
   Compiling hashbrown v0.16.0
   Compiling serde_json v1.0.145
   Compiling utf-8 v0.7.6
   Compiling rand v0.9.2
   Compiling log v0.4.28
   Compiling data-encoding v2.9.0
   Compiling form_urlencoded v1.2.2
   Compiling http-body-util v0.1.3
   Compiling brotli-decompressor v5.0.0
   Compiling sync_wrapper v1.0.2
   Compiling rustls-pki-types v1.12.0
   Compiling atomic-waker v1.1.2
   Compiling hashbrown v0.13.2
   Compiling linkme v0.3.35
   Compiling ryu v1.0.20
   Compiling try-lock v0.2.5
   Compiling want v0.3.1
   Compiling webpki-root-certs v1.0.3
   Compiling indexmap v2.11.4
   Compiling futures-channel v0.3.31
   Compiling anyhow v1.0.100
   Compiling ipnet v2.11.0
   Compiling send_wrapper v0.6.0
   Compiling semver v1.0.27
   Compiling synstructure v0.13.2
   Compiling time v0.3.44
   Compiling schnellru v0.2.4
   Compiling brotli v8.0.2
   Compiling zerofrom-derive v0.1.6
   Compiling yoke-derive v0.8.1
   Compiling zerovec-derive v0.11.2
   Compiling displaydoc v0.2.5
   Compiling tokio-macros v2.6.0
   Compiling futures-macro v0.3.31
   Compiling thiserror-impl v2.0.17
   Compiling foreign-types-macros v0.2.3
   Compiling thiserror-impl v1.0.69
   Compiling openssl-macros v0.1.1
   Compiling serde_derive v1.0.228
   Compiling futures-util v0.3.31
   Compiling tokio v1.49.0
   Compiling typed-builder-macro v0.23.2
   Compiling neon-macros v1.1.1
   Compiling zerofrom v0.1.6
   Compiling foreign-types v0.5.0
   Compiling yoke v0.8.1
   Compiling zerovec v0.11.5
   Compiling zerotrie v0.2.3
   Compiling tungstenite v0.28.0
   Compiling neon v1.1.1
   Compiling typed-builder v0.23.2
   Compiling tinystr v0.8.2
   Compiling potential_utf v0.1.4
   Compiling icu_collections v2.1.1
   Compiling icu_locale_core v2.1.1
   Compiling zstd v0.13.3
   Compiling boring-sys2 v5.0.0-alpha.12
   Compiling icu_provider v2.1.1
   Compiling icu_normalizer v2.1.1
   Compiling icu_properties v2.1.2
   Compiling idna_adapter v1.2.1
   Compiling idna v1.1.0
   Compiling url v2.5.8
   Compiling tokio-util v0.7.16
   Compiling async-compression v0.4.32
   Compiling tokio-socks v0.5.2
   Compiling tower v0.5.3
   Compiling tokio-tungstenite v0.28.0
   Compiling http2 v0.5.11
   Compiling tower-http v0.6.8
   Compiling boring2 v5.0.0-alpha.12
   Compiling tokio-boring2 v5.0.0-alpha.12
   Compiling wreq v6.0.0-rc.27
   Compiling wreq-util v3.0.0-rc.9
   Compiling node-wreq v0.1.0 (E:\cnb\node-wreq\rust)
    Finished `release` profile [optimized] target(s) in 8m 22s

> node-wreq@0.2.0 build:ts
> npm run clean:dist && tsc


> node-wreq@0.2.0 clean:dist
> rimraf dist


E:\cnb\node-wreq>
```



## 二开思路
1. lib.rs 增加akm字符串解析
2. client.rs中解析字符串并构造
3. 需要验证rust wreq 指定了chrome_131之后修改部分tls http2 能否生效？


## 修BUG记录

### response.cookies 锁保存的cookies不完整

### response.headers 当遇到同名key时会丢失信息问题




