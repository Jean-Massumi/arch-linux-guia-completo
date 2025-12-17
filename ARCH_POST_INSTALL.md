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

## 3. Otimizações Essenciais do Sistema

### 3.1 Configuração do Pacman

```bash
# Editar configuração do pacman
sudo nano /etc/pacman.conf

# Descomente e configure as seguintes linhas:
Color                    # Habilita cores na saída
ParallelDownloads = 15   # Permite 15 downloads simultâneos
VerbosePkgLists          # Lista detalhada de pacotes

# Para suporte a programas 32-bits (jogos, Wine, Steam):
# Descomente estas linhas:
[multilib]
Include = /etc/pacman.d/mirrorlist

# Salvar e sair (Ctrl+O, Enter, Ctrl+X)

# Atualizar base de dados
sudo pacman -Sy
```

### 3.2 Reflector Automático

Mantém os mirrors atualizados automaticamente para velocidades de download otimizadas.

```bash
# Instalar reflector
sudo pacman -S reflector

# Configurar atualização automática semanal
sudo systemctl enable reflector.timer
sudo systemctl start reflector.timer

# Editar configuração (opcional)
sudo nano /etc/xdg/reflector/reflector.conf

# Configuração recomendada para Brasil:
--save /etc/pacman.d/mirrorlist
--country Brazil
--protocol https
--latest 10
--sort rate
```

### 3.3 Otimização de Swap/Zram

#### Opção A: Ajustar Swappiness (Swap tradicional)

```bash
# Verificar swap atual
swapon --show
free -h

# Ajustar swappiness (reduz uso de swap, prioriza RAM)
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.d/99-swappiness.conf

# Aplicar imediatamente
sudo sysctl vm.swappiness=10
```

#### Opção B: Zram (Recomendado - melhor performance)

Zram cria swap comprimido na RAM, muito mais rápido que swap em disco.

```bash
# Instalar zram-generator
sudo pacman -S zram-generator

# Criar diretório de configuração
sudo mkdir -p /etc/systemd/zram-generator.conf.d

# Configurar zram (usar metade da RAM)
echo -e "[zram0]\nzram-size = ram / 2" | sudo tee /etc/systemd/zram-generator.conf.d/zram.conf

# Desabilitar swap tradicional (se tiver)
sudo swapoff -a
sudo nano /etc/fstab  # Comentar linha do swap

# Reiniciar para aplicar
sudo reboot
```

### 3.4 Otimizações para SSD

```bash
# Verificar se TRIM está habilitado
sudo systemctl status fstrim.timer

# Se não estiver, habilitar:
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer

# Executar TRIM manualmente (teste)
sudo fstrim -v /
```

---

## 4. Instalação de Pacotes Base

### 4.1 Rede e Bluetooth

```bash
# Instalar ferramentas para gerenciamento de rede sem fio e Bluetooth
sudo pacman -S wpa_supplicant network-manager-applet bluez bluez-utils

# Ativar e iniciar serviços do Bluetooth
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service
```

### 4.2 Suporte a Impressoras

```bash
# Instalar sistema de impressão CUPS
sudo pacman -S cups cups-pdf

# Ativar serviço de impressão
sudo systemctl enable cups
sudo systemctl start cups

# Interface web de gerenciamento: http://localhost:631
```

### 4.3 Sistema de Áudio Moderno

```bash
# PipeWire - substituto moderno para PulseAudio/JACK
sudo pacman -S pipewire pipewire-pulse pipewire-alsa pipewire-jack wireplumber

# Ativar serviços de áudio para o usuário
systemctl --user enable pipewire pipewire-pulse wireplumber
systemctl --user start pipewire pipewire-pulse wireplumber

# Testar áudio
speaker-test -t wav -c 2
```

### 4.4 Drivers Gráficos

**IMPORTANTE**: A instalação de drivers gráficos depende completamente da sua configuração de hardware. As bibliotecas e drivers instalados variam conforme as especificações da sua máquina.

É fundamental que cada usuário verifique as especificações do seu PC e instale as bibliotecas apropriadas para garantir o melhor funcionamento e desempenho.

