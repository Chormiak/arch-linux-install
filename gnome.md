# Instalação do GNOME no Arch Linux

Este guia assume que você já concluiu a instalação base do Arch Linux em UEFI com ext4, configurou rede, bootloader e criou seu usuário.

---

## 1. Acessar o sistema

Entre com seu usuário criado na instalação base. Se necessário, use `su` para root ou `sudo`.

---

## 2. Instalar pacotes GNOME

### 2.1 Ambiente GNOME completo

```bash
sudo pacman -S gnome gnome-extra
````

* `gnome` → Pacotes essenciais (Shell, Nautilus, configurações etc).
* `gnome-extra` → Conjunto adicional (ferramentas, utilitários, apps GNOME).

### 2.2 Display Manager (GDM)

O GNOME utiliza o **GDM** como gerenciador de sessão.

```bash
sudo pacman -S gdm
sudo systemctl enable gdm
```

---

## 3. Serviços recomendados

### 3.1 Suporte a áudio

```bash
sudo pacman -S pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber
```

Ative o `wireplumber` (gerenciamento de sessão):

```bash
systemctl --user enable wireplumber
```

### 3.2 Suporte a impressão (opcional)

```bash
sudo pacman -S cups
sudo systemctl enable cups
```

### 3.3 Suporte a Bluetooth (opcional)

```bash
sudo pacman -S bluez bluez-utils
sudo systemctl enable bluetooth
```

---

## 4. Aceleração gráfica (drivers)

Instale o driver conforme sua GPU:

* **Intel**:

  ```bash
  sudo pacman -S mesa vulkan-intel
  ```
* **AMD**:

  ```bash
  sudo pacman -S mesa vulkan-radeon
  ```
* **NVIDIA proprietário**:

  ```bash
  sudo pacman -S nvidia nvidia-utils nvidia-settings
  ```
* **NVIDIA open source (nouveau)**:

  ```bash
  sudo pacman -S mesa xf86-video-nouveau
  ```

---

## 5. Reiniciar e iniciar GNOME

```bash
reboot
```

O sistema irá inicializar diretamente no **GNOME** via **GDM**.

---

## Final

Agora seu Arch Linux está configurado com o **GNOME** como ambiente gráfico.
Recomenda-se, após o primeiro login, revisar as configurações de idioma, teclado e aparência pelo **GNOME Settings**.

---