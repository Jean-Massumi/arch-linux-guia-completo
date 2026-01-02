# Guia Essencial de Pós-Instalação do Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **Configurações essenciais e universais após instalação base**

---

## Pré-requisito

Este guia assume que você completou a [Instalação Base do Arch Linux](./ARCH_BASE_INSTALL.md).

---

## Índice

- [1. Primeiros Passos](#1-primeiros-passos)
- [2. Otimizações do Sistema](#2-otimizações-do-sistema)
- [3. Pacotes Base Universais](#3-pacotes-base-universais)
- [4. Drivers Essenciais](#4-drivers-essenciais)
- [5. Instalação do YAY (AUR Helper)](#5-instalação-do-yay-aur-helper)
- [6. Segurança Básica](#6-segurança-básica)
- [7. Backup do Sistema](#7-backup-do-sistema)
- [8. Verificação Final](#8-verificação-final)
- [9. Ambientes Desktop e Window Managers](#9-ambientes-desktop-e-window-managers)
- [Comandos Úteis](#comandos-úteis)

---

## 1. Primeiros Passos

### 1.1 Conectar ao Wi-Fi (se necessário)

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

### 1.2 Atualizar Sistema

```bash
# Atualizar todos os pacotes do sistema
sudo pacman -Syu
```

### 1.3 Verificar e Configurar Data/Hora
```bash
# Verificar configuração atual
timedatectl

# Se estiver errado, listar fusos disponíveis:
timedatectl list-timezones | grep America/

# Corrigir fuso horário (formato: Continente/Cidade)
sudo timedatectl set-timezone America/Sao_Paulo

# Habilitar sincronização automática (NTP)
sudo timedatectl set-ntp true

# Verificar novamente
timedatectl
```

---

## 2. Otimizações do Sistema

### 2.1 Configuração do Pacman

```bash
sudo nano /etc/pacman.conf
```

**Descomente/adicione:**
```ini
Color                    # Habilita cores na saída
ParallelDownloads = 10   # Permite 10 downloads simultâneos
VerbosePkgLists          # Lista detalhada de pacotes
ILoveCandy               # Adicione isso

# Para suporte 32-bit (Steam, Wine, jogos)
[multilib]
Include = /etc/pacman.d/mirrorlist
```

**Aplicar:**
```bash
sudo pacman -Sy
```

### 2.2 Reflector Automático

Mantém os mirrors atualizados automaticamente para velocidades de download otimizadas.

```bash
# Instalar
sudo pacman -S reflector

# Configurar
sudo nano /etc/xdg/reflector/reflector.conf
```

**Configuração recomendada:**
```
--save /etc/pacman.d/mirrorlist
--country Brazil
--protocol https
--latest 10
--sort rate
```

**Habilitar atualização automática:**
```bash
sudo systemctl enable reflector.timer
sudo systemctl start reflector.timer
```

### 2.3 Otimização de Swap (Opcional)
O swap melhora a performance quando a RAM está cheia. Você já criou swap na instalação base.

#### Ajustar Swappiness
```bash
# Controla quando o sistema usa swap
sudo nano /etc/sysctl.d/99-swappiness.conf

# Adicionar (valor recomendado por RAM):
vm.swappiness=10

# Valores recomendados:
# - 8GB ou menos: swappiness=10-20
# - 16GB ou mais: swappiness=5-10

# Aplicar
sudo sysctl --system
```

#### Zswap (Opcional - Avançado)

Comprime dados antes de enviar ao swap, economizando RAM.
```bash
sudo nano /etc/default/grub

# Na linha GRUB_CMDLINE_LINUX_DEFAULT, adicionar os parâmetros do Zswap.
# Valores recomendados por quantidade de RAM:
# - 4-8GB: max_pool_percent=20-25
# - 16GB: max_pool_percent=15-20
# - 32GB+: max_pool_percent=10

# Exemplo (ajuste conforme sua RAM):
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25 zswap.zpool=z3fold"

# Compressor: zstd (melhor compressão) ou lz4 (mais rápido)
# Escolha zstd para máxima economia de RAM ou lz4 para velocidade.

# Regenerar GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg

# Reiniciar para aplicar
sudo reboot
```

### 2.4 TRIM para SSDs

**Apenas se você tem SSD:**
```bash
# Verificar suporte
sudo lsblk --discard

# Se DISC-GRAN e DISC-MAX não forem zero:
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
```

---

## 3. Pacotes Base Universais

### 3.1 Ferramentas de Sistema

```bash
# Compilação (necessário para AUR)
sudo pacman -S git

# Compressão e arquivos
sudo pacman -S zip unzip p7zip unrar tar

# Rede e download
sudo pacman -S wget curl rsync

# Monitoramento
sudo pacman -S htop btop

# Informações do sistema
sudo pacman -S fastfetch inxi

# Sensores de hardware
sudo pacman -S lm_sensors

# Detectar sensores (responder "yes" para tudo)
sudo sensors-detect
```

### 3.2 Rede e Conectividade

```bash
# Bluetooth
sudo pacman -S bluez bluez-utils
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service

# Ferramentas de diagnóstico
sudo pacman -S bind mtr
```

### 3.3 Sistema de Arquivos

```bash
# Ferramentas básicas
sudo pacman -S e2fsprogs dosfstools

# FUSE (AppImage, SSHFS)
sudo pacman -S fuse2 fuse3

# Suporte USB
sudo pacman -S exfatprogs

# Montagem automática
sudo pacman -S udisks2

# Diretórios padrão
sudo pacman -S xdg-user-dirs xdg-utils
xdg-user-dirs-update
```

### 3.4 Multimídia Base

```bash
# Codecs essenciais
sudo pacman -S ffmpeg gst-libav gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-plugins-base gstreamer x264 x265 libde265 libva-mesa-driver

# Fontes essenciais
sudo pacman -S noto-fonts noto-fonts-emoji ttf-dejavu ttf-liberation ttf-font-awesome ttf-roboto

# Fontes para terminais e código (Nerd Fonts) - Opcional
sudo pacman -S ttf-jetbrains-mono-nerd ttf-firacode-nerd

# Fontes específicas CJK (Japonês, Chines, Koreano, etc) - Opcional
sudo pacman -S noto-fonts-cjk
```

### 3.5 Sistema Base

```bash
# Polkit (autenticação)
sudo pacman -S polkit

# Bash completion (autocomplete no terminal)
sudo pacman -S bash-completion

# D-Bus (comunicação entre aplicações - CRÍTICO)
sudo pacman -S dbus
sudo systemctl enable dbus.service
```

---

## 4. Drivers Essenciais

### 4.1 Áudio (PipeWire)
```bash
# Sistema de áudio moderno (substitui PulseAudio)
sudo pacman -S pipewire pipewire-alsa pipewire-pulse pipewire-jack pipewire-audio wireplumber

# Habilitar para seu usuário
systemctl --user enable pipewire pipewire-pulse wireplumber
systemctl --user start pipewire pipewire-pulse wireplumber

# Verificar
pactl info
```

### 4.2 Impressoras (CUPS)
```bash
# Backend de impressão
sudo pacman -S cups cups-pdf

# Habilitar serviço
sudo systemctl enable cups.socket
sudo systemctl start cups.socket

# Drivers comuns (opcional)
sudo pacman -S gutenprint foomatic-db foomatic-db-engine
```

**Nota**: Ferramentas de configuração gráfica (system-config-printer, print-manager) 
serão instaladas junto com o ambiente desktop.

---

## 5. Instalação do YAY (AUR Helper)

O YAY permite instalar pacotes do AUR (Arch User Repository), que contém milhares de aplicações adicionais.

```bash
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

---

## 6. Segurança Básica

### 6.1 Firewall

```bash
# Instalar UFW
sudo pacman -S ufw

# Instalar GUFW - interface gráfica para UFW
sudo pacman -S gufw

# Ativar o firewall (responder 'y')
sudo ufw enable
# Saída: Firewall is active and enabled on system startup

# Habilitar serviço para iniciar automaticamente
sudo systemctl enable ufw

# Verificar status
sudo ufw status verbose
# Deve mostrar: Status: active
```

---

## 7. Backup do Sistema

### Timeshift (Recomendado para todos)
```bash
# Instalar
sudo pacman -S timeshift

# Configurar (abre interface gráfica)
sudo timeshift-gtk

# Ou via terminal:
sudo timeshift --create --comments "Primeiro snapshot" --tags D
```

**Configuração inicial no Timeshift:**
1. Escolha o tipo: **RSYNC** (ext4) ou **BTRFS** (se usar Btrfs)
2. Selecione o disco de destino
3. Configure a frequência (diária, semanal, mensal)
4. **Faça backup apenas de `/` (raiz)** - não inclua `/home` para economizar espaço

**Restaurar sistema:**
```bash
sudo timeshift --restore
# Ou pela interface gráfica
```

**Limpeza de snapshots:**
- **Automática**: Timeshift deleta snapshots antigos automaticamente conforme a política configurada (ex: manter apenas 5 diários, 3 semanais)
- **Manual**: Você pode deletar snapshots específicos pela interface ou com `sudo timeshift --delete --snapshot 'nome'`

---

## Comandos Úteis

### Gerenciamento de Pacotes

```bash
# Buscar pacote
pacman -Ss nome          # Repositórios oficiais
yay -Ss nome             # Incluindo AUR

# Informações sobre pacote
pacman -Si nome

# Instalar
sudo pacman -S nome      # Oficial
yay -S nome              # AUR

# Remover (com dependências)
sudo pacman -Rns nome

# Atualizar tudo
yay -Syu

# Limpar cache
yay -Sc

# Remover órfãos
yay -Yc

# Listar arquivos de um pacote
pacman -Ql nome_do_pacote

# Descobrir qual pacote possui um arquivo
pacman -Qo /caminho/do/arquivo
```
