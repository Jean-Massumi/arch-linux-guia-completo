# Guia de Criação de Pendrive Bootável do Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **ATENÇÃO**: Este processo irá **APAGAR TODOS OS DADOS** do pendrive selecionado. Faça backup de arquivos importantes antes de continuar!

> **Tempo estimado**: 15-30 minutos (dependendo da velocidade de download e gravação)

---

## Índice

- [Requisitos](#requisitos)
- [1. Download da ISO do Arch Linux](#1-download-da-iso-do-arch-linux)
- [2. Verificação de Integridade](#2-verificação-de-integridade-da-iso-recomendado)
- [3. Windows - Método Rufus](#3-criando-pendrive-bootável-no-windows)
- [4. Windows - Método Ventoy](#método-2-ventoy-alternativa-versátil)
- [5. Linux - Método dd](#4-criando-pendrive-bootável-no-linux)
- [6. Linux - Método Ventoy](#método-2-ventoy-no-linux)
- [7. Configurando BIOS/UEFI](#5-configurando-a-biosuefi-para-boot)
- [8. Solução de Problemas](#6-solução-de-problemas)

---

## Requisitos

- Pendrive com **no mínimo 4GB** de espaço (recomendado 8GB)
- Conexão com internet estável
- **Backup** de dados importantes do pendrive
- Verificar se o computador suporta boot via USB

---

## 1. Download da ISO do Arch Linux

### 1.1 Download Oficial

**Link direto**: https://archlinux.org/download/

### 1.2 Escolhendo o Mirror (Brasil)

**Mirrors recomendados para usuários brasileiros:**

| Mirror | Instituição | Localização |
|--------|-------------|-------------|
| **c3sl.ufpr.br** | Universidade Federal do Paraná | Curitiba, PR |
| **ufscar.br** | Universidade Federal de São Carlos | São Carlos, SP |
| **unicamp.br** | Universidade Estadual de Campinas | Campinas, SP |
| **cicku.me** | Mirror independente | Brasil |

**Como baixar:**

1. Acesse a página de downloads
2. Role até a seção **"Brazil"** na lista de mirrors
3. Clique no link do mirror mais próximo
4. Baixe o arquivo: `archlinux-YYYY.MM.DD-x86_64.iso`

### 1.3 Download via Torrent (Opcional)

**Vantagens:**
- Download mais rápido em conexões instáveis
- Retomada automática em caso de interrupção
- Verificação de integridade embutida

**Como fazer:**
1. Baixe o arquivo `.torrent` no site oficial
2. Abra com qBittorrent, Transmission, ou outro cliente
3. Aguarde o download completar

---

## 2. Verificação de Integridade da ISO (Recomendado)

É altamente recomendado verificar se a ISO foi baixada corretamente para evitar problemas durante a instalação.

### 2.1 No Windows (PowerShell)

```powershell
# Navegue até a pasta de downloads
cd C:\Users\SeuUsuario\Downloads

# Calcule o hash SHA256
certutil -hashfile archlinux-2025.XX.XX-x86_64.iso SHA256
```

### 2.2 No Linux (Terminal)

```bash
# Navegue até a pasta de downloads
cd ~/Downloads

# Calcule o hash SHA256
sha256sum archlinux-2025.XX.XX-x86_64.iso
```

### 2.3 Verificar o Hash

1. Copie o hash gerado pelo comando
2. Acesse: https://archlinux.org/download/
3. Localize a seção **"Checksums"**
4. Compare os valores:
   - **Idênticos**: ISO íntegra, pode usar com segurança
   - **Diferentes**: Baixe novamente (arquivo corrompido)

---

## 3. Criando Pendrive Bootável no Windows

### Método 1: Rufus (Recomendado para Iniciantes)

**Rufus** é a ferramenta mais popular e confiável para criar pendrives bootáveis no Windows.

#### 3.1 Download do Rufus

**Link**: https://rufus.ie/

- Baixe a versão **portable** (não requer instalação)
- Execute `rufus-x.xx.exe`

#### 3.2 Configuração

| Opção | Valor | Quando Usar |
|-------|-------|-------------|
| **Dispositivo** | Seu pendrive | - |
| **Seleção de boot** | Imagem ISO do Arch | Clique em "SELECIONAR" |
| **Esquema de partição** | GPT | Computadores modernos (UEFI) |
| **Esquema de partição** | MBR | Computadores antigos (BIOS Legacy) |
| **Sistema de destino** | UEFI (non CSM) | Para GPT |
| **Sistema de destino** | BIOS (ou UEFI-CSM) | Para MBR |
| **Sistema de arquivos** | FAT32 | Padrão (deixar assim) |

**Dica**: Se não sabe qual usar, escolha **GPT + UEFI** (mais comum em PCs de 2012 em diante)

#### 3.3 Opções Avançadas (Opcional)

Clique em "MOSTRAR OPÇÕES AVANÇADAS":
- **Nome do volume**: Arch Linux (opcional)
- **Formatação rápida**: Marcado
- **Criar disco bootável usando**: Imagem ISO
- **Criar partição estendida**: Desmarcado

#### 3.4 Gravar

1. Clique em **"INICIAR"**
2. Se perguntar sobre modo de gravação, escolha: **"Gravar em modo de Imagem DD"**
3. Confirme que deseja apagar os dados do pendrive
4. Aguarde 2-5 minutos
5. Clique em **"FECHAR"** quando finalizar

---

### Método 2: Ventoy (Alternativa Versátil)

**Ventoy** permite criar um pendrive multiboot, onde você pode ter várias ISOs diferentes no mesmo pendrive.

#### 3.5 Download

**Link**: https://www.ventoy.net/

1. Baixe a versão para Windows
2. Extraia o arquivo ZIP

#### 3.6 Instalação

1. Execute `Ventoy2Disk.exe` como **administrador**
2. Selecione seu pendrive na lista
3. Clique em **"Install"**
4. Confirme a instalação
5. Aguarde finalizar

#### 3.7 Adicionar ISO

Após a instalação, o pendrive terá uma partição visível. **Simplesmente copie** o arquivo `.iso` do Arch Linux para o pendrive.

Pronto! O Ventoy detectará automaticamente a ISO no boot.

**Vantagens do Ventoy:**
- Suporta múltiplas ISOs no mesmo pendrive
- Não precisa reformatar para adicionar/remover ISOs
- Suporta BIOS e UEFI automaticamente

---

## 4. Criando Pendrive Bootável no Linux

### Método 1: Comando dd (Tradicional)

**ATENÇÃO**: O comando `dd` é muito poderoso. Um erro pode apagar dados do disco rígido!

#### 4.1 Identificar o Pendrive

```bash
# Listar todos os dispositivos
lsblk

# Ou use:
sudo fdisk -l
```

**Exemplo de saída:**
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 465.8G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0     8G  0 part [SWAP]
└─sda3   8:3    0 457.3G  0 part /
sdb      8:16   1  14.9G  0 disk          <- SEU PENDRIVE
└─sdb1   8:17   1  14.9G  0 part
```

**Identifique seu pendrive** pelo tamanho. No exemplo acima, é `/dev/sdb`.

#### 4.2 Desmontar o Pendrive (se montado)

```bash
# Substitua 'sdb' pelo dispositivo do SEU pendrive
sudo umount /dev/sdb*
```

#### 4.3 Gravar a ISO no Pendrive

```bash
# CUIDADO: Verifique o nome do dispositivo!
# Substitua 'sdb' pelo dispositivo correto
# Substitua o nome da ISO pelo arquivo que você baixou

sudo dd bs=4M \
  if=~/Downloads/archlinux-2025.XX.XX-x86_64.iso \
  of=/dev/sdb \
  status=progress \
  oflag=sync
```

**Explicação dos parâmetros:**
- `bs=4M`: Tamanho do bloco de gravação (4 megabytes)
- `if=`: Arquivo de entrada (input file) - a ISO
- `of=`: Arquivo de saída (output file) - o pendrive
- `status=progress`: Mostra o progresso da operação
- `oflag=sync`: Sincroniza os dados, garantindo gravação completa

**Exemplo real:**
```bash
sudo dd bs=4M if=~/Downloads/archlinux-2025.01.01-x86_64.iso of=/dev/sdb status=progress oflag=sync
```

#### 4.4 Aguardar Finalização

O processo pode demorar alguns minutos. Aguarde até ver a mensagem de conclusão.

#### 4.5 Sincronizar e Ejetar

```bash
# Garantir que todos os dados foram gravados
sync

# Ejetar o pendrive com segurança
sudo eject /dev/sdb
```

---

### Método 2: Ventoy no Linux

#### 4.6 Download e Instalação

```bash
# Baixar Ventoy
cd ~/Downloads
wget https://github.com/ventoy/Ventoy/releases/download/v1.0.XX/ventoy-1.0.XX-linux.tar.gz

# Extrair
tar -xzf ventoy-1.0.XX-linux.tar.gz
cd ventoy-1.0.XX
```

#### 4.7 Instalar Ventoy no Pendrive

```bash
# Listar dispositivos
sudo ./Ventoy2Disk.sh -l

# Instalar no pendrive (substitua /dev/sdb pelo seu dispositivo)
sudo ./Ventoy2Disk.sh -i /dev/sdb

# Confirme quando solicitado
```

#### 4.8 Copiar a ISO

```bash
# O pendrive será montado automaticamente
# Copiar a ISO para o pendrive
cp ~/Downloads/archlinux-2025.XX.XX-x86_64.iso /media/$USER/Ventoy/
```

---

## 5. Configurando a BIOS/UEFI para Boot

Após criar o pendrive bootável, você precisa configurar seu computador para inicializar pelo pendrive.

### 5.1 Acessar a BIOS/UEFI

**Teclas comuns para acessar:**

| Fabricante | Tecla |
|------------|-------|
| Dell, Acer, Lenovo, ASUS | F2 |
| HP | F10 |
| Dell, Lenovo (Boot Menu) | F12 |
| ASUS, MSI, Gigabyte | DEL |
| HP, Compaq | ESC |

**Como fazer:**
1. Reinicie o computador
2. Pressione repetidamente a tecla correspondente durante a inicialização
3. Entre nas configurações de BIOS/UEFI

### 5.2 Configurar Ordem de Boot

1. Procure pela seção **"Boot"** ou **"Boot Order"**
2. Coloque o **USB** ou **Removable Devices** como primeira opção
3. Salve as configurações (geralmente F10)
4. Reinicie

### 5.3 Desabilitar Secure Boot (se necessário)

Em alguns computadores modernos, pode ser necessário:

1. Localize a opção **"Secure Boot"**
2. Mude para **"Disabled"** ou **"Other OS"**
3. Salve e reinicie

### 5.4 Boot Menu Rápido (Alternativa)

Método mais rápido sem entrar na BIOS:

1. Reinicie com o pendrive conectado
2. Pressione a tecla de Boot Menu (geralmente **F12** ou **F8**)
3. Selecione o pendrive na lista
4. Pressione Enter

---

## 6. Solução de Problemas

### 6.1 Pendrive não aparece no boot

**Soluções:**
- Verifique se o pendrive está corretamente conectado
- Tente outra porta USB (prefira USB 2.0 para maior compatibilidade)
- Recrie o pendrive usando outro método (Rufus modo DD ou Ventoy)
- Verifique se o Secure Boot está desabilitado na BIOS

### 6.2 Erro "Boot device not found"

**Soluções:**
- Verifique se escolheu a opção correta no Rufus:
  - GPT para UEFI
  - MBR para BIOS Legacy
- Recrie o pendrive usando "modo DD" no Rufus
- Tente usar outro pendrive
- Verifique se a ISO está íntegra (SHA256)

### 6.3 Sistema não inicializa após criar o pendrive

**Soluções:**
- Verifique a integridade da ISO baixada
- Baixe a ISO novamente de outro mirror
- Formate completamente o pendrive antes de recriar
- Teste o pendrive em outro computador

### 6.4 Erro no dd: "Permission denied"

**Solução:**
```bash
# Certifique-se de usar sudo
sudo dd bs=4M if=arquivo.iso of=/dev/sdb status=progress oflag=sync

# Verifique se o pendrive não está montado
sudo umount /dev/sdb*
```

### 6.5 Pendrive fica inutilizável após criar boot

**Como restaurar o pendrive para uso normal:**

**No Windows:**
1. Abra o "Gerenciamento de Disco" (diskmgmt.msc)
2. Localize o pendrive
3. Delete todas as partições
4. Crie uma nova partição
5. Formate como FAT32 ou NTFS

**No Linux:**
```bash
# Listar dispositivos
sudo fdisk -l

# Abrir fdisk no dispositivo (ex: /dev/sdb)
sudo fdisk /dev/sdb

# Dentro do fdisk:
# d - deletar todas as partições
# n - criar nova partição (aceite os padrões)
# t - mudar tipo para 0c (FAT32 LBA)
# w - salvar e sair

# Formatar
sudo mkfs.vfat -F32 /dev/sdb1
```

---

## Próximo Passo

Após criar o pendrive bootável com sucesso, siga o guia de instalação:

**[ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md)** - Instalação completa do Arch Linux

---

## Recursos Adicionais

- **Documentação oficial**: [Arch Linux Installation Guide](https://wiki.archlinux.org/title/Installation_guide)
- **USB Flash Installation**: [USB flash installation medium](https://wiki.archlinux.org/title/USB_flash_installation_medium)

---

**Boa sorte com sua instalação do Arch Linux!**

*Última atualização: Janeiro 2026*
