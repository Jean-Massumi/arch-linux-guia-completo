# Recomendações de Aplicativos - Arch Linux

Lista curada de aplicativos essenciais para Arch Linux, organizados por categoria. Todos os pacotes listados são universais e funcionam independentemente do ambiente desktop escolhido.

---

## Índice

- [Navegadores Web](#navegadores-web)
- [Editores de Texto e IDEs](#editores-de-texto-e-ides)
- [Suítes de Escritório](#suítes-de-escritório)
- [Comunicação](#comunicação)
- [Reprodutores de Mídia](#reprodutores-de-mídia)
- [Edição de Imagens](#edição-de-imagens)
- [Gravação de Tela](#gravação-de-tela)
- [Programação e Desenvolvimento](#programação-e-desenvolvimento)
- [Linguagens de Programação](#linguagens-de-programação)
- [Utilitários do Sistema](#utilitários-do-sistema)
- [Gerenciadores de Download](#gerenciadores-de-download)
- [Gaming](#gaming)
- [Segurança](#segurança)
- [Instalando yay](#instalando-yay-aur-helper)

---

## Navegadores Web

1. **Firefox**
   ```bash
   sudo pacman -S firefox
   ```

2. **Chromium**
   ```bash
   sudo pacman -S chromium
   ```

---

## Editores de Texto e IDEs

1. **VS Code**
   ```bash
   sudo pacman -S code
   ```

2. **Neovim**
   ```bash
   sudo pacman -S neovim
   ```

---

## Suítes de Escritório

**LibreOffice**
```bash
sudo pacman -S libreoffice-fresh-pt-br
```


## Comunicação

1. **Discord**
   ```bash
   sudo pacman -S discord
   ```

2. **Telegram Desktop**
   ```bash
   sudo pacman -S telegram-desktop
   ```

3. **Slack**
   ```bash
   yay -S slack-desktop
   ```

## Reprodutores de Mídia

1. **VLC**
   ```bash
   sudo pacman -S vlc
   ```

2. **MPV**
   ```bash
   sudo pacman -S mpv
   ```

3. **Spotify**
   ```bash
   yay -S spotify
   ```

## Edição de Imagens

1. **GIMP**
   ```bash
   sudo pacman -S gimp
   ```

2. **Inkscape**
   ```bash
   sudo pacman -S inkscape
   ```

3. **Krita**
   ```bash
   sudo pacman -S krita
   ```

## Gravação de Tela

1. **OBS Studio**
   ```bash
   sudo pacman -S obs-studio
   ```

2. **SimpleScreenRecorder**
   ```bash
   sudo pacman -S simplescreenrecorder
   ```

3. **Kdenlive**
   ```bash
   sudo pacman -S kdenlive
   ```

## Programação e Desenvolvimento

1. **Git**
   ```bash
   sudo pacman -S git
   ```

2. **Docker**
   ```bash
   sudo pacman -S docker docker-compose
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **Python**
   ```bash
   sudo pacman -S python python-pip
   ```

## Linguagens de Programação

1. **Node.js / npm**
   ```bash
   sudo pacman -S nodejs npm
   ```

2. **Rust**
   ```bash
   sudo pacman -S rust
   ```

3. **Go**
   ```bash
   sudo pacman -S go
   ```

## Utilitários do Sistema

1. **htop**
   ```bash
   sudo pacman -S htop
   ```

2. **btop**
   ```bash
   sudo pacman -S btop
   ```

3. **GParted**
   ```bash
   sudo pacman -S gparted
   ```

## Gerenciadores de Download

1. **qBittorrent**
   ```bash
   sudo pacman -S qbittorrent
   ```

2. **Transmission**
   ```bash
   sudo pacman -S transmission-gtk
   ```

3. **aria2**
   ```bash
   sudo pacman -S aria2
   ```

## Gaming

1. **Steam**
   ```bash
   sudo pacman -S steam
   ```

2. **Lutris**
   ```bash
   sudo pacman -S lutris
   ```

3. **Heroic Games Launcher**
   ```bash
   yay -S heroic-games-launcher-bin
   ```

## Segurança

1. **KeePassXC**
   ```bash
   sudo pacman -S keepassxc
   ```

2. **Bitwarden**
   ```bash
   yay -S bitwarden
   ```

3. **ProtonVPN**
   ```bash
   yay -S protonvpn
   ```

## Instalando yay (AUR Helper)

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
