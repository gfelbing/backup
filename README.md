# backup

Scripts to make secure incremental backups of a linux system, using

- lvm/luks/gpg2 → encryption layer, password storage
- btrfs → data layer, incremental handling

## Setup

### 1. Configuration

Copy the file config into your home:

```
mkdir ~/.config/backup
cp config ~/.config/backup
```

and provide the right values.

### 2. Setup your backup device

The script backup-prepare can be used to setup your backup device.

```
./backup-prepare
```

It nees root privileges in order to prepare your backup device. It uses sudo when it needs it, so you **don't** it as root!

It will do:

1. Format your backup-device with a full-sized lvm-pv
2. Setup a volume group and logical volume using the whole lvm-pv above
3. Generate a 50 character password using all `pwgen -cnys1 50`
4. Stores the password above into a gpg encrypted file
    - **Note**: Since won't provide your password yourself neither see the generated without your gpg private key! Make sure your it is backuped by another procedure or this backup will be unusable on data loss!
5. Uses the password above to encrypt the logical volume
6. Formats the encrypted volume with btrfs and creates a subvolume for the backup

### 3. Execute your first backup!

```
./backup
```

As above, it requires root privileges for mounting and copying your system files. It gets it using sudo so **don't** run it as root!

It will do:

1. Mount the encrypted backup device
2. Update the backup with rsync
3. Create a snapshot with btrfs
4. Unmount your backup device
