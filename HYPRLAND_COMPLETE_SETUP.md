## 7. Ambiente Hyprland (Wayland Compositor)

### 7.1 Instalação do Hyprland
```bash
# Hyprland e dependências essenciais
sudo pacman -S hyprland wayland wayland-protocols sddm

# Utilitários oficiais do Hyprland
sudo pacman -S hyprlock hypridle hyprcursor hyprpaper hyprpicker aquamarine hyprwayland-scanner hyprutils hyprgraphics 
```

### 7.2 Dependências Essenciais para Hyprland
```bash
# Terminal (essencial - Hyprland não vem com terminal)
sudo pacman -S kitty

# Gerenciador de arquivos
sudo pacman -S dolphin dolphin-plugins ark kio-admin

# Lançador de aplicações
sudo pacman -S wofi
# ou
sudo pacman -S rofi-wayland

# Barra de status
sudo pacman -S waybar

# Screenshot
sudo pacman -S grim slurp

# Controle de áudio (OBRIGATÓRIO)
sudo pacman -S pavucontrol

# Gerenciador de notificações
sudo pacman -S dunst mako
```

### 7.3 Utilitários Específicos Wayland
```bash
# Clipboard manager para Wayland
sudo pacman -S wl-clipboard cliphist

# Polkit para autenticação gráfica
sudo pacman -S polkit-kde-agent

# Portal para Wayland
sudo pacman -S xdg-desktop-portal-hyprland 
```

### 7.4 Portais Desktop (ESSENCIAIS)
```bash
# Sistema de portais base
sudo pacman -S xdg-desktop-portal xdg-desktop-portal-hyprland xdg-desktop-portal-gtk

# Suporte nativo Qt para Wayland
sudo pacman -S qt5-wayland qt6-wayland

# Diretórios padrão do usuário
sudo pacman -S xdg-user-dirs-gtk
```

### 7.5 Aplicações de Mídia
```bash
# Players de mídia com suporte Wayland
sudo pacman -S mpv vlc

# Editor de vídeo
sudo pacman -S kdenlive

# Editor de imagem
sudo pacman -S gimp
```

### 7.6 Ativação do Display Manager
```bash
sudo systemctl enable sddm
```

**Observações Hyprland:**
- Hyprland é um compositor minimalista, não vem com aplicações básicas
- TODOS os utilitários precisam ser instalados separadamente
- Requer componentes específicos para Wayland
- pavucontrol é ESSENCIAL (não tem controle de áudio integrado)
- Precisa de terminal, gerenciador de arquivos, lançador de apps, etc.

---

## 8. Configurações Finais Comuns

### 8.1 Configuração do Flatpak
```bash
# Adicionar repositório Flathub
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### 8.2 Configuração do Firewall (Opcional)
```bash
# Configurar regras básicas do UFW
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

---

## 9. Recursos e Configurações Extras para Hyprland

### 9.1 Temas e Personalização
```bash
# Ferramentas de configuração de temas
sudo pacman -S nwg-look qt5ct qt6ct

# Temas GTK populares
sudo pacman -S papirus-icon-theme
sudo pacman -S materia-gtk-theme

# Temas de cursor modernos (Hyprcursor - AUR)
yay -S bibata-cursor-theme
```

---

Em Andamento ...
