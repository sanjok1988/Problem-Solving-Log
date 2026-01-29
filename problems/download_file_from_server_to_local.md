# Download large (2GB+) file from server to macOS in background (resumable)

## Date
2026-01-29

## Environment / Context
- OS / Platform: macOS (client), Linux server `portal-v2` (remote)
- Tools used: SSH, `rsync`, `nohup`, `tail`, `ps` (optional)
- File: `/home/backups/20260115-100001.zip` (2GB+)

## Problem Description
Need to download a large ZIP from `portal-v2` to Mac `~/Downloads` and keep it running in the background, ideally with resume if the connection drops.

## Root Cause
Using `scp` interactively can be fragile for large transfers (no resume). Also, running `scp` on the server to itself causes SSH auth errors and doesn’t “download” to the Mac.

## Solution
1) Open Terminal on the Mac and go to Downloads:
```bash
cd ~/Downloads
```

2) Use `rsync` (recommended: resumable) and detach it to background:
```bash
nohup rsync -avP root@portal-v2:/home/backups/20260115-100001.zip ./ \
  > ~/Downloads/portal-v2-download.log 2>&1 &
disown
```

3) Monitor progress/logs:
```bash
tail -f ~/Downloads/portal-v2-download.log
```

4) If disconnected, re-run the same `rsync` command to resume.

5) Verify file size after completion:
```bash
ls -lh ~/Downloads/20260115-100001.zip
```

### If SSH uses a custom port (example: 2222)
```bash
nohup rsync -avP -e "ssh -p 2222" root@portal-v2:/home/backups/20260115-100001.zip ./ \
  > ~/Downloads/portal-v2-download.log 2>&1 &
disown
```

### Fallback (background `scp`, not resumable)
```bash
cd ~/Downloads
nohup scp root@portal-v2:/home/backups/20260115-100001.zip ./ \
  > ~/Downloads/portal-v2-scp.log 2>&1 &
disown
```

## Notes / Lessons Learned
- Run the transfer from the destination machine (the Mac) when “downloading”.
- Prefer `rsync -P` over `scp` for large files because it can resume partial downloads.
- `nohup ... & disown` keeps the transfer running after you close the Terminal.
- If hostname `portal-v2` doesn’t resolve from the Mac, use the server IP instead.
