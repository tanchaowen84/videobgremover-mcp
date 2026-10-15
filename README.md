# VideoBGRemover MCP

Connection configuration and documentation for the hosted [VideoBGRemover MCP server](https://videobgremover.net/mcp).

This repository contains public client configuration, plugin manifests, and setup documentation. It does not contain the hosted video-processing backend. The MIT license covers the files in this repository; access to the hosted service is governed by [VideoBGRemover terms](https://videobgremover.net/terms).

## Connect

| Setting | Value |
| --- | --- |
| Endpoint | `https://videobgremover.net/api/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth; sign in with your VideoBGRemover account |
| Official MCP Registry name | `net.videobgremover/video-background-remover` |
| Setup documentation | [videobgremover.net/mcp](https://videobgremover.net/mcp) |

For an MCP client that supports remote servers, add the endpoint above and complete the browser OAuth flow. The connection uses your existing account and credits. No API key or secret is included in these configuration files.

For Claude Code, add the server with:

```sh
claude mcp add --transport http videobgremover https://videobgremover.net/api/mcp
```

Then open `/mcp` in Claude Code to complete authentication. The hosted connection is also suitable for other clients that implement Streamable HTTP and the server's OAuth flow; see the current [setup page](https://videobgremover.net/mcp) for connection guidance. This repository does not claim that every MCP client implements the same configuration format or authentication features.

The root `.mcp.json` provides the remote server entry for clients and plugin systems that use that format. `.claude-plugin/` contains a Claude Code plugin manifest and a marketplace manifest; `.cursor-plugin/` contains a Cursor plugin manifest. These are connection packages, not a local background-removal engine. The root `server.json` mirrors the published official MCP Registry metadata. Importing a configuration does not sign in, upload a video, or start a paid job by itself.

## What an assistant can do

The server exposes 11 tools for account information, video import and upload, job control, and downloading completed output:

| Tool | Purpose |
| --- | --- |
| `get_account` | Inspect account information and credits |
| `import_video` | Import a video from a URL |
| `create_upload` | Start an upload |
| `get_upload_part` | Obtain an upload part |
| `complete_upload` | Finish an upload |
| `get_upload` | Inspect an upload |
| `delete_upload` | Delete an upload |
| `remove_video_background` | Create a background-removal job |
| `get_job` | Inspect a job and its progress |
| `cancel_job` | Cancel a job |
| `get_download` | Retrieve the completed output download |

Use the tool schemas returned by the server for the current arguments and validation rules. The descriptions above are an overview, not a replacement for the live tool contract. A client must authenticate before accessing the user's protected resources.

## Typical workflow

1. Connect the remote endpoint and sign in with OAuth.
2. Use `get_account` to check the account and available credits.
3. Import a video URL or upload a clip through the upload tools.
4. Confirm the video duration and expected credit use with the user. Set the job's `max_credits` before calling `remove_video_background`.
5. Monitor the returned job with `get_job`. Use `cancel_job` when cancellation is needed.
6. Request the finished output with `get_download`.

An example request to the assistant is: “Remove the background from this short clip. Tell me its duration and expected credits first, and ask before processing.” The assistant should use the live tool responses to decide what is possible. Merely reading this example does not invoke the service.

## Output and credits

The output is a transparent VP9 WebM video retaining source audio. Supported video inputs include MP4, MOV, and WebM up to 1080p and 30 fps, subject to the current service limits.

Connecting the MCP server does not purchase credits. Background removal uses the existing VideoBGRemover account balance and costs **1 credit per processed video second**. A connection budget and per-job `max_credits` provide spending controls. Do not describe the processing service as free or the hosted backend as open source. Review the current product pricing before starting a job.

For an integration review that does not need paid processing, keep the connection budget at zero. Account and connection checks do not demonstrate that a paid processing job has completed. This repository's configuration validation likewise does not constitute an end-to-end processing test.

## Privacy and support

Video files and job operations are handled by the hosted VideoBGRemover service under its [privacy policy](https://videobgremover.net/privacy). Follow the permissions and limits shown during OAuth setup. Keep credentials and user videos out of public issues, pull requests, and configuration files.

- Product and current setup: [videobgremover.net/mcp](https://videobgremover.net/mcp)
- Support: [support@videobgremover.net](mailto:support@videobgremover.net)
- Repository license: [MIT](LICENSE), for this connection package only
