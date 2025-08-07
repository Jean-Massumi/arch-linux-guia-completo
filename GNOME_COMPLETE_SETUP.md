## 5. Ambiente GNOME

### 5.1 Instalação do GNOME
```bash
# Grupo GNOME completo (inclui aplicações básicas)
sudo pacman -S gnome gnome-extra

# OU instalação mínima
sudo pacman -S gnome-shell gdm gnome-control-center gnome-terminal nautilus
```

### 5.2 Ativação do GDM
```bash
sudo systemctl enable gdm
```

### 5.3 Pacotes Específicos para GNOME
```bash
# Aplicações GNOME adicionais (opcionais)
sudo pacman -S gnome-tweaks gnome-shell-extensions

# Terminal moderno (alternativa ao gnome-terminal)
sudo pacman -S kitty

# Player de mídia nativo GNOME
sudo pacman -S totem
```

---
