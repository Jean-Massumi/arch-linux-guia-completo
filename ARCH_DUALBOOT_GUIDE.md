# Guia de Instalação Dual-Boot: Arch Linux + Windows

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)

> **Dual-Boot**: Tenha Windows e Arch Linux no mesmo computador

> **IMPORTANTE**: Este guia assume que você JÁ tem Windows instalado e quer adicionar o Arch Linux. Se você não tem nenhum sistema instalado, siga primeiro o [Guia de Instalação Base do Arch Linux](./ARCH_BASE_INSTALL.md).

---

## Índice

- [Diferenças do Dual-Boot](#diferenças-do-dual-boot)
- [Pré-requisitos](#pré-requisitos)
- [1. Preparação do Windows](#1-preparação-do-windows)
- [2. Redimensionar Partição do Windows](#2-redimensionar-partição-do-windows)
- [3. Desabilitar Fast Boot e Secure Boot](#3-desabilitar-fast-boot-e-secure-boot)
- [4. Boot pelo Pendrive do Arch Linux](#4-boot-pelo-pendrive-do-arch-linux)
- [5. Identificar Partições Existentes](#5-identificar-partições-existentes)
- [6. Particionamento para Dual-Boot](#6-particionamento-para-dual-boot)
- [7. Formatação e Montagem](#7-formatação-e-montagem)
- [8. Continuar com Instalação Base](#8-continuar-com-instalação-base)
- [9. Configuração do GRUB para Dual-Boot](#9-configuração-do-grub-para-dual-boot)
- [10. Finalização](#10-finalização)
- [11. Dicas Importantes](#11-dicas-importantes)
- [12. Solução de Problemas Dual-Boot](#12-solução-de-problemas-dual-boot)
- [Apêndice A: Dual-Boot em Discos Separados](#apêndice-a-dual-boot-em-discos-separados)

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

**Para tudo que não está especificamente listado neste guia de dual-boot, siga o [Guia de Instalação Base](./ARCH_BASE_INSTALL.md) normalmente.**

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

### 1.2 Desabilitar BitLocker (se ativo)

```powershell
# Abrir PowerShell como Administrador
# Verificar se BitLocker está ativo
manage-bde -status

# Se estiver ativo, desabilitar (substitua C: pela unidade correta)
manage-bde -off C:
```

**Aguarde o processo de descriptografia completar antes de continuar.**

### 1.3 Verificar Espaço em Disco Disponível

1. Abra o **Gerenciamento de Disco** (diskmgmt.msc)
2. Verifique quanto espaço livre você tem na partição C:
3. Certifique-se de ter pelo menos **60GB livres** para o Arch

### 1.4 Desfragmentar o Disco (HDD apenas)

**Se você usa HDD (não SSD)**:

1. Abra "Desfragmentar e Otimizar Unidades"
2. Selecione a unidade C:
3. Clique em "Otimizar"
4. Aguarde completar

**IMPORTANTE**: Não desfragmente SSDs. Isso reduz sua vida útil.

---

## 2. Redimensionar Partição do Windows

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

**O Windows criará um espaço "Não Alocado" - NÃO formate este espaço agora.**

> **Por que redimensionar pelo Windows?** É mais seguro e o próprio Windows gerencia suas estruturas internas. Redimensionar pelo Linux pode corromper o Windows se feito incorretamente.

---

## 3. Desabilitar Fast Boot e Secure Boot

### 3.1 Desabilitar Fast Boot no Windows

1. Abra "Painel de Controle"
2. Vá em "Opções de Energia"
3. Clique em "Escolher a função dos botões de energia"
4. Clique em "Alterar configurações não disponíveis no momento"
5. **Desmarque** "Ativar inicialização rápida"
6. Salve as alterações

> **IMPORTANTE**: Mantenha o Fast Boot permanentemente desabilitado durante o uso do dual-boot.

### 3.2 Desabilitar Secure Boot na BIOS/UEFI

1. Reinicie o computador
2. Entre na BIOS/UEFI (geralmente F2, F10, DEL ou ESC)
3. Procure por "Secure Boot"
4. Mude para **"Disabled"** ou **"Other OS"**
5. Salve e saia (F10)

**Por quê?** Secure Boot pode impedir o boot do Arch Linux. Você pode reabilitá-lo após a instalação (processo avançado).

---

## 4. Boot pelo Pendrive do Arch Linux

1. Insira o pendrive bootável
2. Reinicie o computador
3. Entre no Boot Menu (geralmente F12, F8 ou ESC)
4. Selecione o pendrive
5. Escolha "Arch Linux install medium" no menu do GRUB

**A partir daqui, você está no ambiente de instalação do Arch Linux.**

### 4.1 Configurações Iniciais

Siga as seções **1** e **2** do guia base para:
- Configurar layout do teclado
- Conectar à internet
- Sincronizar horário

**Guia**: [ARCH_BASE_INSTALL.md - Seções 1 e 2](./ARCH_BASE_INSTALL.md#1-configuração-inicial-do-sistema)

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
└─(livre)   149G         <- Espaço não alocado (onde instalaremos o Arch)
```

**IMPORTANTE**: Identifique estas partições:

| Partição | Tamanho Típico | Tipo | O Que É |
|----------|----------------|------|---------|
| sda1 | 100-500MB | Recovery | Recuperação do Windows (NÃO MEXA) |
| sda2 | 100-500MB | EFI System | Partição EFI (USAR, NÃO FORMATAR) |
| sda3 | Variável | Microsoft basic data | Windows C: (NÃO MEXA) |
| (livre) | Não alocado | - | Espaço livre para Arch |

### 5.2 Verificar Partição EFI

```bash
# Montar temporariamente para verificar conteúdo
mount /dev/sda2 /mnt
ls /mnt/EFI/Microsoft && echo "Partição EFI confirmada" || echo "ERRO: Partição incorreta"
umount /mnt
```

> **IMPORTANTE**: Se sua partição EFI for menor que 300MB, consulte a seção de troubleshooting (12.7) para soluções específicas.

---

## 6. Particionamento para Dual-Boot

### 6.1 Compreendendo a Partição EFI

**ATENÇÃO CRÍTICA**: 

**No MESMO disco físico:**
- Você DEVE USAR a partição EFI existente do Windows
- NÃO criar uma segunda partição EFI

**Por que não criar uma segunda partição EFI?**
- Viola o padrão UEFI (uma ESP por disco)
- Causa problemas em muitas BIOS/UEFI
- Complexidade desnecessária
- Desperdício de espaço

**Exceção**: Se você tem Windows e Arch em discos físicos separados, veja o [Apêndice A](#apêndice-a-dual-boot-em-discos-separados).

### 6.2 Estrutura de Partições para Dual-Boot

**Partições Existentes do Windows (NÃO MEXA):**
- sda1: Recuperação do Windows (100-500MB)
- sda2: EFI (100-500MB) - **SERÁ COMPARTILHADA**
- sda3: Windows C: (tamanho variável)

**Novas Partições do Arch Linux:**

| Partição | Tamanho | Tipo | Uso |
|----------|---------|------|-----|
| sda4 | 8GB | Linux Swap | Memória virtual |
| sda5 | 40-60GB | Linux filesystem | Raiz (/) |
| sda6 | Resto | Linux filesystem | Home (/home) |

### 6.3 Criar Novas Partições

```bash
# Abrir fdisk no disco (substitua sda pelo seu disco)
fdisk /dev/sda
```

**Dentro do fdisk:**

```bash
# NÃO crie nova tabela GPT! Ela já existe do Windows!
# Vá direto para criar as novas partições

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

### 6.4 Verificar Nova Estrutura

```bash
lsblk
```

**Estrutura esperada:**
```
NAME   SIZE TYPE
sda    500G disk
├─sda1 100M part  <- Windows Recovery (NÃO MEXA)
├─sda2 500M part  <- EFI (COMPARTILHADA)
├─sda3 350G part  <- Windows C: (NÃO MEXA)
├─sda4   8G part  <- Swap (NOVA)
├─sda5  60G part  <- Root / (NOVA)
└─sda6  81G part  <- Home /home (NOVA)
```

---

## 7. Formatação e Montagem

### 7.1 Formatar APENAS as Novas Partições

**ATENÇÃO**: NÃO formate sda1 (Recovery), sda2 (EFI) ou sda3 (Windows).

```bash
# Verificar estrutura antes de formatar (OBRIGATÓRIO)
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
```

---

## 8. Continuar com Instalação Base

A partir daqui, execute as seguintes seções do guia base:

**[ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md)**

### 8.1 Seções a Completar

- **Seção 7**: Configuração de Mirrors
- **Seção 8**: Instalação do Sistema Base  
- **Seção 9**: Configuração do Sistema (idioma, localização)
- **Seção 10**: Configuração de Data e Hora
- **Seção 11**: Configuração de Hostname
- **Seção 12**: Configuração de Usuários

### 8.2 Quando Parar

**PARE antes da seção 13 (Bootloader)** do guia base e volte aqui para a seção 9.

> **Por quê?** A configuração do bootloader para dual-boot é diferente e será feita na próxima seção.

---

## 9. Configuração do GRUB para Dual-Boot

Esta é a parte mais importante para dual-boot funcionar.

### 9.1 Instalar Pacotes Necessários

```bash
# Certifique-se de estar em arch-chroot
# Se não estiver, execute: arch-chroot /mnt

# Instalar GRUB e ferramentas para detectar Windows
pacman -S grub efibootmgr os-prober ntfs-3g
```

**O que cada pacote faz:**
- `grub`: Bootloader principal
- `efibootmgr`: Gerencia entradas de boot UEFI
- `os-prober`: Detecta outros sistemas operacionais (Windows)
- `ntfs-3g`: Permite ler/escrever em partições NTFS do Windows

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

# Opcional: adicionar timeout maior para ter mais tempo de escolher
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

**Se o Windows NÃO for detectado**, veja a seção 12.1 de troubleshooting.

### 9.5 Serviços Essenciais e Microcódigo

Complete as seguintes seções do guia base:
- **Seção 14**: Instalação do NetworkManager
- **Seção 15**: Instalação de Microcódigo da CPU

---

## 10. Finalização

### 10.1 Sair e Reiniciar

```bash
# Sair do chroot
exit

# Desmontar todas as partições
umount -R /mnt

# Reiniciar
reboot
```

### 10.2 Primeiro Boot Dual-Boot

Ao reiniciar, você verá o **menu do GRUB** com opções:

1. **Arch Linux**
2. **Advanced options for Arch Linux**
3. **Windows Boot Manager**

**Teste ambos os sistemas** para garantir que funcionam corretamente.

### 10.3 Configuração Pós-Instalação

Após verificar que o dual-boot funciona, siga o guia de pós-instalação:

**[ARCH_POST_INSTALL.md](./ARCH_POST_INSTALL.md)** - Configuração completa do ambiente

---

## 11. Dicas Importantes

### 11.1 Manutenção do Dual-Boot

1. **Sempre atualize o GRUB após mudanças**:
   ```bash
   sudo grub-mkconfig -o /boot/grub/grub.cfg
   ```

2. **Mantenha Fast Boot desabilitado** no Windows (veja seção 3.1)

3. **Não use Hibernação no Windows** quando for alternar para Linux

4. **Faça backup regular** de ambos os sistemas

5. **Não mexa na partição EFI** manualmente

### 11.2 Acessando Arquivos do Windows no Linux

```bash
# Criar ponto de montagem
sudo mkdir -p /mnt/windows

# Montar partição do Windows
sudo mount -t ntfs-3g /dev/sda3 /mnt/windows

# Acessar arquivos
ls /mnt/windows
```

**Para montar automaticamente na inicialização:**
```bash
echo "/dev/sda3 /mnt/windows ntfs-3g defaults 0 0" | sudo tee -a /etc/fstab
```

---

## 12. Solução de Problemas Dual-Boot

### 12.1 GRUB Não Detecta o Windows

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

# Montar partição EFI se não estiver montada
sudo mount /dev/sda2 /boot/efi

# Regenerar configuração
sudo grub-mkconfig -o /boot/grub/grub.cfg

# Verificar se detectou
# Deve aparecer: "Found Windows Boot Manager..."
```

### 12.2 Sistema Boota Direto no Windows

**Causa**: BIOS está priorizando Windows Boot Manager.

**Solução 1 - Pela BIOS:**

1. Entre na BIOS/UEFI (F2, DEL, F10)
2. Procure "Boot Order" ou "Boot Priority"
3. Coloque **GRUB** ou **Arch Linux** como primeira opção
4. Salve e reinicie

**Solução 2 - Via efibootmgr:**

```bash
# Verificar ordem de boot atual
efibootmgr -v

# Mudar ordem (coloque GRUB primeiro)
# Substitua XXXX pelo número do GRUB
sudo efibootmgr -o XXXX,YYYY
```

### 12.3 Erro "No Bootable Device" ou Windows Sobrescreveu GRUB

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

### 12.4 Horário Diferente entre Windows e Linux

**Causa**: Windows usa horário local (RTC), Linux usa UTC.

**Solução 1 - Fazer Linux usar horário local (mais simples):**

```bash
sudo timedatectl set-local-rtc 1 --adjust-system-clock
```

**Solução 2 - Fazer Windows usar UTC (recomendado):**

No Windows, como Administrador:
```cmd
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
```

### 12.5 Não Consigo Acessar Partições do Windows no Arch

**Solução**: Veja seção 11.3 sobre como montar partições Windows.

Se o problema persistir:

```bash
# Verificar se ntfs-3g está instalado
sudo pacman -S ntfs-3g

# Tentar montar com mais opções
sudo mount -t ntfs-3g -o rw,uid=1000,gid=1000 /dev/sda3 /mnt/windows
```

### 12.6 Partição EFI Muito Pequena (100MB)

**Problema**: Windows criou partição EFI de apenas 100MB, que pode encher rapidamente.

**Solução 1 - Usar compressão alta no initramfs (Recomendado):**

```bash
sudo nano /etc/mkinitcpio.conf

# Adicionar estas linhas ao final:
COMPRESSION="xz"
COMPRESSION_OPTIONS=(-9e)
MODULES_DECOMPRESS="yes"

# Salvar e regenerar initramfs
sudo mkinitcpio -P
```

**Solução 2 - Manter apenas um kernel:**

```bash
# Listar kernels instalados
pacman -Q | grep linux

# Ao atualizar, remover kernels antigos automaticamente
# O pacman já faz isso, mas você pode verificar com:
ls /boot

# Manter apenas o kernel atual e remover backups antigos
```

**Solução 3 - Aumentar partição EFI (Avançado):**

Consulte: [Arch Wiki - EFI System Partition](https://wiki.archlinux.org/title/EFI_system_partition#Enlarge_the_EFI_system_partition)

---

## Apêndice A: Dual-Boot em Discos Separados

Se você tem Windows em um disco (ex: sda) e quer instalar Arch em outro disco físico (ex: sdb):

### Características deste Cenário

**Vantagens:**
- Isolamento completo entre sistemas
- Pode criar nova partição EFI no disco do Arch
- Mais fácil de remover um sistema sem afetar o outro
- Menor risco de corrupção acidental

**Desvantagens:**
- Precisa selecionar o disco no Boot Menu da BIOS a cada boot (opcional)
- Dois pontos de falha (dois discos)

### Processo de Instalação

**Este cenário NÃO requer este guia de dual-boot.**

Siga o **[Guia de Instalação Base](./ARCH_BASE_INSTALL.md)** completo normalmente:

1. Instale o Arch Linux no segundo disco como instalação única
2. Crie uma nova partição EFI no disco do Arch
3. Instale o GRUB normalmente
4. O GRUB detectará o Windows automaticamente (mesmo em outro disco)

### Configuração da BIOS

**Opção 1 - GRUB como bootloader principal:**
1. Configure a BIOS para dar prioridade ao disco do Arch
2. O GRUB carregará automaticamente e mostrará ambos os sistemas

**Opção 2 - Selecionar manualmente:**
1. Deixe a BIOS com prioridade no disco do Windows
2. Pressione F12 (ou tecla de boot menu) para escolher o disco do Arch quando quiser

### Estrutura de Partições

**Disco 1 (sda) - Windows:**
```
sda
├─ sda1: EFI (Windows)
├─ sda2: Windows Recovery
└─ sda3: Windows C:
```

**Disco 2 (sdb) - Arch Linux:**
```
sdb
├─ sdb1: EFI (Arch) <- NOVA PARTIÇÃO EFI
├─ sdb2: Swap
├─ sdb3: Root /
└─ sdb4: Home
```

### Configuração do GRUB

```bash
# Após instalar normalmente, editar:
sudo nano /etc/default/grub

# Adicionar:
GRUB_DISABLE_OS_PROBER=false

# Instalar os-prober
sudo pacman -S os-prober

# Regenerar configuração
sudo grub-mkconfig -o /boot/grub/grub.cfg

# O Windows será detectado automaticamente mesmo em outro disco
```

---

## Recursos Adicionais

- **Arch Wiki - Dual Boot with Windows**: [wiki.archlinux.org/title/Dual_boot_with_Windows](https://wiki.archlinux.org/title/Dual_boot_with_Windows)
- **Arch Wiki - GRUB**: [wiki.archlinux.org/title/GRUB](https://wiki.archlinux.org/title/GRUB)
- **Arch Wiki - EFI System Partition**: [wiki.archlinux.org/title/EFI_system_partition](https://wiki.archlinux.org/title/EFI_system_partition)

