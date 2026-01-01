# GPG Key Management for ROS-Hacks APT Repository

## The Problem

When you update your APT repository, clients get `NO_PUBKEY` errors because:
1. The GPG key used to sign packages must remain **exactly the same** across all updates
2. If the key changes or is regenerated, all previous signatures become invalid
3. Clients cache the public key and won't automatically fetch updates

## The Solution

The `setup-apt-repo.sh` script now automatically:

1. **Backs up your GPG key** to `~/ros-hacks-apt/.gpg-backup/`
   - Private key: `private.key` (for signing)
   - Public key: `public.key` (for distribution)

2. **Restores the key** automatically if you run the script on a different machine

3. **Uses the same key** for all signatures, ensuring consistency

## Important: Key Backup

The `.gpg-backup/` directory contains your **private signing key** and is:
- ✅ Automatically excluded from git (via `.gitignore`)
- ⚠️ **Must be backed up separately** (e.g., encrypted cloud storage, USB drive)
- 🔒 Should be kept secure and never shared publicly

## Usage

### Normal Workflow (No Action Needed)

Just run your normal commands:
```bash
./scripts/setup-apt-repo.sh all
```

The script will automatically:
- Use the existing backed-up key if available
- Generate and backup a new key if none exists
- Ensure the same key is used for all signatures

### Manual Key Management

Check key status:
```bash
./scripts/backup-gpg-key.sh status
```

Backup the key manually:
```bash
./scripts/backup-gpg-key.sh backup
```

Restore the key on a new machine:
```bash
./scripts/backup-gpg-key.sh restore
```

## For Users Experiencing Signature Errors

If users see `NO_PUBKEY` errors after you've already pushed updates with different keys, they need to refresh the public key **once**:

```bash
wget -qO /tmp/ros-hacks.key https://danlil240.github.io/ros-hacks-apt/ros-hacks.key
sudo rm -f /etc/apt/keyrings/ros-hacks.gpg
cat /tmp/ros-hacks.key | sudo gpg --dearmor -o /etc/apt/keyrings/ros-hacks.gpg
sudo apt update
```

After this one-time fix, future updates will work without issues as long as you keep using the same backed-up key.

## Moving to a New Machine

1. Copy the entire `~/ros-hacks-apt/.gpg-backup/` directory to the new machine
2. Run `./scripts/backup-gpg-key.sh restore` to import the key
3. Continue using `setup-apt-repo.sh` as normal

## Key Fingerprint

Your key fingerprint is stored in `~/ros-hacks-apt/.signing_key_fpr` for consistency.
This ensures the same key is always used for signing.
