# Arch Linux - Instalação UEFI com ext4

Este guia cobre a instalação do Arch Linux em modo UEFI usando o sistema de arquivos ext4.

---

## 1. Modificar o terminal da ISO de instalação

### 1.1 Melhorar a fonte (opcional)

Facilita a leitura se o terminal parecer pequeno ou distorcido.

```bash
setfont ter-132b

```

### 1.2 Usar teclado ABNT2 (brasileiro)

Para facilitar a digitação correta, especialmente de acentos e símbolos.

```bash
loadkeys br-abnt2

```

---

## 2. Conectar-se à internet

### 2.1 Testar conexão com a internet

Verifica se o sistema consegue acessar a rede (obrigatório para instalar pacotes).

```bash
ping google.com

```

```bash
#retorna algo como:
64 bytes from 190.98.170.34: icmp_req=1 ttl=57 time=8.22 ms
64 bytes from 190.98.170.34: icmp_req=2 ttl=57 time=3.93 ms
64 bytes from 190.98.170.34: icmp_req=3 ttl=57 time=4.83 ms
```

### 2.2 Conectar via Wi-Fi com IWD (caso não use cabo)

```bash
iwctl
device list
station <adaptador> scan
station <adaptador> get-networks
station <adaptador> connect <nome-da-rede>

```

Após conectar, teste novamente com `ping google.com`.

---

## 3. Particionar o disco (UEFI)

> Se sua máquina for BIOS (Legacy), este guia não serve. Use um guia específico para BIOS.
> 

### 3.1 Ver discos e iniciar particionamento com `cfdisk`

> O nome do `sda` pode mudar dependendo da sua configuração de hardware do disco.
> 

```bash
lsblk
cfdisk /dev/sda

```

- Escolha o rótulo `gpt`
- Crie as seguintes partições:

| Descrição | Tamanho | Tipo |
| --- | --- | --- |
| EFI | 256 MB | EFI System |
| Root (`/`) | 20 GB+ | Linux filesystem |
| Home (`/home`) | restante | Linux filesystem |
| Swap | 4 GB (ou mais) | Linux swap |

Confirme e escreva as mudanças com `Write`, depois `Quit`.

### 3.2 Formatar partições

```bash
mkfs.fat -F32 /dev/sda1              # EFI
mkfs.ext4 /dev/sda2                  # Root
mkfs.ext4 /dev/sda3                  # Home
mkswap /dev/sda4                     # Swap

```

### 3.3 Montar as partições

```bash
mount /dev/sda2 /mnt                 # Root
mkdir -p /mnt/boot/efi /mnt/home
mount /dev/sda1 /mnt/boot/efi        # EFI
mount /dev/sda3 /mnt/home            # Home
swapon /dev/sda4                     # Ativa swap

```

---

## 4. Instalar o sistema base

### 4.1 Atualizar espelhos (opcional, mas recomendado)

```bash
pacman -Sy
pacman -S reflector
reflector --country Brazil --latest 20 --sort rate --verbose --save /etc/pacman.d/mirrorlist
```

### 4.2 Instalar pacotes principais

```bash
pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano vim

```

> Use linux em vez de linux-lts se quiser o kernel mais recente, mas a mais riscos de vir com bugs.
> 

### 4.3 Gerar o arquivo `fstab`

```bash
genfstab -U /mnt >> /mnt/etc/fstab

```

Este arquivo será usado para montar as partições automaticamente no boot.

---

## 5. Configurar o sistema instalado

### 5.1 Acessar o novo sistema com `chroot`

```bash
arch-chroot /mnt

```

### 5.2 Ajustar configurações do pacman

```bash
nano /etc/pacman.conf

```

Descomente:

```bash
...
->#Color
...
->#ParallelDownloads = 5
...
->#[multilib]
->#Include = /etc/pacman.d/mirrorlist
...
```

- `Color` ativa cores pro terminal.
- `ParallelDownloads = 5` faz 5 conexões simultâneas para download.
- `[multilib]` e `Include = /etc/pacman.d/mirrorlist` se quiser suporte a programas 32 bits.

### 5.3 Configurar fuso horário e relógio

```bash
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock --systohc
timedatectl set-ntp true

```

### 5.4 Configurar idioma e teclado

```bash
nano /etc/locale.gen

```

Descomente:

```bash
pt_BR.UTF-8 UTF-8

```

Então rode:

```bash
locale-gen
echo "LANG=pt_BR.UTF-8" > /etc/locale.conf
echo "KEYMAP=br-abnt2" > /etc/vconsole.conf

```

### 5.5 Nome do sistema

```bash
echo "nome que preferir" > /etc/hostname

```

```bash
nano /etc/hosts # ou também pode ser 'host' invés de 'hosts'

```

Adicione:

```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   "nome que escolheu para o sistema".localdomain "também"

```

> Para fazer o espaçamento, use TAB
> 

### 5.6 Criar senha do root

```bash
passwd

```

### 5.7 Ativar sudo para grupo wheel

```bash
nano /etc/sudoers

```

Descomente:

```bash
...
root ALL=(ALL:ALL) ALL

## Uncomment to allow members of group wheel to execute any command
->#%wheel ALL=(ALL:ALL) ALL
...

```

### 5.8 Criar usuário com sudo

```bash
useradd -mG wheel <nome>
passwd <nome>

```

---

## 6. Bootloader e rede

### 6.1 Instalar pacotes essenciais

```bash
pacman -Sy
pacman -S dosfstools mtools networkmanager iwd grub efibootmgr intel-ucode

```

> Use amd-ucode se seu processador for AMD, mas se for máquina virtual não instale
> 

### 6.2 Instalar o GRUB para UEFI

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck

```

### 6.3 Ativar suporte a dual boot (opcional)

```bash
nano /etc/default/grub

```

Descomente:

```bash
GRUB_DISABLE_OS_PROBER=false

```

### 6.4 Gerar o arquivo de configuração do GRUB

```bash
grub-mkconfig -o /boot/grub/grub.cfg

```

### 6.5 Habilitar rede e suporte a IWD (Wi-Fi)

```bash
systemctl enable NetworkManager iwd

```

```bash
nano /etc/NetworkManager/NetworkManager.conf

```

Adicione:

```
[device]
wifi.backend=iwd

```

---

## Final

Reinicie o sistema, remova o pendrive de instalação e aproveite seu novo Arch Linux! Se quiser, prossiga com a instalação do ambiente gráfico (KDE, GNOME etc).

---
