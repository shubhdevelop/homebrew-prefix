# GitHub Actions Workflow

This project includes a GitHub Actions workflow to automatically build macOS executables using a matrix build strategy.

## Workflow: `build.yaml`

Uses a build matrix to build for both macOS architectures.

## Supported Platforms

The workflow builds for:
- **macOS**: amd64 (Intel), arm64 (Apple Silicon/M1/M2/M3)

## Triggers

The workflows run on:
1. **Pull requests to main** - Validates builds work before merging
2. **Git tags** (v*) - Creates a GitHub Release with all binaries
3. **Manual trigger** - Can be run from GitHub Actions tab

## Usage

### For Regular Development

1. Create a pull request to `main` branch
2. GitHub Actions will automatically build all platforms
3. Review build status in the PR checks
4. Download artifacts from the Actions tab if needed
5. Once PR is merged, changes are in main

### For Releases

1. Create and push a version tag:
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. GitHub Actions will:
   - Build macOS binaries (Intel and Apple Silicon)
   - Generate SHA256 checksums
   - Create a GitHub Release
   - Attach all binaries and checksums to the release

### Manual Trigger

1. Go to the "Actions" tab in your repository
2. Select the workflow
3. Click "Run workflow"
4. Choose the branch and click "Run workflow"

## Build Artifacts

After each build, you can download artifacts from:
- Repository → Actions → Select workflow run → Artifacts section

For tagged releases, binaries are available in:
- Repository → Releases section

## Build Options

The builds use these optimizations:
- `-trimpath`: Remove file system paths for reproducible builds
- `-ldflags="-s -w"`: Strip debug info to reduce binary size
- `CGO_ENABLED=0`: Static linking for better portability

## File Naming Convention

Binaries are named as:
```
prefix-macos-{arch}
```

Examples:
- `prefix-macos-amd64` (Intel Macs)
- `prefix-macos-arm64` (Apple Silicon: M1, M2, M3)

## Requirements

No setup needed! The workflow:
- Set up Go automatically
- Download dependencies
- Build for both macOS architectures
- Upload artifacts

## Permissions

The workflow requires `contents: write` permission to create releases. This is already configured in the workflow files.

## Customization

### Adding More Platforms

To add Linux or Windows builds, add to the matrix in `build.yaml`:
```yaml
- arch: amd64
  goos: linux
  goarch: amd64
  output: prefix-linux-amd64
```

### Changing Go Version

Update the `go-version` in the "Set up Go" step:
```yaml
- name: Set up Go
  uses: actions/setup-go@v5
  with:
    go-version: '1.22'  # Change this
```

## Troubleshooting

### Build Fails
- Check the Actions tab for error logs
- Ensure `go.mod` is committed
- Verify all dependencies are available

### Release Not Created
- Ensure you pushed a tag starting with 'v'
- Check workflow has `contents: write` permission
- Verify GITHUB_TOKEN has necessary permissions

### Artifacts Not Uploading
- Check artifact names are unique
- Ensure file paths are correct
- Verify retention days are set (default: 5)
