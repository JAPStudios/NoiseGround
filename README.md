# NoiseGround

NoiseGround is a free, public audio-sharing service for songs, music and podcasts. Listening is open without subscriptions, advertising or paid access.

## Current deployment

- Public site: https://noiseground-public.onrender.com
- Source repository: https://github.com/JAPStudios/NoiseGround
- Static hosting: Render
- API: https://mtxbpf8g3l.execute-api.us-east-1.amazonaws.com
- AWS region: us-east-1

## Implemented

- Public catalogue with text search and category filters
- Browser audio playback using short-lived signed S3 URLs
- Email-verified Cognito member registration and sign-in
- Creator profiles with display name, biography and website
- Audio publishing with multipart S3 uploads (up to 2 GB per recording)
- Podcast series, season and episode metadata
- Required rights-and-permissions attestation before upload
- Track ownership checks for deletion
- Likes
- Public and private playlists with track management
- Creator following and follower counts
- Recording reports and administrator review actions
- Administrator announcements, featured audio and server-enforced upload pause
- Private, encrypted audio storage with public bucket access blocked
- Incomplete multipart-upload cleanup and noncurrent-version expiration

## Architecture

- Amazon S3 stores original audio privately.
- Amazon DynamoDB stores recording metadata, creator profiles, likes and reports.
- AWS Lambda implements the application API.
- API Gateway HTTP API exposes public catalogue/playback routes and JWT-protected member routes.
- Amazon Cognito manages email-verified member authentication.
- Render serves the static web interface from this repository.

## API routes

Public:
- `GET /health`
- `GET /site-settings`
- `GET /featured`
- `GET /tracks`
- `GET /tracks/{trackId}`
- `GET /tracks/{trackId}/playback`
- `GET /creators/{userId}`
- `GET /creators/{userId}/followers`
- `GET /tracks/{trackId}/likes`

Member-authenticated:
- `GET /playlists/{playlistId}`
- `GET /playlists/me`
- `POST /playlists`
- `POST /playlists/{playlistId}/tracks`
- `DELETE /playlists/{playlistId}/tracks/{trackId}`
- `DELETE /playlists/{playlistId}`
- `POST /creators/{userId}/follow`
- `DELETE /creators/{userId}/follow`
- `GET /profile`
- `PUT /profile`
- `POST /uploads/init`
- `POST /uploads/part-url`
- `POST /uploads/complete`
- `DELETE /tracks/{trackId}`
- `POST /tracks/{trackId}/likes`
- `DELETE /tracks/{trackId}/likes`
- `POST /tracks/{trackId}/reports`

Administrator-authenticated:
- `GET /admin/settings`
- `PUT /admin/settings`
- `GET /admin/members`
- `PUT /admin/members/{username}/role`
- `GET /admin/reports`
- `POST /admin/reports/{reportId}/resolve`

## Operational notes

- Playback links expire after 15 minutes.
- Audio uploads are stored privately; the S3 bucket blocks public access.
- Incomplete multipart uploads are aborted after seven days.
- Noncurrent audio object versions expire after 30 days.
- Lambda logs are retained for 14 days.
- The initial catalogue is empty until members publish recordings.

## Remaining production work

- Assign the initial administrator account after its member registration is completed.
- Independently verify a real member's email-confirmation and sign-in flow in a browser.
- Complete browser-level upload, playback, deletion and report tests with a real member account.
- Add media transcoding and adaptive streaming; current playback serves the uploaded source file directly.
- Add playlists, follows, richer creator discovery and full-text indexing.
- Add formal service terms, privacy and copyright-agent procedures before broad public operation.
- Connect a custom domain if one is designated for the service.

The service is deployed, but these remaining items mean the production launch is not yet complete.
