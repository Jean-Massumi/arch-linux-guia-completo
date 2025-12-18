# Guia de Pós-Instalação do Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **Configuração completa do sistema pós-instalação base**

---

## Pré-requisito

Este guia assume que você completou a [Instalação Base do Arch Linux](./ARCH_BASE_INSTALL.md).

---

## Índice

- [1. Conectando ao Wi-Fi](#1-conectando-ao-wi-fi)
- [2. Atualizando o Sistema](#2-atualizando-o-sistema)
- [3. Otimizações Essenciais do Sistema](#3-otimizações-essenciais-do-sistema)
- [4. Instalação de Pacotes Base Universais](#4-instalação-de-pacotes-base-universais)
- [5. Instalação do YAY (AUR Helper)](#5-instalação-do-yay-aur-helper)
- [6. Backup do Sistema (Timeshift)](#6-backup-do-sistema-timeshift)
- [7. Aplicações Básicas Independentes de DE/WM](#7-aplicações-básicas-independentes-de-dewm)
- [8. Ambientes Desktop e Window Managers](#8-ambientes-desktop-e-window-managers)
- [9. Otimizações para Gaming (Opcional)](#9-otimizações-para-gaming-opcional)
- [10. Ferramentas Avançadas de Rede (Opcional)](#10-ferramentas-avançadas-de-rede-opcional)
- [11. Verificação Final do Sistema](#11-verificação-final-do-sistema)
- [12. Problemas Comuns e Soluções](#12-problemas-comuns-e-soluções)
- [13. Manutenção Regular](#13-manutenção-regular)
- [14. Comandos Úteis](#14-comandos-úteis)
- [15. Aliases Úteis](#15-aliases-úteis)
- [16. Documentação e Ajuda](#16-documentação-e-ajuda)

---

## 1. Conectando ao Wi-Fi

Após finalizar a instalação básica do Arch Linux:

```bash
# Verificar se o NetworkManager está instalado e ativo
systemctl status NetworkManager

# Listar redes Wi-Fi disponíveis
nmcli device wifi list

# Conectar a uma rede Wi-Fi específica
nmcli device wifi connect "nome_da_rede" password "sua_senha"

# Testar conexão
ping -c 3 archlinux.org
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

# Atualizar base de dados
sudo pacman -Sy
```

### 3.2 Reflector Automático

Mantém os mirrors atualizados automaticamente para velocidades de download otimizadas.

```bash
# Instalar reflector
sudo pacman -S reflector

# Editar configuração
sudo nano /etc/xdg/reflector/reflector.conf

# Configuração recomendada para Brasil:
--save /etc/pacman.d/mirrorlist
--country Brazil
--protocol https
--latest 10
--sort rate

# Configurar atualização automática semanal
sudo systemctl enable reflector.timer
sudo systemctl start reflector.timer

# Verificar status
sudo systemctl status reflector.timer
```

### 3.3 Otimização de Memória Virtual (Zswap)
O Zswap é uma tecnologia que comprime dados na RAM antes de enviá-los ao disco, combinando velocidade com suporte à hibernação.

#### Passo 1: Verificar/Criar Swap

Você precisa de swap em disco (partição ou arquivo) para usar Zswap e hibernação.

**Se já tem partição swap (criada na instalação):**
```bash
# Verificar se já existe
swapon --show
# Se mostrar algo, você já tem swap configurado. Pule para o Passo 2.
```

**Se não tem swap, criar swapfile:**
```bash
# Criar swapfile (tamanho = sua RAM, para hibernação)
# Exemplo para 16GB de RAM:
sudo dd if=/dev/zero of=/swapfile bs=1M count=16384 status=progress
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Tornar permanente
echo '/swapfile none swap defaults 0 0' | sudo tee -a /etc/fstab

# Verificar
swapon --show
free -h
```

#### Passo 2: Habilitar Zswap
```bash
# Editar GRUB
sudo nano /etc/default/grub

# Na linha GRUB_CMDLINE_LINUX_DEFAULT, adicionar:
# zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25

# Exemplo:
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25"

# Regenerar GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### Passo 3: Ajustar Swappiness
```bash
# Reduz uso de swap em disco (prioriza RAM e cache Zswap)
echo "vm.swappiness=10" | sudo tee /etc/sysctl.d/99-swappiness.conf
sudo sysctl vm.swappiness=10
```

#### Passo 4: Reiniciar e Verificar
```bash
sudo reboot

# Após reiniciar:
# Verificar Zswap ativo
cat /sys/module/zswap/parameters/enabled  # Y
cat /sys/module/zswap/parameters/compressor  # zstd

# Verificar swap
swapon --show
free -h
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

### 3.5 Gestão de Energia (Laptops)

**Apenas para laptops:**

#### Opção A: TLP (recomendado para a maioria)
```bash
# Instalar TLP
sudo pacman -S tlp tlp-rdw

# Habilitar e iniciar
sudo systemctl enable tlp.service
sudo systemctl start tlp.service

# Verificar status (opcional)
sudo tlp-stat -s

# Mascarar serviços conflitantes (importante!)
sudo systemctl mask systemd-rfkill.service systemd-rfkill.socket
```

#### Opção B: power-profiles-daemon (se usa GNOME/KDE)
```bash
# Já vem instalado com GNOME/KDE por padrão
sudo systemctl enable power-profiles-daemon
sudo systemctl start power-profiles-daemon
```

**IMPORTANTE:** Não use TLP e power-profiles-daemon simultaneamente - causam conflitos!

---

## 4. Instalação de Pacotes Base Universais

### 4.1 Rede e Bluetooth

```bash
# Ferramentas essenciais de rede
sudo pacman -S wpa_supplicant

# Bluetooth (essencial para todos os ambientes)
sudo pacman -S bluez bluez-utils

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
# SOF Firmware (necessário para laptops/PCs modernos)
sudo pacman -S sof-firmware

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

# Configurar módulos do kernel para Wayland/Hyprland
sudo nano /etc/modprobe.d/nvidia.conf
# Adicionar esta linha:
options nvidia_drm modeset=1 fbdev=1

# Regenerar initramfs
sudo mkinitcpio -P

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
# DBUS - Message bus (ESSENCIAL para comunicação entre processos)
sudo pacman -S dbus
sudo systemctl enable dbus

# FUSE - Sistemas de arquivos em userspace (AppImage, SSHFS, etc)
sudo pacman -S fuse2 fuse3

# Monitoramento de recursos
sudo pacman -S htop btop

# Monitor de GPU (se tiver NVIDIA)
sudo pacman -S nvtop

# Informações de hardware
sudo pacman -S lshw dmidecode

# Sensores de hardware (temperatura, ventoinhas)
sudo pacman -S lm_sensors
sudo sensors-detect  # Detectar sensores (aceite os padrões)
sensors              # Ver temperaturas

# ACPI - Gerenciamento de energia (essencial para laptops)
sudo pacman -S acpi acpid
# Se laptop, habilitar:
sudo systemctl enable acpid

# Firewall
sudo pacman -S ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo ufw status verbose
```

### 4.6 Utilitários do Sistema

```bash
# Ferramentas de sistema de arquivos
sudo pacman -S e2fsprogs dosfstools

# Utilitários básicos de rede e download
sudo pacman -S git wget curl rsync

# Utilitários de terminal
sudo pacman -S tree dialog bash-completion

# Após instalar bash-completion, recarregar shell
source /etc/profile.d/bash_completion.sh
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

# Ferramentas de diagnóstico de rede
sudo pacman -S net-tools    # ifconfig, netstat (legado mas útil)
sudo pacman -S bind         # dig, nslookup (DNS)
sudo pacman -S mtr          # traceroute + ping melhorado
sudo pacman -S iw           # Configuração Wi-Fi avançada
sudo pacman -S ethtool      # Informações de interfaces de rede

# Ver informações do hardware
inxi -Fxz           # Informações completas
lspci               # Dispositivos PCI
lsusb               # Dispositivos USB
lsblk               # Discos e partições

# Exemplos de uso das ferramentas de rede:
# dig google.com              # Testar DNS
# mtr google.com              # Traceroute interativo
# sudo ethtool eth0           # Info da interface ethernet
# iw dev wlan0 scan           # Escanear redes Wi-Fi
```

### 4.9 Codecs de Mídia e Fontes

```bash
# Codecs de áudio e vídeo
sudo pacman -S ffmpeg gst-libav gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-plugins-base gstreamer

# Fontes essenciais
sudo pacman -S noto-fonts noto-fonts-emoji ttf-dejavu ttf-liberation ttf-font-awesome ttf-roboto

# Fontes para terminais e código (Nerd Fonts)
sudo pacman -S ttf-jetbrains-mono-nerd ttf-firacode-nerd

# Fontes adicionais (opcional)
sudo pacman -S ttf-ubuntu-font-family ttf-opensans
```

### 4.10 Sistema de Autenticação (Polkit)

```bash
# Polkit - necessário para autenticação gráfica em TODOS os ambientes
sudo pacman -S polkit
```

### 4.11 Diretórios e Utilitários XDG

```bash
# Criar e gerenciar pastas padrão (Downloads, Documents, Pictures, etc)
sudo pacman -S xdg-user-dirs
xdg-user-dirs-update

# Utilitários XDG (abrir arquivos com aplicação padrão, etc)
sudo pacman -S xdg-utils
```

### 4.12 Sistema de Arquivos Virtual (GVFS)

```bash
# GVFS - essencial para gerenciadores de arquivos, acesso a dispositivos, redes, etc
sudo pacman -S gvfs gvfs-mtp gvfs-gphoto2 gvfs-afc gvfs-nfs gvfs-smb

# Suporte a thumbnails/previews de arquivos (imagens, vídeos, PDFs)
sudo pacman -S tumbler ffmpegthumbnailer poppler-glib libgsf
```

### 4.13 Gerenciamento de Discos e Partições

```bash
# Montagem automática de dispositivos USB/externos
sudo pacman -S udisks2

# Suporte completo a NTFS (leitura/escrita em partições Windows)
sudo pacman -S ntfs-3g

# Suporte a exFAT (pendrives formatados em exFAT)
sudo pacman -S exfatprogs

# Ferramenta gráfica de particionamento
sudo pacman -S gparted
```

### 4.14 Sistema de Notificações

```bash
# Biblioteca de notificações (base necessária para todos os ambientes)
sudo pacman -S libnotify
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

# Remover dependências órfãs
yay -Yc
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
sudo pacman -S discord libreoffice-fresh

# OBS Studio (streaming/gravação)
sudo pacman -S obs-studio

# Terminal file manager (opcional, mas muito útil)
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

## 9. Otimizações para Gaming (Opcional)

Se você pretende jogar no Linux:

```bash
# Kernel otimizado para gaming (melhor latência e performance)
yay -S linux-zen linux-zen-headers

# Após instalar, regenerar GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg

# Gamemode - otimização automática ao jogar
sudo pacman -S gamemode lib32-gamemode

# MangoHud - overlay de performance (FPS, temperatura, etc)
sudo pacman -S mangohud lib32-mangohud

# Steam
sudo pacman -S steam

# Lutris - gerenciador de jogos para Windows e emuladores
sudo pacman -S lutris wine-staging

# ProtonUp-Qt - gerenciar versões do Proton
flatpak install flathub net.davidotek.pupgui2
```

**Uso do Gamemode:**
```bash
# Iniciar jogo com gamemode
gamemoderun ./jogo

# Para Steam: adicione nas opções de inicialização do jogo
gamemoderun %command%
```

---

## 10. Ferramentas Avançadas de Rede (Opcional)

**Apenas instale se você precisa de diagnóstico avançado ou desenvolvimento:**

```bash
# Captura e análise de pacotes (troubleshooting avançado)
sudo pacman -S tcpdump wireshark-qt

# Ferramentas adicionais de rede
sudo pacman -S nmap          # Scanner de portas/rede
sudo pacman -S traceroute    # Rastreamento de rotas
sudo pacman -S iperf3        # Teste de largura de banda

# Se usar Wireshark, adicionar usuário ao grupo
sudo usermod -aG wireshark $USER
# Relogar para aplicar
```

---

## 11. Verificação Final do Sistema

Após completar a instalação, execute estas verificações:

```bash
# Verificar serviços com falha
systemctl --failed

# Ver erros do último boot
journalctl -p 3 -xb

# Verificar status do áudio
systemctl --user status pipewire
systemctl --user status pipewire-pulse

# Testar áudio
speaker-test -t wav -c 2

# Verificar driver de vídeo
glxinfo | grep "OpenGL renderer"
vulkaninfo | grep "deviceName"

# Verificar se NVIDIA está carregada (se aplicável)
lsmod | grep nvidia

# Verificar temperatura do sistema
sensors

# Verificar zram (se configurado)
zramctl
swapon --show

# Ver informações completas
inxi -Fxz
neofetch

# Verificar modo de boot
bootctl status

# Verificar entradas de boot
efibootmgr -v
```

---

## 12. Problemas Comuns e Soluções

### 12.1 Áudio não funciona

```bash
# Verificar se PipeWire está rodando
systemctl --user status pipewire
systemctl --user status pipewire-pulse

# Reiniciar serviços de áudio
systemctl --user restart pipewire pipewire-pulse wireplumber

# Verificar dispositivos de áudio
pactl list sinks

# Testar áudio
speaker-test -t wav -c 2
```

### 12.2 Bluetooth não conecta

```bash
# Verificar status
systemctl status bluetooth

# Reiniciar bluetooth
sudo systemctl restart bluetooth

# Conectar via CLI
bluetoothctl
# Dentro do bluetoothctl:
power on
scan on
# Aguarde aparecer o dispositivo
pair MAC_ADDRESS
connect MAC_ADDRESS
trust MAC_ADDRESS
```

### 12.3 Performance baixa (NVIDIA)

```bash
# Verificar se driver está carregado
lsmod | grep nvidia
nvidia-smi

# Verificar se modeset está habilitado
cat /sys/module/nvidia_drm/parameters/modeset
# Deve retornar: Y

# Forçar uso da GPU dedicada para um programa específico
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia nome_do_programa

# Adicionar ao .bashrc para sempre usar GPU dedicada
echo 'export __NV_PRIME_RENDER_OFFLOAD=1' >> ~/.bashrc
echo 'export __GLX_VENDOR_LIBRARY_NAME=nvidia' >> ~/.bashrc
```

### 12.4 Tela preta após atualização do kernel (NVIDIA)

```bash
# Boot em modo de recuperação ou pelo pendrive
# Montar sistema
mount /dev/sdaX /mnt  # Sua partição root
arch-chroot /mnt

# Reinstalar drivers NVIDIA
sudo pacman -S nvidia nvidia-utils

# Regenerar initramfs
sudo mkinitcpio -P

# Regenerar GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg

exit
reboot
```

### 12.5 Sistema lento após instalar zram

```bash
# Verificar configuração atual
zramctl

# Se zram estiver muito grande, reduzir
sudo nano /etc/systemd/zram-generator.conf.d/zram.conf

# Mudar para 1/4 da RAM
[zram0]
zram-size = ram / 4

# Reiniciar
sudo reboot
```

---

## 13. Manutenção Regular

```bash
# Atualizar sistema (incluindo AUR)
yay -Syu

# Limpar cache de pacotes antigos (mantém últimas 3 versões)
yay -Sc

# Limpar cache completamente (libera mais espaço)
yay -Scc

# Remover pacotes órfãos (não usados por nada)
yay -Yc

# Ver pacotes instalados explicitamente
pacman -Qe

# Ver pacotes maiores instalados
expac -H M '%m\t%n' | sort -h | tail -20

# Ver ocupação de disco
df -h
du -sh ~/.cache/
du -sh /var/cache/pacman/pkg/

# Limpar cache do yay/paru
yay -Sc
rm -rf ~/.cache/yay

# Verificar arquivos .pacnew (configurações novas)
sudo find /etc -name "*.pacnew"

# Verificar logs antigos
journalctl --disk-usage
sudo journalctl --vacuum-time=2weeks
```

---

## 14. Comandos Úteis

```bash
# Buscar arquivo de configuração
sudo find /etc -name "arquivo.conf"

# Ver logs em tempo real
journalctl -f

# Ver logs de um serviço específico
journalctl -u nome_do_servico

# Listar serviços ativos
systemctl list-units --type=service --state=running

# Reiniciar serviço
sudo systemctl restart nome_do_servico

# Ver portas abertas
sudo ss -tulpn

# Ver processos que mais usam CPU
ps aux --sort=-%cpu | head -10

# Ver processos que mais usam RAM
ps aux --sort=-%mem | head -10

# Testar velocidade de internet
speedtest-cli  # Instale com: sudo pacman -S speedtest-cli

# Ver informações de hardware
sudo dmidecode -t memory  # RAM
sudo dmidecode -t processor  # CPU
sudo dmidecode -t baseboard  # Placa-mãe
```

---

## 15. Documentação e Ajuda

- **Arch Wiki**: https://wiki.archlinux.org/
- **Fórum oficial**: https://bbs.archlinux.org/
- **Manual do pacman**: `man pacman`
- **Manual de qualquer comando**: `man nome_do_comando`
- **Pesquisar no wiki do Arch**: Instale `arch-wiki-docs` e use `wiki-search termo`
- **Ajuda inline**: `comando --help` ou `comando -h`

---

**Importante**: 

- Mantenha sempre um **backup** das suas configurações importantes
- **Documente** as modificações que fizer no sistema para facilitar futuras reinstalações ou troubleshooting
- Antes de atualizar, verifique o site do Arch Linux por **breaking changes**
- **Não atualize às cegas** - leia os pacotes que serão atualizados

**Próximo passo**: Escolha e instale seu ambiente desktop/window manager preferido seguindo os guias específicos listados na seção 8.