**Referências oficiais:**
- NVIDIA + Hyprland: https://wiki.hypr.land/Nvidia/
- Drivers NVIDIA (Arch Wiki): https://wiki.archlinux.org/title/NVIDIA  
- Drivers Xorg/Wayland: https://wiki.archlinux.org/title/Xorg#Driver_installation

#### 4.4.1 Sistema Híbrido Intel + NVIDIA

```bash
# Drivers Mesa para Intel (OpenGL e Vulkan)
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel

# Drivers proprietários NVIDIA
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils nvidia-settings

# Suporte EGL para NVIDIA com Wayland (essencial para Hyprland)
sudo pacman -S egl-wayland

# Verificar instalação
nvidia-smi
glxinfo | grep "OpenGL renderer"
```

#### 4.4.2 GPU AMD

```bash
# Drivers Mesa para AMD (OpenGL e Vulkan)
sudo pacman -S mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon

# Aceleração de vídeo
sudo pacman -S libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau

# Verificar instalação
glxinfo | grep "OpenGL renderer"
vulkaninfo | grep "deviceName"
```

#### 4.4.3 Intel Integrado (Apenas)

```bash
# Drivers Mesa para Intel
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel

# Aceleração de vídeo
sudo pacman -S intel-media-driver libva-intel-driver

# Verificar instalação
glxinfo | grep "OpenGL renderer"
```

### 4.5 Ferramentas Essenciais do Sistema

```bash
# Monitoramento de recursos
sudo pacman -S htop btop nvtop

# Sensores de hardware (temperatura, ventoinhas)
sudo pacman -S lm_sensors
sudo sensors-detect  # Detectar sensores (aceite os padrões)
sensors              # Ver temperaturas

# Firewall
sudo pacman -S ufw
sudo ufw enable           # Habilitar firewall
sudo ufw status verbose   # Ver status
```

### 4.6 Utilitários do Sistema

```bash
# Ferramentas de sistema de arquivos
sudo pacman -S e2fsprogs dosfstools

# Utilitários básicos
sudo pacman -S git wget curl
```

### 4.7 Ferramentas de Compressão

```bash
# Suporte a múltiplos formatos de compressão
sudo pacman -S zip unzip p7zip unrar tar
```

### 4.8 Ferramentas de Diagnóstico

```bash
# Informações detalhadas do sistema
sudo pacman -S inxi neofetch

# Ver informações do hardware
inxi -Fxz           # Informações completas
lspci               # Dispositivos PCI
lsusb               # Dispositivos USB
lsblk               # Discos e partições
```

### 4.9 Codecs de Mídia e Fontes

```bash
# Codecs de áudio e vídeo
sudo pacman -S ffmpeg gst-libav gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-plugins-base gstreamer

# Fontes essenciais
sudo pacman -S noto-fonts noto-fonts-emoji ttf-dejavu ttf-liberation ttf-font-awesome ttf-jetbrains-mono-nerd ttf-roboto

# Fontes adicionais (opcional)
sudo pacman -S ttf-ubuntu-font-family ttf-opensans
```

---

## 5. Instalação do YAY (AUR Helper)

O YAY permite instalar pacotes do AUR (Arch User Repository), que contém milhares de aplicações adicionais.

```bash
# Instalar dependências
sudo pacman -S --needed git base-devel

# Clonar repositório do YAY
git clone https://aur.archlinux.org/yay.git

# Entrar no diretório e compilar
cd yay
makepkg -si

# Remover diretório após instalação
cd ..
rm -rf yay

# Testar instalação
yay --version

# Atualizar sistema incluindo AUR
yay -Syu
```

### Uso Básico do YAY

```bash
# Buscar pacote
yay -Ss nome_do_pacote

# Instalar pacote
yay -S nome_do_pacote

# Remover pacote
yay -R nome_do_pacote

# Atualizar tudo (sistema + AUR)
yay -Syu

# Limpar cache de pacotes
yay -Sc
```

---

## 6. Backup do Sistema (Timeshift)

```bash
# Instalar Timeshift
yay -S timeshift

# Executar Timeshift
sudo timeshift-gtk

# Configuração recomendada:
# - Tipo: RSYNC
# - Localização: Selecionar partição com espaço suficiente
# - Agendamento: Semanal
# - Incluir: Apenas partição raiz (/)
```

**Alternativa para sistemas Btrfs:**

