## v0.3.0 (2024-11-20)

- Added low-level `createRequest(req, options)` and `sendResponse(res, response)` exports to assist with building custom fetch servers on Node.js

## v0.2.0 (2024-11-14)

- Small perf improvement from avoiding accessing `req.headers` and reading `req.rawHeaders` instead
- Added CommonJS build

## v0.1.0 (2024-09-05)

- Initial release
