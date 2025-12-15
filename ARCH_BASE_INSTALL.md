# Guia de Instalação Dual-Boot: Arch Linux + Windows

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)

> **Dual-Boot**: Tenha Windows e Arch Linux no mesmo computador

> **Tempo estimado**: 2-3 horas  
> **Nível de dificuldade**: Avançado

> **IMPORTANTE**: Este guia assume que você JÁ tem Windows instalado e quer adicionar o Arch Linux. Se você não tem nenhum sistema instalado, siga primeiro o [Guia de Instalação Base do Arch Linux](./ARCH_BASE_INSTALL.md).

---

## Índice

- [Diferenças do Dual-Boot](#diferenças-do-dual-boot)
- [Pré-requisitos](#pré-requisitos)
- [1. Preparação do Windows](#1-preparação-do-windows)
- [2. Criar Espaço para o Arch Linux](#2-criar-espaço-para-o-arch-linux)
- [3. Desabilitar Fast Boot e Secure Boot](#3-desabilitar-fast-boot-e-secure-boot)
- [4. Boot pelo Pendrive do Arch Linux](#4-boot-pelo-pendrive-do-arch-linux)
- [5. Identificar Partições Existentes](#5-identificar-partições-existentes)
- [6. Particionamento para Dual-Boot](#6-particionamento-para-dual-boot)
- [7. Formatação e Montagem](#7-formatação-e-montagem)
- [8. Continuar com Instalação Base](#8-continuar-com-instalação-base)
- [9. Configuração do GRUB para Dual-Boot](#9-configuração-do-grub-para-dual-boot)
- [10. Solução de Problemas Dual-Boot](#10-solução-de-problemas-dual-boot)

---

## Diferenças do Dual-Boot

O processo de dual-boot difere da instalação padrão nos seguintes pontos:

**O que é DIFERENTE no dual-boot:**

1. **Partição EFI**: Você vai REUTILIZAR a partição EFI do Windows (não criar uma nova)
2. **Particionamento**: Você vai criar partições no espaço livre (não apagar tudo)
3. **GRUB**: Precisa detectar e adicionar o Windows ao menu de boot
4. **Cuidados especiais**: Fast Boot, Secure Boot e BitLocker precisam ser desabilitados

**O que é IGUAL à instalação padrão:**

- Configuração de teclado e internet
- Instalação do sistema base
- Configuração de idioma, fuso horário e usuários
- Instalação de drivers e NetworkManager
- Todo o processo pós-instalação

**Para tudo que não está listado neste guia de dual-boot, siga o [Guia de Instalação Base](./ARCH_BASE_INSTALL.md) normalmente.**

---

## Pré-requisitos

- **Windows 10 ou 11** já instalado e funcionando
- **Backup completo** de todos os dados importantes
- **Pendrive bootável** com Arch Linux ISO ([veja como criar](./BOOTABLE_USB_GUIDE.md))
- **Mínimo 60GB livres** no disco para o Arch Linux
- **Conexão com internet** durante a instalação
- **Desabilitar BitLocker** se estiver ativo no Windows

> **ATENÇÃO CRÍTICA**: Dual-boot envolve particionar discos. Sempre faça backup antes de começar!

---

## 1. Preparação do Windows

### 1.1 Fazer Backup Completo

Antes de começar, faça backup de:
- Documentos importantes
- Fotos e vídeos
- Configurações e programas
- Chaves de licença de software

**Ferramentas recomendadas**:
- Windows Backup interno
- Clonezilla (para imagem completa do disco)
- Backup manual para HD externo

### 1.2 Desabilitar BitLocker (se ativo)

```powershell
# Abrir PowerShell como Administrador
# Verificar se BitLocker está ativo
manage-bde -status

# Se estiver ativo, desabilitar (substitua C: pela unidade correta)
manage-bde -off C:
```

**Aguarde o processo de descriptografia completar antes de continuar.**

### 1.3 Desfragmentar o Disco (HDD apenas)

**Se você usa HDD (não SSD)**:

1. Abra "Desfragmentar e Otimizar Unidades"
2. Selecione a unidade C:
3. Clique em "Otimizar"
4. Aguarde completar

**IMPORTANTE**: Não desfragmente SSDs! Isso reduz sua vida útil.

---

## 2. Criar Espaço para o Arch Linux

### 2.1 Abrir Gerenciamento de Disco

1. Pressione `Win + X`
2. Selecione "Gerenciamento de Disco"

### 2.2 Reduzir Volume do Windows

1. Clique com botão direito na partição **C:** (Windows)
2. Selecione "Reduzir Volume"
3. Digite a quantidade de espaço a reduzir em MB
   - Para 60GB: digite `61440` MB
   - Para 100GB: digite `102400` MB
4. Clique em "Reduzir"

**O Windows criará um espaço "Não Alocado" - NÃO formate este espaço agora!**

---

## 3. Desabilitar Fast Boot e Secure Boot

### 3.1 Desabilitar Fast Boot no Windows

1. Abra "Painel de Controle"
2. Vá em "Opções de Energia"
3. Clique em "Escolher a função dos botões de energia"
4. Clique em "Alterar configurações não disponíveis no momento"
5. **Desmarque** "Ativar inicialização rápida"
6. Salve as alterações

### 3.2 Desabilitar Secure Boot na BIOS/UEFI

1. Reinicie o computador
2. Entre na BIOS/UEFI (geralmente F2, F10, DEL ou ESC)
3. Procure por "Secure Boot"
4. Mude para **"Disabled"** ou **"Other OS"**
5. Salve e saia (F10)

**Por quê?** Secure Boot pode impedir o boot do Arch Linux.

---

## 4. Boot pelo Pendrive do Arch Linux

1. Insira o pendrive bootável
2. Reinicie o computador
3. Entre no Boot Menu (geralmente F12, F8 ou ESC)
4. Selecione o pendrive
5. Escolha "Arch Linux install medium" no menu do GRUB

**A partir daqui, você está no ambiente de instalação do Arch Linux.**

Para configurar teclado e internet, siga as seções **1. Configuração Inicial** e **2. Conexão com Internet** do [Guia de Instalação Base](./ARCH_BASE_INSTALL.md).

---

## 5. Identificar Partições Existentes

### 5.1 Listar Discos e Partições

```bash
lsblk
fdisk -l
```

**Exemplo de saída típica de dual-boot:**

```
NAME        SIZE TYPE MOUNTPOINT
sda         500G disk
├─sda1      100M part    <- Partição de Recuperação do Windows
├─sda2      500M part    <- Partição EFI (COMPARTILHADA)
├─sda3      350G part    <- Windows C:
└─sda4      149G part    <- Espaço livre (onde instalaremos o Arch)
```

**IMPORTANTE**: Identifique estas partições:

| Partição | Tamanho Típico | Tipo | O Que É |
|----------|----------------|------|---------|
| sda1 | 100-500MB | Recovery | Recuperação do Windows (NÃO MEXA) |
| sda2 | 100-500MB | EFI System | Partição EFI (USAR, NÃO FORMATAR) |
| sda3 | Variável | Microsoft basic data | Windows C: (NÃO MEXA) |
| sda4+ | Não alocado | - | Espaço livre para Arch |

### 5.2 Verificar Partição EFI e Seu Tamanho

```bash
# Verificar tamanho da partição EFI
lsblk /dev/sda2

# Montar temporariamente a partição EFI para verificar
mkdir -p /mnt/efi_test
mount /dev/sda2 /mnt/efi_test
ls /mnt/efi_test/EFI

# Você deve ver uma pasta "Microsoft" - isso confirma que é a partição EFI do Windows
umount /mnt/efi_test
```

**IMPORTANTE - Partição EFI pequena (100MB):**

O Windows frequentemente cria uma partição EFI de apenas 100MB, que é insuficiente para múltiplos kernels do Linux. Se sua partição EFI for menor que 500MB, você tem algumas opções:

1. **Aumentar a partição EFI** (avançado, requer redimensionar partições do Windows)
2. **Montar EFI em /efi** ao invés de /boot/efi e usar bootloader com drivers de sistema de arquivos
3. **Usar compressão alta** nos initramfs (mais lento, mas economiza espaço)
4. **Manter apenas um kernel** (remover kernels antigos regularmente)

Para a maioria dos usuários, a opção 3 (compressão alta) é a mais simples. Veja a seção de solução de problemas para detalhes.

---

## 6. Particionamento para Dual-Boot

### 6.1 Estrutura de Partições para Dual-Boot

**ATENÇÃO**: Você vai USAR a partição EFI existente do Windows, NÃO criar uma nova!

| Partição | Tamanho | Tipo | Uso |
|----------|---------|------|-----|
| **Existente** sda2 | 100-500MB | EFI System | Compartilhada Windows/Arch |
| **Nova** sda4 | 8GB | Linux Swap | Memória virtual |
| **Nova** sda5 | 40-60GB | Linux filesystem | Raiz (/) |
| **Nova** sda6 | Resto | Linux filesystem | Home (/home) |

### 6.2 Criar Novas Partições

```bash
# Abrir fdisk no disco (substitua sda pelo seu disco)
fdisk /dev/sda
```

**Dentro do fdisk:**

```bash
# NÃO crie nova tabela GPT! Ela já existe!
# Vá direto para criar as partições

# Verificar partições existentes
Command (m for help): p

# Criar Partição Swap (8GB)
Command (m for help): n
Partition number: 4 (ou próximo número disponível)
First sector: [Enter] (aceitar padrão)
Last sector: +8G

Command (m for help): t
Partition number: 4
Hex code or alias: 19  (Linux swap)

# Criar Partição Root (60GB)
Command (m for help): n
Partition number: 5
First sector: [Enter]
Last sector: +60G
# Tipo já é Linux filesystem (padrão)

# Criar Partição Home (resto do espaço)
Command (m for help): n
Partition number: 6
First sector: [Enter]
Last sector: [Enter] (usar todo espaço restante)

# Verificar e salvar
Command (m for help): p  (verificar)
Command (m for help): w  (salvar e sair)
```

### 6.3 Verificar Nova Estrutura

```bash
lsblk
```

**Estrutura esperada:**
```
NAME   SIZE TYPE
sda    500G disk
├─sda1 100M part  <- Windows Recovery
├─sda2 500M part  <- EFI (COMPARTILHADA)
├─sda3 350G part  <- Windows C:
├─sda4   8G part  <- Swap (NOVA)
├─sda5  60G part  <- Root / (NOVA)
└─sda6  81G part  <- Home /home (NOVA)
```

---

## 7. Formatação e Montagem

### 7.1 Formatar APENAS as Novas Partições

**ATENÇÃO**: NÃO formate sda1 (Recovery), sda2 (EFI) ou sda3 (Windows)!

```bash
# Verificar novamente antes de formatar
lsblk

# Formatar Swap
mkswap /dev/sda4
swapon /dev/sda4

# Formatar Root
mkfs.ext4 /dev/sda5

# Formatar Home
mkfs.ext4 /dev/sda6
```

### 7.2 Montar as Partições

```bash
# Montar Root
mount /dev/sda5 /mnt

# Criar diretórios
mkdir -p /mnt/boot/efi
mkdir /mnt/home

# Montar EFI EXISTENTE (do Windows)
mount /dev/sda2 /mnt/boot/efi

# Montar Home
mount /dev/sda6 /mnt/home

# Verificar montagens
lsblk
mount | grep /mnt
```

**Verificação final antes de continuar:**
```bash
ls /mnt/boot/efi/EFI
# Você DEVE ver uma pasta "Microsoft" aqui
# Se não ver, você montou a partição errada!
```

---

## 8. Continuar com Instalação Base

**A partir daqui, o processo é IDÊNTICO à instalação padrão.**

Siga o [Guia de Instalação Base do Arch Linux](./ARCH_BASE_INSTALL.md) a partir da seção **7. Configuração de Mirrors**.

Complete todas as seções até a **12. Configuração de Usuários** (inclusive). **Pare antes da seção 13** (Bootloader). Depois volte aqui para a configuração específica de dual-boot do GRUB.

---

## 9. Configuração do GRUB para Dual-Boot

Esta é a parte mais importante para dual-boot!

### 9.1 Instalar Pacotes Necessários

```bash
# Certifique-se de estar em arch-chroot
# Se não estiver, execute: arch-chroot /mnt

# Instalar GRUB e ferramentas para detectar Windows
pacman -S grub efibootmgr os-prober ntfs-3g
```

### 9.2 Instalar GRUB na Partição EFI Compartilhada

```bash
# Instalar GRUB
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck

# Verificar instalação
efibootmgr -v
# Você deve ver entradas para "Windows Boot Manager" E "GRUB"
```

### 9.3 Configurar os-prober

```bash
# Editar configuração do GRUB
nano /etc/default/grub

# Encontre e DESCOMENTE (remova o #) ou adicione esta linha:
GRUB_DISABLE_OS_PROBER=false

# Opcional: adicionar timeout maior
GRUB_TIMEOUT=10

# Salvar e sair (Ctrl+O, Enter, Ctrl+X)
```

### 9.4 Gerar Configuração do GRUB

```bash
# Gerar configuração detectando o Windows
grub-mkconfig -o /boot/grub/grub.cfg

# Você DEVE ver uma linha similar a:
# "Found Windows Boot Manager on /dev/sda2@/EFI/Microsoft/Boot/bootmgfw.efi"
```

**Se o Windows NÃO for detectado**, veja a seção de solução de problemas abaixo.

### 9.5 Regenerar Configuração do GRUB Após Instalar Microcódigo

Após instalar o microcódigo da CPU (seção 15 do guia base), você deve regenerar a configuração do GRUB:

```bash
# Ainda em arch-chroot
grub-mkconfig -o /boot/grub/grub.cfg
```

### 9.6 Sobre Secure Boot

**Durante a instalação**: Secure Boot deve estar DESABILITADO para instalar o Arch Linux.

**Após a instalação**: Se você quiser reabilitar Secure Boot, precisará:
1. Configurar Secure Boot para Linux (processo avançado)
2. Assinar o bootloader e kernel com suas próprias chaves
3. Consulte o [Guia de Secure Boot do Arch Wiki](https://wiki.archlinux.org/title/Secure_Boot)

Para a maioria dos usuários iniciantes, é recomendado **manter o Secure Boot desabilitado**.

### 9.7 Continuar com Instalação Base

Agora volte ao [Guia de Instalação Base](./ARCH_BASE_INSTALL.md) e complete as seções a partir da **14. Instalação do NetworkManager** até o final:

- **14. Instalação do NetworkManager**
- **15. Instalação de Microcódigo da CPU**
- **16. Finalização da Instalação**

---

## 10. Solução de Problemas Dual-Boot

### 10.1 GRUB Não Detecta o Windows

**Solução:**

```bash
# Boot pelo Arch Linux
# Verificar se os-prober está instalado
pacman -Q os-prober

# Se não estiver, instalar
sudo pacman -S os-prober ntfs-3g

# Editar configuração do GRUB
sudo nano /etc/default/grub
# Adicionar ou descomentar: GRUB_DISABLE_OS_PROBER=false

# Montar partição EFI
sudo mount /dev/sda2 /boot/efi

# Regenerar configuração
sudo grub-mkconfig -o /boot/grub/grub.cfg

# Verificar se detectou
# Deve aparecer: "Found Windows Boot Manager..."
```

### 10.2 Sistema Boota Direto no Windows

**Causa**: BIOS está priorizando Windows Boot Manager.

**Solução:**

1. Entre na BIOS/UEFI (F2, DEL, F10)
2. Procure "Boot Order" ou "Boot Priority"
3. Coloque **GRUB** ou **Arch Linux** como primeira opção
4. Salve e reinicie

**Alternativa via efibootmgr:**

```bash
# Verificar ordem de boot atual
efibootmgr -v

# Mudar ordem (coloque GRUB primeiro)
# Substitua XXXX pelo número do GRUB
sudo efibootmgr -o XXXX,YYYY
```

### 10.3 Erro "No Bootable Device"

**Solução:**

```bash
# Boot pelo pendrive do Arch
mount /dev/sda5 /mnt
mount /dev/sda2 /mnt/boot/efi
mount /dev/sda6 /mnt/home
arch-chroot /mnt

# Reinstalar GRUB
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
grub-mkconfig -o /boot/grub/grub.cfg

exit
umount -R /mnt
reboot
```

### 10.4 Horário Diferente entre Windows e Linux

**Causa**: Windows usa horário local, Linux usa UTC.

**Solução - Fazer Linux usar horário local:**

```bash
sudo timedatectl set-local-rtc 1 --adjust-system-clock
```

**OU fazer Windows usar UTC (recomendado):**

No Windows, como Administrador:
```cmd
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
```

### 10.5 Não Consigo Acessar Partições do Windows

**Solução:**

```bash
# Instalar ntfs-3g
sudo pacman -S ntfs-3g

# Criar ponto de montagem
sudo mkdir -p /mnt/windows

# Montar partição do Windows
sudo mount -t ntfs-3g /dev/sda3 /mnt/windows

# Para montar automaticamente, adicionar ao /etc/fstab:
echo "/dev/sda3 /mnt/windows ntfs-3g defaults 0 0" | sudo tee -a /etc/fstab
```

### 10.7 Partição EFI Muito Pequena (100MB)

**Problema**: Windows criou partição EFI de 100MB, que enche rapidamente com múltiplos kernels.

**Solução 1 - Usar compressão alta no initramfs (Recomendado):**

```bash
sudo nano /etc/mkinitcpio.conf

# Adicionar estas linhas:
COMPRESSION="xz"
COMPRESSION_OPTIONS=(-9e)
MODULES_DECOMPRESS="yes"

# Salvar e regenerar initramfs
sudo mkinitcpio -P
```

**Solução 2 - Remover kernels antigos regularmente:**

```bash
# Listar kernels instalados
ls /boot/efi/EFI/GRUB/

# Remover kernels antigos manualmente quando necessário
# (Mantenha sempre pelo menos um kernel funcionando!)
```

**Solução 3 - Aumentar partição EFI (Avançado):**

Consulte: [Arch Wiki - EFI System Partition](https://wiki.archlinux.org/title/EFI_system_partition#Replace_the_partition_with_a_larger_one)

### 10.8 Windows Não Inicia Após Atualização

**Causa**: Atualização do Windows pode reescrever o bootloader.

**Solução:**

```bash
# Boot pelo pendrive do Arch
mount /dev/sda5 /mnt
mount /dev/sda2 /mnt/boot/efi
arch-chroot /mnt

# Reinstalar GRUB
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
grub-mkconfig -o /boot/grub/grub.cfg

exit
reboot
```

---

## Próximos Passos

Após concluir a instalação dual-boot, siga o [Guia de Pós-Instalação](./ARCH_POST_INSTALL.md) para configurar seu sistema Arch Linux completamente.

---

## Recursos Adicionais

- [Arch Wiki - Dual Boot with Windows](https://wiki.archlinux.org/title/Dual_boot_with_Windows)
- [Arch Wiki - GRUB](https://wiki.archlinux.org/title/GRUB)
- [Arch Wiki - EFI System Partition](https://wiki.archlinux.org/title/EFI_system_partition)

---

**Parabéns! Você configurou com sucesso um dual-boot Arch Linux + Windows!**

*Última atualização: Dezembro 2025*
