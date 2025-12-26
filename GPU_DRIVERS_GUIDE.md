# Guia de Drivers de GPU - Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **Instale os drivers de GPU corretos ANTES do ambiente gráfico**

---

## Índice

- [1. Identificar Sua GPU](#1-identificar-sua-gpu)
- [2. Intel Integrado](#2-intel-integrado)
- [3. AMD](#3-amd)
- [4. NVIDIA](#4-nvidia)
- [5. Sistema Híbrido (Intel + NVIDIA)](#5-sistema-híbrido-intel--nvidia)
- [6. Verificação da Instalação](#6-verificação-da-instalação)
- [7. Problemas Comuns](#7-problemas-comuns)
- [Próximos Passos](#próximos-passos)
- [Documentação Oficial](#documentação-oficial)

---

## 1. Identificar Sua GPU

**ANTES de instalar drivers, identifique qual GPU você tem:**

### 1.1 Comando Básico

```bash
# Ver fabricante e modelo da GPU
lspci | grep -E "VGA|3D"
```

### 1.2 Exemplos de Saída

**Intel Integrado:**
```
00:02.0 VGA compatible controller: Intel Corporation Device 9a49
00:02.0 VGA compatible controller: Intel Corporation UHD Graphics 620
00:02.0 VGA compatible controller: Intel Corporation Iris Xe Graphics
```

**AMD:**
```
01:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Navi 23
01:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Renoir
01:00.0 VGA compatible controller: AMD/ATI Radeon RX 6800 XT
```

**NVIDIA:**
```
01:00.0 VGA compatible controller: NVIDIA Corporation GA106 [GeForce RTX 3060]
01:00.0 VGA compatible controller: NVIDIA Corporation TU117M [GeForce GTX 1650 Mobile]
01:00.0 3D controller: NVIDIA Corporation GA107M [GeForce RTX 3050 Mobile]
```

**Sistema Híbrido (Intel + NVIDIA):**
```
00:02.0 VGA compatible controller: Intel Corporation Device 9a49
01:00.0 3D controller: NVIDIA Corporation GA107M [GeForce RTX 3050 Mobile]
```

### 1.3 Informações Detalhadas

```bash
# Ver mais detalhes da GPU
lspci -v | grep -A 10 "VGA"

# Informações completas do sistema (se instalou inxi)
inxi -G
```

---

## 2. Intel Integrado

**Para GPUs Intel (HD Graphics, UHD Graphics, Iris, Iris Xe):**

### 2.1 Instalação Básica
```bash
# Driver OpenGL (obrigatório)
sudo pacman -S mesa

# Driver Vulkan (para jogos modernos e aplicações 3D)
sudo pacman -S vulkan-intel
```

### 2.2 Aceleração de Vídeo

**Reduz uso de CPU ao assistir vídeos (YouTube, Netflix, etc).**

**Escolha conforme sua GPU:**

**GPUs Broadwell (2014) e mais recentes:**
```bash
sudo pacman -S intel-media-driver
```

**GPUs Haswell (2013) e mais antigas:**
```bash
sudo pacman -S libva-intel-driver
```

> **Na dúvida?** Instale `intel-media-driver` - funciona para a maioria das GPUs modernas.

### 2.3 Suporte 32-bit (Opcional)

**Necessário APENAS para:**
- Jogos antigos 32-bit
- Steam (alguns jogos ainda são 32-bit)
- Wine/Proton com aplicações 32-bit
```bash
sudo pacman -S lib32-mesa lib32-vulkan-intel
```

> **Se você não joga ou usa apenas aplicações 64-bit, pode pular esta etapa.**

---

## 3. AMD

**Para GPUs AMD (Radeon, RX 5000/6000/7000 Series, APUs Ryzen com gráficos integrados):**


### 3.1 Drivers Essenciais
```bash
# Driver OpenGL (obrigatório)
sudo pacman -S mesa

# Driver Vulkan (jogos modernos e performance máxima)
sudo pacman -S vulkan-radeon
```

> **Por que Vulkan?** A maioria dos jogos modernos usa Vulkan para melhor performance. Steam/Proton também requer Vulkan.

### 3.2 Aceleração de Vídeo (Opcional)

**Reduz uso de CPU ao assistir vídeos (YouTube, Netflix, OBS):**
```bash
sudo pacman -S libva-mesa-driver
```

> **Pule se:** Não assiste vídeos ou não se importa com economia de energia.

### 3.3 Suporte 32-bit (Para Jogos)

**Steam, Proton, Wine e jogos 32-bit:**
```bash
sudo pacman -S lib32-mesa lib32-vulkan-radeon lib32-libva-mesa-driver
```

> **Pule se:** Não joga ou usa apenas aplicações 64-bit nativas.

---

## 4. NVIDIA

**Para GPUs NVIDIA dedicadas (GeForce, RTX, GTX):**

### 4.1 Passo 1: Escolha e Instale os Drivers

```bash
OPÇÃO 1: Se instalou APENAS o kernel linux (padrão):
sudo pacman -S nvidia-open nvidia-utils lib32-nvidia-utils nvidia-settings

OPÇÃO 2: Se instalou APENAS o kernel linux-lts:
sudo pacman -S nvidia-open-lts nvidia-utils lib32-nvidia-utils nvidia-settings

OPÇÃO 3: Se instalou 2+ kernels OU usa kernel customizado (zen, hardened, etc):
sudo pacman -S nvidia-open-dkms nvidia-utils lib32-nvidia-utils nvidia-settings
```

> **Por que DKMS?** Recompila o driver automaticamente para cada kernel quando atualizar. Essencial para múltiplos kernels.

### 4.2 Passo 2: Configurar Módulos do Kernel

```bash
# Criar arquivo de configuração
sudo nano /etc/modprobe.d/nvidia.conf
```

**Adicionar:**
```
options nvidia_drm modeset=1 fbdev=1
```

**Salvar e sair** (Ctrl+O, Enter, Ctrl+X)

### 4.3 Passo 3: Adicionar Módulos ao Initramfs

```bash
# Editar mkinitcpio
sudo nano /etc/mkinitcpio.conf
```

**Encontrar a linha MODULES e modificar para:**
```
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

**Salvar e sair**

### 4.4 Passo 4: Regenerar Initramfs

```bash
sudo mkinitcpio -P
```

### 4.5 Passo 5: Configurar GRUB (Para Wayland/Hyprland)

```bash
# Editar GRUB
sudo nano /etc/default/grub
```

**Encontrar a linha GRUB_CMDLINE_LINUX_DEFAULT e adicionar os parâmetros NVIDIA:**

**Exemplo - Se sua linha está assim:**
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

**Deve ficar assim:**
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia_drm.modeset=1 nvidia_drm.fbdev=1"
```

**IMPORTANTE: Se você já tem outros parâmetros (como Zswap), apenas adicione ao final:**

**(Zswap + NVIDIA):**
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25 zswap.zpool=z3fold nvidia_drm.modeset=1 nvidia_drm.fbdev=1"
```

> **Dica:** Todos os parâmetros ficam na mesma linha, separados por espaços. Não quebre a linha!

**Salvar e sair**

**Regenerar GRUB:**
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**Nota:** Se for usar Xorg (GNOME X11, KDE X11), este passo é opcional.

### 4.6 Passo 7: Reiniciar

```bash
sudo reboot
```

### 4.7 Verificação

**Após reiniciar:**

```bash
# Verificar se driver está carregado
nvidia-smi
# Deve mostrar informações da GPU

# Verificar módulos
lsmod | grep nvidia
# Deve listar: nvidia, nvidia_drm, nvidia_modeset, nvidia_uvm
```

**Pronto!** Sistema NVIDIA está configurado. Você pode prosseguir para instalar o ambiente desktop.

### 4.8 Drivers Open-Source (Nouveau)

**Alternativa aos drivers proprietários NVIDIA.**

**Quando usar:**
- Problemas com drivers proprietários
- Precisa de estabilidade máxima
- Compatibilidade com Wayland sem configurações extras

**Quando NÃO usar:**
- Jogos e aplicações 3D (performance inferior)
- Precisa de todos os recursos da GPU
- Hardware muito recente

**Instalação:**
```bash
# Drivers Nouveau (open-source)
sudo pacman -S mesa lib32-mesa xf86-video-nouveau

# Aceleração de vídeo
sudo pacman -S libva-mesa-driver lib32-libva-mesa-driver
```

**Nota**: Se você instalou drivers proprietários NVIDIA antes, remova-os primeiro:
````bash
sudo pacman -Rns nvidia nvidia-utils lib32-nvidia-utils
sudo reboot
````

**Verificação:**
```bash
lsmod | grep nouveau
# Deve aparecer: nouveau

glxinfo | grep "OpenGL renderer"
# Saída: Nouveau...
```

**Limitações conhecidas:**
- Performance 50-70% inferior aos drivers proprietários
- Alguns recursos avançados não disponíveis
- Melhor compatibilidade com Wayland

---

## 5. Sistema Híbrido (Intel + NVIDIA)

**Para laptops com GPU integrada Intel + GPU dedicada NVIDIA (Tecnologia Optimus):**

### 5.1 Passo 1: Instalar Drivers da GPU Integrada

**Instale os drivers conforme sua GPU integrada:**

- **Se for Intel:** Siga as instruções da [Seção 2 - Intel Integrado](#2-intel-integrado)
- **Se for AMD:** Siga as instruções da [Seção 3 - AMD](#3-amd)

### 5.2 Passo 2: Instalar Drivers NVIDIA

**Siga as instruções da [Seção 4.1 - Escolha e Instale os Drivers](#41-passo-1-escolha-e-instale-os-drivers)**

> Escolha a opção correta conforme seu(s) kernel(s): `nvidia-open`, `nvidia-open-lts` ou `nvidia-open-dkms`.


### 5.3 Passo 3: Configurar Módulos do Kernel

```bash
# Criar arquivo de configuração
sudo nano /etc/modprobe.d/nvidia.conf
```

**Adicionar:**
```
options nvidia_drm modeset=1 fbdev=1
```

**Salvar e sair**

### 5.4 Passo 4: Adicionar ao Initramfs

```bash
sudo nano /etc/mkinitcpio.conf
```

**Modificar MODULES conforme sua GPU integrada:**

**Para Intel + NVIDIA:**
```
MODULES=(i915 nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

**Para AMD + NVIDIA:**
```
MODULES=(amdgpu nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

**Salvar e sair**

**Regenerar:**
```bash
sudo mkinitcpio -P
```

### 5.5 Passo 5: Configurar GRUB

```bash
sudo nano /etc/default/grub
```

**Modificar:** 

**Exemplo - Se sua linha está assim:**
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

**Deve ficar assim:**
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia_drm.modeset=1 nvidia_drm.fbdev=1"
```

**IMPORTANTE: Se você já tem outros parâmetros (como Zswap), apenas adicione ao final:**

**(Zswap + NVIDIA):**
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25 zswap.zpool=z3fold nvidia_drm.modeset=1 nvidia_drm.fbdev=1"
```

> **Dica:** Todos os parâmetros ficam na mesma linha, separados por espaços. Não quebre a linha!

**Salvar e sair**

**Regenerar:**
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### 5.6 Passo 6: Instalar nvidia-prime
```bash
sudo pacman -S nvidia-prime
```

> **O que é?** Fornece o comando `prime-run` para usar a GPU NVIDIA facilmente.

### 5.7 Passo 7: Reiniciar
```bash
sudo reboot
```

### 5.8 Usando as Duas GPUs

**Por padrão, o sistema usará a GPU integrada (economia de energia).**

**Para rodar aplicação na GPU NVIDIA:**
```bash
prime-run nome_do_programa
```

**Exemplos:**
```bash
prime-run firefox
prime-run steam
prime-run glxinfo | grep "OpenGL renderer"  # Verificar
```

**Para sempre usar NVIDIA (não recomendado - gasta muito mais bateria):**
```bash
echo 'export __NV_PRIME_RENDER_OFFLOAD=1' >> ~/.bashrc
echo 'export __GLX_VENDOR_LIBRARY_NAME=nvidia' >> ~/.bashrc
source ~/.bashrc
```

### 5.9 Verificação
```bash
# Verificar ambas GPUs detectadas
lspci | grep -E "VGA|3D"

# GPU padrão (integrada)
glxinfo | grep "OpenGL renderer"

# Testar NVIDIA
prime-run glxinfo | grep "OpenGL renderer"
```

> **Mais testes?** Veja [Seção 6 - Verificação Completa](#6-verificação-da-instalação)

---

## 6. Verificação da Instalação

### 6.1 Comandos Universais

**Funcionam para Intel, AMD e NVIDIA:**
```bash
# Verificar OpenGL
glxinfo | grep "OpenGL renderer"

# Verificar Vulkan (se instalou vulkan)
vulkaninfo | grep "deviceName"

# Ver módulos do kernel carregados
lsmod | grep -E "nvidia|amdgpu|i915"
```

### 6.2 Verificação Específica NVIDIA

```bash
# Informações da GPU NVIDIA
nvidia-smi

# Verificar módulos NVIDIA
lsmod | grep nvidia

# Deve listar:
# nvidia
# nvidia_drm
# nvidia_modeset
# nvidia_uvm
```

### 6.3 Teste Gráfico Simples

```bash
# Instalar ferramenta de teste (opcional)
sudo pacman -S mesa-demos

# Testar OpenGL
glxgears

# Ver FPS e informações
glxinfo | grep -i "opengl"
```

---

## 7. Problemas Comuns

<details>
<summary>GPU Não Detectada</summary>

<br>
    
```bash
# Reinstalar drivers
sudo pacman -S --overwrite '*' mesa  # Intel/AMD
sudo pacman -S --overwrite '*' nvidia  # NVIDIA

# Regenerar initramfs
sudo mkinitcpio -P

# Reiniciar
sudo reboot
```
</details>

<details>
<summary>NVIDIA: Tela Preta Após Boot</summary>

<br>
    
**Solução 1: Adicionar nomodeset temporariamente**
1. No menu GRUB, pressione `e` para editar
2. Adicione `nomodeset` no final da linha `linux`
3. Pressione `Ctrl+X` para boot

**Solução 2: Verificar configuração**
```bash
# Verificar módulos no mkinitcpio
sudo nano /etc/mkinitcpio.conf
# MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)

# Verificar configuração NVIDIA
sudo nano /etc/modprobe.d/nvidia.conf
# options nvidia_drm modeset=1 fbdev=1

# Regenerar tudo
sudo mkinitcpio -P
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot
```
</details>

<details>
<summary>NVIDIA: Performance Baixa</summary>

<br>
    
```bash
# Verificar se está usando GPU correta
glxinfo | grep "OpenGL renderer"
# Deve mostrar NVIDIA, não Intel ou software

# Forçar uso da NVIDIA
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxinfo | grep "OpenGL renderer"
```
</details>

<details>
<summary>AMD: Performance Baixa ou Travamentos</summary>

<br>
    
```bash
# Verificar módulo carregado
lsmod | grep amdgpu

# Se não aparecer, força carregar
sudo modprobe amdgpu

# Adicionar ao initramfs
sudo nano /etc/mkinitcpio.conf
# MODULES=(amdgpu)
sudo mkinitcpio -P
sudo reboot
```
</details>

<details>
<summary>Intel: Tearing (Imagem Rasgada)</summary>

<br>

```bash
# Criar configuração Intel
sudo nano /etc/X11/xorg.conf.d/20-intel.conf
```

**Adicionar:**
```
Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "TearFree" "true"
EndSection
```

**Salvar e reiniciar**
</details>

---

## Próximos Passos

**Instalar ambiente desktop/window manager:**
   - [Guia GNOME](./GNOME_COMPLETE_SETUP.md)
   - [Guia KDE Plasma](./KDE_COMPLETE_SETUP.md)
   - [Guia Hyprland](./HYPRLAND_COMPLETE_SETUP.md)

---

## Documentação Oficial

- **Arch Wiki - Intel Graphics**: https://wiki.archlinux.org/title/Intel_graphics
- **Arch Wiki - AMDGPU**: https://wiki.archlinux.org/title/AMDGPU
- **Arch Wiki - NVIDIA**: https://wiki.archlinux.org/title/NVIDIA
- **Arch Wiki - PRIME (Híbrido)**: https://wiki.archlinux.org/title/PRIME
- **Arch Wiki - Optimus**: https://wiki.archlinux.org/title/NVIDIA_Optimus
- **Hyprland + NVIDIA**: https://wiki.hypr.land/Nvidia/

---

**Importante:** Sempre consulte a Wiki do Arch antes de instalar drivers. A documentação é mantida atualizada pela comunidade!
