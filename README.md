# multipart-parser

`multipart-parser` is a fast, efficient parser for multipart streams. It can be used in any JavaScript environment (not just node) for a variety of use cases including:

- Handling file uploads (`multipart/form-data` requests)
- Parsing `multipart/mixed` messages (email attachments, API responses, etc.)
- Parsing email messages with both plain text and HTML versions (`multipart/alternative`)

## Features

This package is a streaming multipart parser for JavaScript's fetch API, making it easy to parse incoming `Request` bodies that are generated by `<form enctype="multipart/form-data">` in the browser.

## Installation

```sh
$ npm install @mjackson/multipart-parser
```

## Usage

```typescript
import { MultipartParseError, parseMultipartFormData } from '@mjackson/multipart-parser';

async function handleMultipartRequest(request: Request): void {
  try {
    // The parser `yield`s each part as a MultipartPart as it becomes available.
    for await (let part of parseMultipartFormData(request)) {
      console.log(part.name);
      console.log(part.filename);
      console.log(part.mediaType);

      if (/^text\//.test(part.mediaType)) {
        console.log(await part.text());
      } else {
        // TODO: part.body is a ReadableStream<Uint8Array>, stream it to a file
      }
    }
  } catch (error) {
    if (error instanceof MultipartParseError) {
      console.error('Failed to parse multipart/form-data:', error.message);
    } else {
      console.error('An unexpected error occurred:', error);
    }
  }
}
```

## Benchmark

The results of running the benchmarks on my laptop:

```
Platform: Darwin (23.5.0)
CPU: Apple M2 Pro
Node.js v20.15.1
Date: 7/28/2024, 8:16:46 AM
┌────────────────────────┬──────────────────┬──────────────────┬──────────────────┬───────────────────┐
│ (index)                │ 1 small file     │ 1 large file     │ 100 small files  │ 5 large files     │
├────────────────────────┼──────────────────┼──────────────────┼──────────────────┼───────────────────┤
│ fetch-multipart-parser │ '0.02 ms ± 0.13' │ '1.81 ms ± 0.10' │ '0.37 ms ± 0.14' │ '19.59 ms ± 1.25' │
│ busboy                 │ '0.03 ms ± 0.09' │ '3.95 ms ± 0.19' │ '0.22 ms ± 0.02' │ '46.38 ms ± 2.98' │
│ @fastify/busboy        │ '0.03 ms ± 0.08' │ '1.94 ms ± 0.21' │ '0.38 ms ± 0.04' │ '26.46 ms ± 2.31' │
└────────────────────────┴──────────────────┴──────────────────┴──────────────────┴───────────────────┘
```