```bash
# Snapper (para sistemas Btrfs)
sudo pacman -S snapper
```

---

## 7. Aplicações Básicas Independentes de DE/WM

```bash
# Navegador web
sudo pacman -S firefox

# Gerenciador de pacotes Flatpak
sudo pacman -S flatpak

# Habilitar repositório Flathub
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Aplicações diversas
sudo pacman -S discord libreoffice-fresh obs-studio

# Gerenciador de arquivos leve (para WM sem DE)
sudo pacman -S thunar

# Terminal file manager (opcional)
sudo pacman -S ranger
```

---

## 8. Ambientes Desktop e Window Managers

### 8.1 GNOME

**GNOME** é um ambiente desktop moderno e elegante, focado na simplicidade e produtividade. É o padrão em distribuições como Ubuntu e Fedora, oferecendo uma interface limpa com workflow baseado em Activities e Workspaces.

**Características:**
- Interface moderna e intuitiva
- Excelente para produtividade
- Boa integração com aplicações
- Suporte robusto a extensões

**Para instalação completa do GNOME com todas as configurações:**

[Guia Completo de Instalação do GNOME](./GNOME_COMPLETE_SETUP.md)

---

### 8.2 KDE Plasma

**KDE Plasma** é um ambiente desktop altamente customizável e rico em recursos. É perfeito para usuários que gostam de personalizar cada aspecto do sistema, oferecendo uma experiência similar ao Windows, mas com muito mais flexibilidade.

**Características:**
- Altamente customizável
- Interface familiar (similar ao Windows)
- Excelente performance
- Suite completa de aplicações KDE

**Para instalação completa do KDE Plasma com todas as configurações:**

[Guia Completo de Instalação do KDE Plasma](./KDE_COMPLETE_SETUP.md)

---

### 8.3 Hyprland

**Hyprland** é um compositor Wayland moderno com tiling dinâmico, conhecido por suas animações suaves e alta customização. É a escolha perfeita para entusiastas que querem um ambiente bonito, eficiente e totalmente personalizável.

**Características:**
- Tiling window manager dinâmico
- Animações e efeitos visuais impressionantes
- Excelente performance
- Configuração via arquivo de texto
- Suporte nativo ao Wayland

**Para instalação completa do Hyprland com todas as configurações:**

[Guia Completo de Instalação do Hyprland](./HYPRLAND_COMPLETE_SETUP.md)

---

## 9. Verificação Final do Sistema

Após completar a instalação, execute estas verificações:

```bash
# Verificar serviços com falha
systemctl --failed

# Ver erros do último boot
journalctl -p 3 -xb

# Verificar status do áudio
systemctl --user status pipewire

# Testar áudio
speaker-test -t wav -c 2

# Verificar driver de vídeo
glxinfo | grep "OpenGL renderer"
vulkaninfo | grep "deviceName"

# Verificar temperatura do sistema
sensors

# Ver informações completas
inxi -Fxz
neofetch
```

---

## 11. Dicas Finais

### Manutenção Regular

```bash
# Atualizar sistema (incluindo AUR)
yay -Syu

# Limpar cache de pacotes antigos
yay -Sc

# Remover pacotes órfãos
yay -Yc

# Ver pacotes instalados explicitamente
pacman -Qe

# Ver ocupação de disco
df -h
du -sh ~/.cache/
```

### Comandos Úteis

```bash
# Buscar arquivo de configuração
sudo find /etc -name "arquivo.conf"

# Ver logs em tempo real
journalctl -f

# Listar serviços ativos
systemctl list-units --type=service --state=running

# Reiniciar serviço
sudo systemctl restart nome_do_servico

# Ver portas abertas
sudo ss -tulpn
```

### Documentação e Ajuda

- Arch Wiki: https://wiki.archlinux.org/
- Fórum oficial: https://bbs.archlinux.org/
- Manual do pacman: `man pacman`
- Manual de qualquer comando: `man nome_do_comando`
- Buscar no wiki: `wiki-search termo`

---

**Importante**: Mantenha sempre um backup das suas configurações importantes e documente as modificações que fizer no sistema para facilitar futuras reinstalações ou troubleshooting.

**Próximo passo**: Escolha e instale seu ambiente desktop/window manager preferido seguindo os guias específicos listados na seção 8.
