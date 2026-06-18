# Agent instructions — ros-hacks-apt

## APT signing key — DO NOT CHANGE

The repository **must** always be signed with this GPG fingerprint:

```
24F6F039ADD1E7A19FE61176172DC787BB63A69F
```

Also recorded in `SIGNING_KEY_FPR` (committed).

### Never do these

- **Never** generate a new GPG key for this repository
- **Never** replace, rotate, or overwrite `APT_GPG_PRIVATE_KEY` / `APT_GPG_KEY_NAME` GitHub secrets without explicit user approval
- **Never** commit `.gpg-backup/` or `private.key`
- **Never** export the whole GPG keyring into `private.key` — export **only** the key with fingerprint above
- **Never** publish `ros-hacks.key` unless its fingerprint matches `SIGNING_KEY_FPR`

### If signing fails

Restore the canonical key from the user's external backup at:

`/media/daniel/da1b3b5a-b8a2-4666-8a31-14308d1037a0/daniel/ros-hacks-apt/.gpg-backup/`

Then re-run `scripts/setup-apt-repo.sh update` from the ROS-Hacks repo.

Changing the signing key breaks `apt update` for all users (`NO_PUBKEY` errors).
