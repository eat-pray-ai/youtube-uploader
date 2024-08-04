# Upload video to YouTube

GitHub action to upload video to YouTube, powered by [yutu](https://github.com/eat-pray-ai/yutu).

If you are looking for a more general action to interact with YouTube, please refer to [youtube-action](https://github.com/eat-pray-ai/youtube-action).

## Example

```yaml
name: Upload video to YouTube
on:
  workflow_dispatch:

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - id: generate
        shell: bash
        run: |
          echo "generate video.mp4" // yutu.mp4
          echo "title=yutu" >> $GITHUB_OUTPUT
          echo "description=eat-pray-ai/yutu" >> $GITHUB_OUTPUT
          echo "tag=eat-pray-ai,yutu🐰" >> $GITHUB_OUTPUT
          echo "playlistId=PL_if_required" >> $GITHUB_OUTPUT
      - name: upload video
        uses: eat-pray-ai/youtube-uploader@main
        with:
          credential: ${{ secrets.YOUTUBE_CREDENTIAL }}
          token: ${{ secrets.YOUTUBE_TOKEN }}
          file: "./${{ steps.generate.outputs.title }}.mp4"
          title: ${{ steps.generate.outputs.title }}
          description: ${{ steps.generate.outputs.description }}
          tags: ${{ steps.generate.outputs.tag }}
          rest-flags: "-y ${{ steps.generate.outputs.playlistId }} -p public -g 22"
```
## Inputs

### `version`

**Optional**, The version of yutu to use, `v0.9.5` for example, leave empty to use the latest version.

### `credential`

**Required**, Please refer to [yutu prerequisites](https://github.com/eat-pray-ai/yutu?tab=readme-ov-file#prerequisites) to get a credential.

Note: The credential should be base64 encoded as input, for example:

```shell
base64 -w 0 client_secret.json
```

### `token`

**Required**, Please refer to [yutu prerequisites](https://github.com/eat-pray-ai/yutu?tab=readme-ov-file#prerequisites) to generate a token.

Note: The token should be base64 encoded as input, for example:

```shell
base64 -w 0 youtube.token.json
```

### `file`

**Required**, Path to the video file.

### `title`

**Optional**, Title of the video, use filename if not provided.

### `description`

**Optional**, Description of the video.

### `tags`

**Optional**, Comma separated list of tags for the video.

### `output`

**Optional**, Output format: `json`, `yaml` or `silent`.

### `rest-flags`

**Optional**, Flags passed to yutu, such as `playlistId`, for all available flags when uploading a video:

```shell
❯ yutu video insert --help
Upload a video to YouTube, with the specified title, description, tags, etc.

Usage:
  yutu video insert [flags]

Flags:
  -a, --autoLevels                             Should auto-levels be applied to the upload (default true)
  -g, --categoryId string                      Category of the video
  -c, --channelId string                       Channel ID of the video
  -d, --description string                     Description of the video
  -e, --embeddable                             Whether the video is embeddable (default true)
  -f, --file string                            Path to the video file
  -k, --forKids                                Whether the video is for kids
  -h, --help                                   help for insert
  -l, --language string                        Language of the video
  -L, --license string                         youtube(default) or creativeCommon (default "youtube")
  -n, --notifySubscribers                      Notify the channel subscribers about the new video (default true)
  -b, --onBehalfOfContentOwner string
  -B, --onBehalfOfContentOwnerChannel string
  -o, --output string                          json, yaml or silent
  -y, --playlistId string                      Playlist ID of the video
  -p, --privacy string                         Privacy status of the video: public, private, or unlisted
  -P, --publicStatsViewable                    Whether the extended video statistics can be viewed by everyone
  -U, --publishAt string                       Datetime when the video is scheduled to publish
  -s, --stabilize                              Should stabilize be applied to the upload (default true)
  -T, --tags stringArray                       Comma separated tags
  -u, --thumbnail string                       Path to the thumbnail
  -t, --title string                           Title of the video
```

## Outputs

### `response`

The response from yutu, in the format specified by [`output`](#output) flag.
