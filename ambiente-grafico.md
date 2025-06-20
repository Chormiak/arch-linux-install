# Instalando ambiente gráfico 'KDE Plasma' no Arch Linux
Guia de como instalar o KDE Plasma no Arch Linux instalado da guia 1.

---

# 1 Atualizar sistema

```bash
sudo pacman -Syu
```

# 2 Instalar servidor gráfico e utilitários do KDE

```bash
sudo pacman -S xorg xorg-xinit                   # servidor gráfico base
sudo pacman -S plasma dolphin ark gwenview konsole firefox vlc

```

```bash
#Recomandado selecionar as seguintes opções da instalação
1. Apenas dê 'Enter' (para usar o default)
2. Pipeware
3. de sua preferência ou noto-fonts
4. phonon-qt6-vlc

```

Esses pacotes incluem a interface do Plasma e alguns aplicativos essenciais:

* **Dolphin**: gerenciador de arquivos
* **Ark**: compactador/descompactador de arquivos
* **Gwenview**: visualizador de imagens
* **Konsole**: terminal
* **Firefox / VLC**: navegador e reprodutor multimídia

# 3 Instalar suporte a áudio, vídeo e fontes

```bash
sudo pacman -S pipewire pipewire-alsa pipewire-jack pipewire-pulse gstreamer gst-libav gst-plugins-base gst-plugins-good gst-plugins-ugly gst-plugins-bad ffmpeg

sudo pacman -S ttf-roboto ttf-ibm-plex #opcional

```

# 4 Ativar gerenciador de login gráfico (SDDM)

```bash
sudo systemctl enable sddm

```

# 5 Ativar PipeWire para o usuário atual

```bash
systemctl --user enable pipewire pipewire-pulse

```

> Use `loginctl enable-linger <seu-usuario>` se quiser que o serviço seja iniciado fora da sessão.

---

# Final

Reinicie o sistema, e pronto.

---
