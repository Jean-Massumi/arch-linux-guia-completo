
## 6. Ambiente KDE Plasma

### 6.1 Instalação do KDE Plasma
```bash
# Grupo KDE Plasma
sudo pacman -S plasma kde-applications
```

### 6.2 Ativação do SDDM
```bash
sudo systemctl enable sddm
```

### 6.3 Pacotes Específicos para KDE
```bash
# Controle de áudio KDE (opcional - Plasma já tem controle integrado)
sudo pacman -S kmix

# Editor de vídeo KDE
sudo pacman -S kdenlive

# Player de mídia
sudo pacman -S mpv vlc
```

---
