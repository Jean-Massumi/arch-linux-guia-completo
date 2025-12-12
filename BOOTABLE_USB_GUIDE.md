# Guia de Criação de Pendrive Bootável do Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **ATENÇÃO**: Este processo irá **APAGAR TODOS OS DADOS** do pendrive selecionado. Faça backup de arquivos importantes antes de continuar!

---

## Índice

- [1. Download da ISO do Arch Linux](#1-download-da-iso-do-arch-linux)
- [2. Verificação de Integridade da ISO](#2-verificação-de-integridade-da-iso-recomendado)
- [3. Criando Pendrive Bootável no Windows](#3-criando-pendrive-bootável-no-windows)
- [4. Criando Pendrive Bootável no Linux](#4-criando-pendrive-bootável-no-linux)
- [5. Configurando a BIOS/UEFI para Boot](#5-configurando-a-biosuefi-para-boot)
- [6. Solução de Problemas](#6-solução-de-problemas)

---

## Requisitos

- Pendrive com **no mínimo 4GB** de espaço (recomendado 8GB ou mais)
- Conexão com internet estável
- **Backup** de dados importantes do pendrive (serão apagados!)

---

## 1. Download da ISO do Arch Linux

### 1.1 Download Oficial

Acesse o site oficial do Arch Linux:

**Link direto**: [https://archlinux.org/download/](https://archlinux.org/download/)

### 1.2 Escolhendo o Mirror

Na página de download, você verá uma lista de mirrors (servidores espelho) organizados por país.

**Para usuários brasileiros**, recomendamos usar um dos seguintes mirrors:

- **c3sl.ufpr.br** - Universidade Federal do Paraná (UFPR) - Curitiba
- **ufscar.br** - Universidade Federal de São Carlos (UFSCar)
- **unicamp.br** - Universidade Estadual de Campinas (UNICAMP)
- **cicku.me** - Mirror independente brasileiro

**Como baixar:**

1. Localize a seção "Brazil" na lista de mirrors
2. Clique no link do mirror de sua preferência
3. Baixe o arquivo com nome similar a: `archlinux-2026.XX.XX-x86_64.iso`

### 1.3 Download via Torrent (Alternativa)

Se preferir, você pode usar o **BitTorrent** para baixar a ISO:

1. Baixe o arquivo `.torrent` no site oficial
2. Abra com seu cliente BitTorrent favorito (qBittorrent, Transmission, etc.)
3. Aguarde o download completar

**Vantagens do Torrent:**
- Download mais rápido em conexões lentas
- Retomada automática em caso de interrupção
- Verificação automática de integridade

---

## 2. Verificação de Integridade da ISO (Recomendado)

É **altamente recomendado** verificar se a ISO foi baixada corretamente para evitar problemas durante a instalação.

### 2.1 No Windows

```powershell
# Abra o PowerShell e navegue até a pasta onde baixou a ISO
cd C:\Users\SeuUsuario\Downloads

# Calcule o hash SHA256
certutil -hashfile archlinux-2025.XX.XX-x86_64.iso SHA256
```

### 2.2 No Linux

```bash
# Navegue até a pasta onde baixou a ISO
cd ~/Downloads

# Calcule o hash SHA256
sha256sum archlinux-2025.XX.XX-x86_64.iso
```

### 2.3 Comparar o Hash

1. Copie o hash gerado pelo comando
2. Acesse: [https://archlinux.org/download/](https://archlinux.org/download/)
3. Procure pela seção "Checksums"
4. Compare o hash SHA256 da página com o que você gerou

**Se os hashes forem idênticos**: A ISO está íntegra e pode ser usada com segurança.

**Se os hashes forem diferentes**: Baixe a ISO novamente, pois o arquivo pode estar corrompido.

---

## 3. Criando Pendrive Bootável no Windows

### Método 1: Rufus (Recomendado)

**Rufus** é a ferramenta mais popular e confiável para criar pendrives bootáveis no Windows.

#### 3.1 Download do Rufus

1. Acesse: [https://rufus.ie/](https://rufus.ie/)
2. Baixe a versão **portable** (não requer instalação)
3. Execute o arquivo `rufus-x.xx.exe`

#### 3.2 Configuração no Rufus

1. **Dispositivo**: Selecione seu pendrive
2. **Seleção de boot**: Clique em "SELECIONAR" e escolha a ISO do Arch Linux
3. **Esquema de partição**: 
   - Para UEFI: Selecione **GPT**
   - Para BIOS Legacy: Selecione **MBR**
4. **Sistema de destino**: 
   - Para UEFI: Selecione **UEFI (non CSM)**
   - Para BIOS Legacy: Selecione **BIOS (ou UEFI-CSM)**
5. **Sistema de arquivos**: **FAT32** (padrão)
6. **Tamanho do cluster**: **4096 bytes** (padrão)

#### 3.3 Configurações Avançadas

Clique em "MOSTRAR OPÇÕES AVANÇADAS" e configure:

- **Nome do volume**: Arch Linux (opcional)
- **Opções de formatação rápida**: **Marcado**
- **Criar disco bootável usando**: **Imagem ISO**
- **Criar partição estendida**: **Desmarcado**

#### 3.4 Criar o Pendrive

1. Clique em "INICIAR"
2. Se aparecer aviso sobre modo de gravação, escolha: **Gravar em modo de Imagem DD**
3. Confirme que deseja apagar todos os dados do pendrive
4. Aguarde o processo completar (2-5 minutos)
5. Clique em "FECHAR" quando finalizar

---

### Método 2: Ventoy (Alternativa Versátil)

**Ventoy** permite criar um pendrive multiboot, onde você pode ter várias ISOs diferentes.

#### 3.5 Download do Ventoy

1. Acesse: [https://www.ventoy.net/](https://www.ventoy.net/)
2. Baixe a versão para Windows
3. Extraia o arquivo ZIP

#### 3.6 Instalação do Ventoy

1. Execute `Ventoy2Disk.exe` como administrador
2. Selecione seu pendrive
3. Clique em "Install"
4. Confirme a instalação
5. Aguarde finalizar

#### 3.7 Copiar a ISO

1. Após a instalação, o pendrive terá uma partição visível
2. **Simplesmente copie** o arquivo `.iso` do Arch Linux para o pendrive
3. Pronto! O Ventoy detectará automaticamente a ISO

**Vantagens do Ventoy:**
- Suporta múltiplas ISOs no mesmo pendrive
- Não precisa reformatar para adicionar/remover ISOs
- Suporta BIOS e UEFI automaticamente

---

### Método 3: balenaEtcher (Simples e Intuitivo)

#### 3.8 Download do Etcher

1. Acesse: [https://www.balena.io/etcher/](https://www.balena.io/etcher/)
2. Baixe e instale o balenaEtcher

#### 3.9 Criar o Pendrive

1. Abra o balenaEtcher
2. Clique em "Flash from file" e selecione a ISO do Arch Linux
3. Clique em "Select target" e escolha seu pendrive
4. Clique em "Flash!" e aguarde finalizar

---

## 4. Criando Pendrive Bootável no Linux

### Método 1: Comando dd (Linha de Comando)

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
# Substitua 'sdb1' pela partição do SEU pendrive
sudo umount /dev/sdb1

# Se houver múltiplas partições, desmonte todas:
sudo umount /dev/sdb*
```

#### 4.3 Gravar a ISO no Pendrive

```bash
# CUIDADO: Verifique o nome do dispositivo!
# Substitua 'sdb' pelo dispositivo correto
# Substitua o nome da ISO pelo arquivo que você baixou

sudo dd bs=4M if=/caminho/para/archlinux-2025.XX.XX-x86_64.iso of=/dev/sdb status=progress oflag=sync
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
# Montar a partição Ventoy (se não montou automaticamente)
# Copiar a ISO para o pendrive
cp ~/Downloads/archlinux-2025.XX.XX-x86_64.iso /media/Ventoy/
```

---

### Método 3: GNOME Disks (Interface Gráfica)

Para usuários que preferem interface gráfica no Linux:

#### 4.9 Abrir GNOME Disks

```bash
gnome-disks
```

Ou procure por "Discos" no menu de aplicativos.

#### 4.10 Restaurar Imagem

1. Selecione seu pendrive na lista à esquerda
2. Clique no menu (três pontos) no canto superior direito
3. Selecione "Restaurar imagem de disco..."
4. Escolha a ISO do Arch Linux
5. Clique em "Iniciar restauração..."
6. Confirme e aguarde finalizar

---

## 5. Configurando a BIOS/UEFI para Boot

Após criar o pendrive bootável, você precisa configurar seu computador para inicializar pelo pendrive.

### 5.1 Acessar a BIOS/UEFI

**Teclas comuns para acessar:**
- **F2** - Dell, Acer, Lenovo, ASUS
- **F10** - HP
- **F12** - Dell, Lenovo (Boot Menu direto)
- **DEL** - ASUS, MSI, Gigabyte
- **ESC** - HP, Compaq

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

### 5.4 Boot Menu Rápido

Alternativa mais rápida:

1. Reinicie com o pendrive conectado
2. Pressione a tecla de Boot Menu (geralmente **F12** ou **F8**)
3. Selecione o pendrive na lista
4. Pressione Enter

---

## 6. Solução de Problemas

### 6.1 Pendrive não aparece no boot

**Soluções:**
- Verifique se o pendrive está corretamente conectado
- Tente outra porta USB (prefira USB 2.0 para compatibilidade)
- Recrie o pendrive usando outro método
- Verifique se o Secure Boot está desabilitado

### 6.2 Erro "Boot device not found"

**Soluções:**
- Verifique se escolheu a opção correta no Rufus (GPT para UEFI, MBR para BIOS)
- Recrie o pendrive usando "modo DD" no Rufus
- Tente usar outro pendrive

### 6.3 Sistema não inicializa após criar o pendrive

**Soluções:**
- Verifique a integridade da ISO (SHA256)
- Baixe a ISO novamente
- Formate completamente o pendrive antes de criar
- Teste em outro computador

### 6.4 Erro no dd: "Permission denied"

**Solução:**
```bash
# Certifique-se de usar sudo
sudo dd bs=4M if=arquivo.iso of=/dev/sdb status=progress oflag=sync
```

### 6.5 Pendrive fica inutilizável após criar boot

**Como restaurar o pendrive para uso normal:**

**No Windows:**
1. Abra o "Gerenciamento de Disco"
2. Localize o pendrive
3. Delete todas as partições
4. Crie uma nova partição FAT32
5. Formate

**No Linux:**
```bash
# Listar partições
sudo fdisk -l

# Abrir fdisk no dispositivo (ex: /dev/sdb)
sudo fdisk /dev/sdb

# Dentro do fdisk:
# d - deletar todas as partições
# n - criar nova partição (aceite os padrões)
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

*Última atualização: Janeiro 2025*
