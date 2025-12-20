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
- [4. Instalação do YAY (AUR Helper)](#4-instalação-do-yay-aur-helper)
- [5. Segurança Básica](#5-segurança-básica)
- [6. Backup do Sistema](#6-backup-do-sistema)
- [7. Verificação Final](#7-verificação-final)
- [8. Ambientes Desktop e Window Managers](#8-ambientes-desktop-e-window-managers)
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

### 2.3 Otimização de Memória Virtual (Zswap)
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
# Criar swapfile - ajuste o tamanho conforme sua necessidade:
# - Hibernação: tamanho = sua RAM
# - Sem hibernação: 4-8GB é suficiente
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

# Na linha GRUB_CMDLINE_LINUX_DEFAULT, adicionar os parâmetros do Zswap.
# Valores recomendados por quantidade de RAM:
# - 4-8GB: max_pool_percent=20-25
# - 16GB: max_pool_percent=15-20
# - 32GB+: max_pool_percent=10

# Exemplo (ajuste conforme sua RAM):
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25"

# Compressor: zstd (melhor compressão) ou lz4 (mais rápido)
# Escolha zstd para máxima economia de RAM ou lz4 para velocidade.

# Regenerar GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### Passo 3: Ajustar Swappiness
```bash
# Criar arquivo de configuração
sudo nano /etc/sysctl.d/99-swappiness.conf

Adicionar (escolha o valor conforme sua RAM):
vm.swappiness=10
vm.vfs_cache_pressure=50

# Valores recomendados:
# - 4-8GB RAM: swappiness=10-20
# - 16GB RAM: swappiness=5-10
# - 32GB+ RAM: swappiness=1-5

# Aplicar imediatamente
sudo sysctl --system
```

#### Passo 4: Reiniciar e Verificar
```bash
sudo reboot

# Após reiniciar, verificar configuração:
cat /sys/module/zswap/parameters/enabled  # deve mostrar Y
cat /sys/module/zswap/parameters/compressor  # deve mostrar zstd ou lz4
cat /proc/sys/vm/swappiness  # deve mostrar o valor configurado
swapon --show
free -h
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
sudo pacman -S base-devel git

# Compressão e arquivos
sudo pacman -S zip unzip p7zip unrar tar

# Rede e download
sudo pacman -S wget curl rsync

# Monitoramento
sudo pacman -S htop btop

# Informações do sistema
sudo pacman -S neofetch inxi

# Sensores de hardware
sudo pacman -S lm_sensors
sudo sensors-detect  # Aceitar padrões
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

# Suporte Windows/USB
sudo pacman -S ntfs-3g exfatprogs

# Montagem automática
sudo pacman -S udisks2

# GVFS (essencial para gerenciadores de arquivo)
sudo pacman -S gvfs gvfs-mtp gvfs-gphoto2 gvfs-afc gvfs-smb

# Diretórios padrão
sudo pacman -S xdg-user-dirs xdg-utils
xdg-user-dirs-update
```

### 3.4 Multimídia Base

```bash
# Codecs essenciais
sudo pacman -S ffmpeg

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
source /etc/profile.d/bash_completion.sh
```

---

## 4. Instalação do YAY (AUR Helper)

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

## 5. Segurança Básica

### 5.1 Firewall

```bash
# Instalar UFW
sudo pacman -S ufw

# Configurar
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Habilitar
sudo ufw enable

# Verificar
sudo ufw status verbose
```

---

## 6. Backup do Sistema

### 6.1 Timeshift

```bash
# Instalar
yay -S timeshift

# Executar
sudo timeshift-gtk

# Configuração recomendada:
# - Tipo: RSYNC
# - Agendamento: Semanal
# - Incluir: Apenas partição raiz (/)
```

### 6.2 Criar Primeiro Snapshot

```bash
sudo timeshift --create --comments "Instalação base completa" --tags D
```

---

## 7. Verificação Final

```bash
# Verificar serviços com falha
systemctl --failed

# Ver erros do último boot
journalctl -p 3 -xb

# Verificar temperatura
sensors

# Verificar swap
swapon --show
free -h

# Informações completas do sistema
inxi -Fxz
neofetch

# Verificar entradas de boot
efibootmgr -v
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





