# Btrfs Snapshot

## Script

To simplify the snapshot management, please consider to use this script:

```
#!/bin/bash

set -euo pipefail

# ============================================================
# Configuration
# ============================================================

REPO="/mnt/blankon-repo"
SNAPSHOTS="/mnt/blankon-repo-snapshots"

DEVICE="/dev/sdb"
BTRFS_ROOT="/mnt/btrfs-root"

# ============================================================
# Usage
# ============================================================

usage() {
    cat <<EOF
Usage:

  $0 snapshot <name>
  $0 restore <name>
  $0 list
  $0 delete <name>

Examples:

  $0 snapshot before-sync
  $0 snapshot before-publish

  $0 list

  $0 restore before-sync
  $0 restore @repo-before-restore-20260826-144933

  $0 delete before-sync
EOF

    exit 1
}

# ============================================================
# Validation
# ============================================================

require_root() {
    if [[ $EUID -ne 0 ]]; then
        echo "ERROR: Please run as root."
        echo
        echo "Example:"
        echo "  sudo $0 snapshot before-sync"
        exit 1
    fi
}

validate_name() {
    local name="$1"

    if [[ -z "$name" ]]; then
        echo "ERROR: Name cannot be empty."
        exit 1
    fi

    if [[ ! "$name" =~ ^[a-zA-Z0-9._@-]+$ ]]; then
        echo "ERROR: Invalid name: $name"
        echo
        echo "Allowed characters:"
        echo "  letters, numbers, '.', '_', '@', '-'"
        exit 1
    fi
}

check_repo_mount() {
    if ! mountpoint -q "$REPO"; then
        echo "ERROR: Repository is not mounted:"
        echo "  $REPO"
        exit 1
    fi
}

check_snapshots_mount() {
    if ! mountpoint -q "$SNAPSHOTS"; then
        echo "ERROR: Snapshot filesystem is not mounted:"
        echo "  $SNAPSHOTS"
        exit 1
    fi
}

# ============================================================
# Btrfs top-level mount
# ============================================================

mount_btrfs_root() {
    mkdir -p "$BTRFS_ROOT"

    if mountpoint -q "$BTRFS_ROOT"; then
        echo "ERROR: $BTRFS_ROOT is already mounted."
        exit 1
    fi

    mount -o subvolid=5 "$DEVICE" "$BTRFS_ROOT"
}

unmount_btrfs_root() {
    if mountpoint -q "$BTRFS_ROOT"; then
        umount "$BTRFS_ROOT"
    fi
}

# ============================================================
# Find a snapshot or pre-restore backup
# ============================================================

find_restore_source() {
    local name="$1"

    # Regular snapshot
    if [[ -d "$SNAPSHOTS/$name" ]] &&
       btrfs subvolume show "$SNAPSHOTS/$name" >/dev/null 2>&1; then

        echo "$SNAPSHOTS/$name"
        return 0
    fi

    # Pre-restore backup
    if [[ -d "$BTRFS_ROOT/$name" ]] &&
       btrfs subvolume show "$BTRFS_ROOT/$name" >/dev/null 2>&1; then

        echo "$BTRFS_ROOT/$name"
        return 0
    fi

    return 1
}

# ============================================================
# Snapshot
# ============================================================

snapshot() {
    local name="$1"
    local destination="$SNAPSHOTS/$name"

    validate_name "$name"
    check_repo_mount
    check_snapshots_mount

    if [[ -e "$destination" ]]; then
        echo "ERROR: Snapshot already exists:"
        echo "  $destination"
        exit 1
    fi

    echo
    echo "Creating read-only snapshot:"
    echo "  $name"
    echo

    btrfs subvolume snapshot -r \
        "$REPO" \
        "$destination"

    echo
    echo "========================================"
    echo "Snapshot created successfully"
    echo "========================================"
    echo
    echo "Name:"
    echo "  $name"
    echo
    echo "Location:"
    echo "  $destination"
    echo
}

# ============================================================
# List
# ============================================================

list_snapshots() {
    check_snapshots_mount

    echo "Available snapshots:"
    echo

    local found_snapshots=false

    while IFS= read -r snapshot; do
        if btrfs subvolume show "$snapshot" >/dev/null 2>&1; then
            found_snapshots=true
            echo "  $(basename "$snapshot")"
        fi
    done < <(
        find "$SNAPSHOTS" \
            -mindepth 1 \
            -maxdepth 1 \
            -type d \
            -print |
        sort
    )

    if [[ "$found_snapshots" == false ]]; then
        echo "  No snapshots found."
    fi

    echo
    echo "Pre-restore backups:"
    echo

    local found_backups=false

    # Temporarily mount Btrfs top-level to inspect
    # @repo-before-restore-* subvolumes.
    mount_btrfs_root

    while IFS= read -r backup; do
        found_backups=true
        echo "  $(basename "$backup")"
    done < <(
        find "$BTRFS_ROOT" \
            -mindepth 1 \
            -maxdepth 1 \
            -type d \
            -name '@repo-before-restore-*' \
            -print |
        sort
    )

    unmount_btrfs_root

    if [[ "$found_backups" == false ]]; then
        echo "  No pre-restore backups found."
    fi

    echo
}

# ============================================================
# Restore
# ============================================================

restore() {
    local name="$1"

    validate_name "$name"
    check_repo_mount
    check_snapshots_mount

    echo
    echo "Checking restore source..."

    # We need the Btrfs top-level mounted because pre-restore
    # backups live there.
    mount_btrfs_root

    local source=""

    if [[ -d "$SNAPSHOTS/$name" ]] &&
       btrfs subvolume show "$SNAPSHOTS/$name" >/dev/null 2>&1; then

        source="$SNAPSHOTS/$name"

    elif [[ -d "$BTRFS_ROOT/$name" ]] &&
         btrfs subvolume show "$BTRFS_ROOT/$name" >/dev/null 2>&1; then

        source="$BTRFS_ROOT/$name"

    else
        unmount_btrfs_root

        echo "ERROR: Snapshot or pre-restore backup does not exist:"
        echo "  $name"

        exit 1
    fi

    echo
    echo "========================================"
    echo "WARNING: Repository restore"
    echo "========================================"
    echo
    echo "Restore source:"
    echo "  $source"
    echo
    echo "Current repository:"
    echo "  $REPO"
    echo
    echo "The current @repo will NOT be deleted."
    echo "It will be preserved as:"
    echo
    echo "  @repo-before-restore-TIMESTAMP"
    echo

    read -r -p "Continue? [y/N] " answer

    if [[ "$answer" != "y" && "$answer" != "Y" ]]; then
        unmount_btrfs_root
        echo "Aborted."
        exit 0
    fi

    echo
    echo "Unmounting current repository..."

    umount "$REPO"

    # Generate unique backup name.
    local backup="@repo-before-restore-$(date +%Y%m%d-%H%M%S)"

    echo
    echo "Preserving current repository:"
    echo "  $backup"

    mv \
        "$BTRFS_ROOT/@repo" \
        "$BTRFS_ROOT/$backup"

    echo
    echo "Creating new writable @repo..."

    # IMPORTANT:
    #
    # No "-r" here.
    #
    # The source snapshot remains read-only.
    # The new @repo is writable.
    btrfs subvolume snapshot \
        "$source" \
        "$BTRFS_ROOT/@repo"

    echo
    echo "Unmounting temporary Btrfs mount..."

    unmount_btrfs_root

    echo
    echo "Remounting repository..."

    mount "$REPO"

    if ! mountpoint -q "$REPO"; then
        echo
        echo "ERROR: Failed to remount:"
        echo "  $REPO"
        echo
        echo "The restored @repo exists, but it could not be mounted."
        exit 1
    fi

    echo
    echo "========================================"
    echo "Restore complete!"
    echo "========================================"
    echo
    echo "Restored:"
    echo "  $name"
    echo
    echo "Previous repository preserved as:"
    echo "  $backup"
    echo
    echo "Repository:"
    echo "  $REPO"
    echo
}

# ============================================================
# Delete regular snapshot
# ============================================================

delete_snapshot() {
    local name="$1"
    local snapshot="$SNAPSHOTS/$name"

    validate_name "$name"
    check_snapshots_mount

    # Explicitly prevent deletion of pre-restore backups.
    if [[ "$name" == @repo-before-restore-* ]]; then
        echo "ERROR: Pre-restore backups cannot be deleted with this command."
        echo
        echo "This command only deletes regular snapshots."
        exit 1
    fi

    if ! btrfs subvolume show "$snapshot" >/dev/null 2>&1; then
        echo "ERROR: Snapshot does not exist:"
        echo "  $snapshot"
        exit 1
    fi

    echo
    echo "========================================"
    echo "WARNING: Delete snapshot"
    echo "========================================"
    echo
    echo "Snapshot:"
    echo "  $snapshot"
    echo
    echo "This operation cannot be undone."
    echo

    read -r -p "Continue? [y/N] " answer

    if [[ "$answer" != "y" && "$answer" != "Y" ]]; then
        echo "Aborted."
        exit 0
    fi

    echo
    echo "Deleting snapshot..."

    btrfs subvolume delete "$snapshot"

    echo
    echo "Snapshot deleted:"
    echo "  $name"
    echo
}

# ============================================================
# Main
# ============================================================

require_root

COMMAND="${1:-}"

case "$COMMAND" in

    snapshot)
        [[ $# -eq 2 ]] || usage
        snapshot "$2"
        ;;

    restore)
        [[ $# -eq 2 ]] || usage
        restore "$2"
        ;;

    list)
        [[ $# -eq 1 ]] || usage
        list_snapshots
        ;;

    delete)
        [[ $# -eq 2 ]] || usage
        delete_snapshot "$2"
        ;;

    *)
        usage
        ;;

esac
```
