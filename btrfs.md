# Instalando o BTRFS no Arch Linux

Modificações do guia original para usar o sistema de arquivos BTRFS com subvolumes.

---

## 3.2 Formatar partições

```bash
mkfs.fat -F32 /dev/sda1		# Mesmo que o Guia 1
mkfs.btrfs /dev/sda2		# Root
mkfs.btrfs /dev/sda3		# Home
mkswap /dev/sda4		# Sem modificação

```

## 3.3 Montar as partições

```bash
# Criar subvolumes na partição raiz (system)
mount /dev/sda2 /mnt
btrfs subvolume create /mnt/@
umount /mnt

mount -o noatime,compress=zstd,subvol=@ /dev/sda2 /mnt

mkdir -p /mnt/boot/efi /mnt/home
mount /dev/sda1 /mnt/boot/efi
mount /dev/sda3 /mnt/home

swapon /dev/sda4

```

---

## 4.2 Instalar pacotes principais 

```bash
# o mesmo comando do guia 1 + btrsf-progs
pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano vim btrfs-progs

```

---

Siga o resto do guia 1.

---
