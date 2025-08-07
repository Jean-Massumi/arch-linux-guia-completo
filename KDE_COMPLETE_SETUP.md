
## 6. Ambiente KDE Plasma

### 6.1 Instalação do KDE Plasma
```bash
# Grupo KDE Plasma
sudo pacman -S plasma kde-applications

# OU instalação mínima
sudo pacman -S plasma-desktop sddm dolphin konsole
```

### 6.2 Ativação do SDDM
```bash
# SDDM (recomendado para KDE)
sudo pacman -S sddm
sudo systemctl enable sddm

# Ou continuar usando GDM se já tiver
```

### 6.3 Pacotes Específicos para KDE
```bash
# Controle de áudio KDE (opcional - Plasma já tem controle integrado)
sudo pacman -S kmix

# Aplicações KDE essenciais (se não instalou kde-applications)
sudo pacman -S dolphin-plugins ark kio-admin polkit-kde-agent kio kio-extras
sudo pacman -S kate spectacle gwenview okular

# Editor de vídeo KDE
sudo pacman -S kdenlive

# Player de mídia
sudo pacman -S mpv vlc
```

---
