# Guia de Pós-Instalação do Arch Linux

## 1. Conectando ao Wi-Fi

Após finalizar a instalação básica do Arch Linux:

```bash
# Verificar se o NetworkManager está instalado e ativo
systemctl status NetworkManager

# Listar redes Wi-Fi disponíveis
nmcli device wifi list

# Conectar a uma rede Wi-Fi específica
nmcli device wifi connect "nome_da_rede" password "sua_senha"
```

---

## 2. Atualizando o Sistema

```bash
# Atualizar todos os pacotes do sistema
sudo pacman -Syu
```

---

## 3. Instalação de Pacotes Base Comuns

### 3.1 Rede e Bluetooth

```bash
# Instalar ferramentas para gerenciamento de rede sem fio e Bluetooth
sudo pacman -S wpa_supplicant network-manager-applet bluez bluez-utils

# Ativar e iniciar serviços do Bluetooth
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service
```

### 3.2 Suporte a Impressoras

```bash
# Instalar sistema de impressão CUPS
sudo pacman -S cups cups-pdf

# Ativar serviço de impressão
sudo systemctl enable cups
```

### 3.2 Sistema de Áudio Moderno
```bash
# PipeWire - substituto moderno para PulseAudio/JACK
sudo pacman -S pipewire pipewire-pulse wireplumber

# Ativar serviços de áudio para o usuário
systemctl --user enable pipewire pipewire-pulse wireplumber
```

### 3.3 Drivers Gráficos (Configuração Híbrida Intel + NVIDIA)
> **⚠️ Importante**: A instalação de drivers gráficos **depende completamente da sua configuração de hardware**. As bibliotecas e drivers instalados variam conforme as especificações da sua máquina. 

> **É fundamental que cada usuário verifique as especificações do seu PC e instale as bibliotecas apropriadas** para garantir o melhor funcionamento e desempenho.

> O exemplo abaixo é **apenas para sistemas híbridos Intel + NVIDIA**. Para outras configurações (AMD, Intel integrado, NVIDIA dedicado, etc.), consulte os links de referência:

> - **NVIDIA + Hyprland**: https://wiki.hypr.land/Nvidia/
> - **Drivers NVIDIA (Arch Wiki)**: https://wiki.archlinux.org/title/NVIDIA  
> - **Drivers Xorg/Wayland**: https://wiki.archlinux.org/title/Xorg#Driver_installation

```bash
# Drivers Mesa para Intel (OpenGL e Vulkan)
sudo pacman -S mesa lib32-mesa vulkan-intel

# Drivers proprietários NVIDIA
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils nvidia-settings

# Suporte EGL para NVIDIA com Wayland (essencial para Hyprland)
sudo pacman -S egl-wayland
```

### 3.4 Ferramentas de Sistema Essenciais
```bash
# Utilitários básicos
sudo pacman -S htop nvtop lm_sensors zip unrar p7zip tar ntfs-3g

# Firewall
sudo pacman -S ufw

# Ativar firewall (opcional - configure conforme necessário)
sudo ufw enable
```

### 3.5 Codecs de Mídia e Fontes
```bash
# Codecs de áudio e vídeo
sudo pacman -S ffmpeg gst-libav gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-plugins-base gstreamer

# Fontes essenciais
sudo pacman -S noto-fonts noto-fonts-emoji ttf-dejavu ttf-liberation ttf-font-awesome ttf-jetbrains-mono-nerd ttf-roboto
```

### 3.6 Instalação do YAY (AUR Helper)
```bash
# Clonar repositório do YAY
git clone https://aur.archlinux.org/yay.git

# Entrar no diretório e compilar
cd yay
makepkg -si

# Remover diretório após instalação
cd ..
rm -rf yay
```

---

## 4. Aplicações Básicas Independentes de DE/WM
```bash
# Navegador web
sudo pacman -S firefox

# Gerenciador de pacotes Flatpak
sudo pacman -S flatpak

# Aplicações diversas
sudo pacman -S discord libreoffice-fresh obs-studio
```

---

## 5. Ambiente Desktop GNOME

**GNOME** é um ambiente desktop moderno e elegante, focado na simplicidade e produtividade. É o padrão em distribuições como Ubuntu e Fedora, oferecendo uma interface limpa com workflow baseado em Activities e Workspaces.

**Características:**
- Interface moderna e intuitiva
- Excelente para produtividade
- Boa integração com aplicações
- Suporte robusto a extensões

**Para instalação completa do GNOME com todas as configurações:**

👉 **[Guia Completo de Instalação do GNOME](./GNOME_COMPLETE_SETUP.md)**

---

## 6. Ambiente Desktop KDE Plasma

**KDE Plasma** é um ambiente desktop altamente customizável e rico em recursos. É perfeito para usuários que gostam de personalizar cada aspecto do sistema, oferecendo uma experiência similar ao Windows, mas com muito mais flexibilidade.

**Características:**
- Altamente customizável
- Interface familiar (similar ao Windows)
- Excelente performance
- Suite completa de aplicações KDE

**Para instalação completa do KDE Plasma com todas as configurações:**

👉 **[Guia Completo de Instalação do KDE Plasma](./KDE_COMPLETE_SETUP.md)**

---

## 7. Gerenciador de Janelas Hyprland

**Hyprland** é um compositor Wayland moderno com tiling dinâmico, conhecido por suas animações suaves e alta customização. É a escolha perfeita para entusiastas que querem um ambiente bonito, eficiente e totalmente personalizável.

**Características:**
- Tiling window manager dinâmico
- Animações e efeitos visuais impressionantes
- Excelente performance
- Configuração via arquivo de texto
- Suporte nativo ao Wayland

**Para instalação completa do Hyprland com todas as configurações:**

👉 **[Guia Completo de Instalação do Hyprland](./HYPRLAND_COMPLETE_SETUP.md)**

---

**Dica Final**: Mantenha sempre um backup das suas configurações importantes e documente as modificações que fizer no sistema para facilitar futuras reinstalações ou troubleshooting.
