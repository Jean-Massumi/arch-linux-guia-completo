# Guia Completo de Instalação do Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **Instalação Base do Sistema**

> **Tempo estimado**: 1-2 horas  
> **Nível de dificuldade**: Intermediário a Avançado

---

## Índice

- [Pré-requisitos](#pré-requisitos)
- [1. Configuração Inicial do Sistema](#1-configuração-inicial-do-sistema)
- [2. Conexão com Internet](#2-conexão-com-internet)
- [3. Preparação do Disco](#3-preparação-do-disco)
- [4. Particionamento do Disco](#4-particionamento-do-disco)
- [5. Formatação das Partições](#5-formatação-das-partições)
- [6. Montagem das Partições](#6-montagem-das-partições)
- [7. Configuração de Mirrors](#7-configuração-de-mirrors-servidores-de-download)
- [8. Instalação do Sistema Base](#8-instalação-do-sistema-base)
- [9. Configuração do Sistema](#9-configuração-do-sistema)
- [10. Configuração de Data e Hora](#10-configuração-de-data-e-hora)
- [11. Configuração de Hostname](#11-configuração-de-hostname)
- [12. Configuração de Usuários](#12-configuração-de-usuários)
- [13. Instalação e Configuração do Bootloader](#13-instalação-e-configuração-do-bootloader)
- [14. Instalação do NetworkManager](#14-instalação-do-networkmanager)
- [15. Instalação de Microcódigo da CPU](#15-instalação-de-microcódigo-da-cpu)
- [16. Finalização da Instalação](#16-finalização-da-instalação)
- [17. Primeiro Boot](#17-primeiro-boot)
- [18. Solução de Problemas Comuns](#18-solução-de-problemas-comuns)
- [19. Próximos Passos](#19-próximos-passos)
- [Apêndices](#apêndices)

---

## Pré-requisitos

- Pendrive bootável com Arch Linux ISO
- Conexão com internet (cabo ethernet ou Wi-Fi)
- Computador com UEFI (modo recomendado)
- Conhecimento básico de linha de comando
- Tempo: 1-2 horas aproximadamente

> **ATENÇÃO**: Este processo apagará todos os dados do disco selecionado. Faça backup de dados importantes!

---

## 1. Configuração Inicial do Sistema

### 1.1 Configurar Layout do Teclado

```bash
loadkeys br-abnt2
```

**O que faz**: Configura o layout do teclado para ABNT2 (padrão brasileiro) durante a instalação.

### 1.2 Verificar Modo de Boot

```bash
ls /sys/firmware/efi/efivars
```

**O que faz**: Verifica se o sistema iniciou em modo UEFI. Se o diretório existir, você está em UEFI (recomendado). Se não existir, está em modo BIOS legacy.

---

## 2. Conexão com Internet

### 2.1 Configurar Wi-Fi (se necessário)

```bash
# Entrar no utilitário de configuração Wi-Fi
iwctl

# Dentro do iwctl, execute os comandos:
device list                           # Lista interfaces de rede disponíveis
station wlan0 scan                    # Escaneia redes Wi-Fi disponíveis
station wlan0 get-networks            # Mostra as redes encontradas
station wlan0 connect "nome_da_rede"  # Conecta à rede (substitua pelo nome real)
exit                                  # Sai do iwctl
```

**O que faz**: Conecta o sistema à rede Wi-Fi. O `wlan0` pode variar (use o nome mostrado em `device list`).

### 2.2 Testar Conexão

```bash
ping -c 3 archlinux.org
```

**O que faz**: Testa se a conexão com internet está funcionando enviando 3 pacotes para o site do Arch Linux.

---

## 3. Preparação do Disco

> **PONTO DE NÃO RETORNO**
> 
> Os próximos comandos trabalharão diretamente com seus discos.
> **Execute `lsblk` antes de CADA comando de formatação.**
> **Um erro aqui pode causar perda total de dados.**

---

## ALERTA CRÍTICO: Verifique Seus Dispositivos Antes de Formatar

Esta é a etapa mais crítica de toda a instalação. Erros aqui podem levar à **perda total de dados** ou a um sistema que não funciona. Leia com atenção!

### 1. O Nome do Disco (`/dev/sda`) NÃO é Fixo

O nome `/dev/sda` usado neste guia é apenas um **exemplo** comum para um primeiro disco SATA (HD ou SSD). O seu pode ser diferente:

* `/dev/sdb`, `/dev/sdc`, etc.: Se for um segundo ou terceiro disco.
* `/dev/nvme0n1`: Se for um SSD do tipo NVMe (muito comum em notebooks modernos).
* `/dev/vda`, `/dev/xvda`: Em ambientes de virtualização.

**Como verificar o nome correto?** Use os comandos da seção 3.1 a seguir. Identifique na lista o disco com o tamanho correspondente ao que você quer instalar o sistema.

### 2. A Numeração das Partições (`1`, `2`, `3`) DEPENDE DE VOCÊ

A numeração `/dev/sda1`, `/dev/sda2`, etc., depende **exatamente** da ordem e das escolhas que você fez no `fdisk` na etapa de particionamento. Se você criar as partições em ordem diferente, a numeração será diferente.

**Antes de executar qualquer comando `mkfs` (formatação), você DEVE verificar sua estrutura.**

### 3. Exemplo de Verificação para o disco `/dev/sda`

Se o seu disco realmente for `/dev/sda` e você seguir os passos de particionamento **exatamente** como descritos neste guia, sua estrutura final deverá ser:

* `/dev/sda1` - Partição EFI - Deve ser formatada em **FAT32**
* `/dev/sda2` - Partição Swap - Deve ser configurada como **Linux Swap**
* `/dev/sda3` - Partição Raiz (/) - Deve ser formatada em **ext4**
* `/dev/sda4` - Partição Home (/home) - Deve ser formatada em **ext4**

**Se o seu disco for `/dev/nvme0n1`, os nomes serão `/dev/nvme0n1p1`, `/dev/nvme0n1p2`, e assim por diante.**

### 4. REGRA DE OURO

**SEMPRE execute `lsblk` e `fdisk -l` ANTES de formatar qualquer partição para confirmar que você está trabalhando com o dispositivo correto!**

---

### 3.1 Identificar Discos Disponíveis

```bash
lsblk
fdisk -l
```

**O que faz**: Lista todos os discos e partições disponíveis no sistema para identificar onde instalar.

**ATENÇÃO**: Anote cuidadosamente o nome do seu disco (ex: `/dev/sda`, `/dev/nvme0n1`) e seu tamanho. Você usará essas informações nos próximos passos.

### 3.2 Sincronizar Horário (IMPORTANTE)

```bash
timedatectl set-ntp true
```

**O que faz**: Sincroniza o relógio do sistema via NTP. **Importante**: Alguns certificados SSL podem falhar se a data/hora estiverem incorretas, causando problemas no download de pacotes.

### 3.3 Limpar Disco (CUIDADO - APAGA TUDO!)

```bash
# Substitua 'sda' pelo seu disco (ex: nvme0n1, sdb, etc.)
wipefs -a /dev/sda
```

**O que faz**: Remove todas as assinaturas de sistema de arquivos e estruturas de partição do disco. **ATENÇÃO**: Isso apaga TODOS os dados!

### 3.4 Verificar Limpeza

```bash
lsblk
fdisk -l /dev/sda
```

**O que faz**: Confirma que o disco foi limpo e não possui partições.

---

## 4. Particionamento do Disco

### 4.1 Escolha Seu Tipo de Instalação

Este guia cobre dois cenários:

**A. Instalação Única (Disco Completo)**
- O Arch Linux será o único sistema operacional
- Todo o disco será usado para o Linux
- Processo mais simples e direto

**B. Dual-Boot com Windows**
- Arch Linux convivendo com Windows
- Requer cuidados especiais com partições
- **Consulte o guia**: [ARCH_DUALBOOT_GUIDE.md](./ARCH_DUALBOOT_GUIDE.md)

> **Para Dual-Boot**: Pule para o guia específico de dual-boot. O processo de particionamento é diferente e requer atenção especial para não danificar o Windows.

### 4.2 Instalação Única - Iniciar Particionamento

```bash
fdisk /dev/sda    # Substitua pelo nome do SEU disco
```

**O que faz**: Abre o utilitário de particionamento para o disco especificado.

### 4.3 Criar Tabela de Partição GPT

```bash
# Dentro do fdisk:
Command (m for help): g
```

**O que faz**: Cria uma nova tabela de partição GPT (GUID Partition Table), necessária para UEFI.

### 4.4 Verificar Espaço Disponível

```bash
# Dentro do fdisk, antes de criar partições:
Command (m for help): p
```

**O que faz**: Mostra o espaço total disponível no disco para você calcular melhor o tamanho das partições.

### 4.5 Recomendações de Tamanho de Partições

| Partição | Tamanho Mínimo | Recomendado | Uso |
|----------|----------------|-------------|-----|
| EFI | 512MB | 1GB | Boot UEFI |
| Swap | Varia | Veja tabela abaixo | Memória virtual |
| Root (/) | 20GB | 40-60GB | Sistema operacional |
| Home | 20GB | Resto do disco | Dados pessoais |

**Recomendações de Swap por Quantidade de RAM:**

| RAM Instalada | Tamanho de Swap Recomendado |
|---------------|----------------------------|
| Menos de 8GB | 2x a quantidade de RAM |
| 8GB a 16GB | Igual à quantidade de RAM |
| Mais de 16GB | RAM/2 (ou 8GB fixo) |

### 4.6 Criar Partições

O usuário decide o tamanho de cada partição do armazenamento.

#### Partição 1 - EFI System (1GB)

```bash
Command (m for help): n           # Criar nova partição
Partition number (1-128): [Enter] # Aceitar padrão (1)
First sector: [Enter]             # Aceitar início padrão
Last sector: +1G                  # Definir tamanho de 1GB 

Command (m for help): t           # Alterar tipo da partição
Partition number: 1               # Selecionar partição 1
Hex code or alias: 1              # Tipo: EFI System
```

#### Partição 2 - Swap (8GB)

```bash
Command (m for help): n           # Criar nova partição
Partition number (2-128): [Enter] # Aceitar padrão (2)
First sector: [Enter]             # Aceitar início padrão
Last sector: +8G                  # Definir tamanho de 8GB

Command (m for help): t           # Alterar tipo da partição
Partition number: 2               # Selecionar partição 2
Hex code or alias: 19             # Tipo: Linux swap
```

#### Partição 3 - Raiz / Root (60GB)

```bash
Command (m for help): n          # Criar nova partição
Partition number (3-128): [Enter] # Aceitar padrão (3)
First sector: [Enter]            # Aceitar início padrão
Last sector: +60G                # Definir tamanho de 60GB
# Tipo já fica como Linux filesystem (padrão)
```

#### Partição 4 - Home (Resto do Disco)

```bash
Command (m for help): n          # Criar nova partição
Partition number (4-128): [Enter] # Aceitar padrão (4)
First sector: [Enter]            # Aceitar início padrão
Last sector: [Enter]             # Usar todo espaço restante
```

### 4.7 Verificar e Salvar Partições

```bash
Command (m for help): p          # Visualizar tabela de partições criada
Command (m for help): w          # Escrever mudanças no disco e sair
```

---

## 5. Formatação das Partições

**ANTES DE FORMATAR**: Execute `lsblk` novamente para confirmar a estrutura das partições criadas!

```bash
# Verificar estrutura atual (OBRIGATÓRIO!)
lsblk

# ADAPTE os nomes das partições para o SEU sistema:
# Se seu disco é /dev/nvme0n1, use /dev/nvme0n1p1, /dev/nvme0n1p2, etc.

# Formatar partição EFI com FAT32
mkfs.fat -F32 /dev/sda1

# Configurar e ativar swap
mkswap /dev/sda2      # Criar área de swap
swapon /dev/sda2      # Ativar swap

# Formatar partição raiz com ext4
mkfs.ext4 /dev/sda3

# Formatar partição home com ext4
mkfs.ext4 /dev/sda4
```

**O que fazem**:
- `mkfs.fat -F32`: Formata a partição EFI com sistema FAT32
- `mkswap/swapon`: Cria e ativa a área de memória virtual (swap)
- `mkfs.ext4`: Formata partições com sistema de arquivos ext4 (padrão Linux)

### 5.1 Verificar Formatação

```bash
# Verificar se todas as partições foram formatadas corretamente
lsblk -f
```

**O que faz**: Lista todas as partições mostrando o tipo de sistema de arquivos, útil para confirmar que a formatação foi bem-sucedida.

---

## 6. Montagem das Partições

**ATENÇÃO**: Use os nomes corretos das suas partições!

```bash
# Montar partição raiz no ponto de montagem principal
mount /dev/sda3 /mnt

# Criar diretórios para os outros pontos de montagem
mkdir -p /mnt/boot/efi    # Diretório para partição EFI
mkdir /mnt/home           # Diretório para partição home

# Montar partição EFI
mount /dev/sda1 /mnt/boot/efi

# Montar partição home
mount /dev/sda4 /mnt/home
```

**O que faz**: Monta as partições nos diretórios corretos para que o sistema possa acessá-las durante a instalação.

### 6.1 Verificar Montagens

```bash
lsblk
mount | grep /mnt
```

**O que fazem**: Verificam se todas as partições foram montadas corretamente nos pontos de montagem esperados.

---

## 7. Configuração de Mirrors (Servidores de Download)

### 7.1 Atualizar Base de Dados de Pacotes

```bash
# Atualizar lista de pacotes disponíveis
pacman -Sy
```

**O que faz**: Sincroniza a base de dados de pacotes com os repositórios remotos.

### 7.2 Configurar Mirrors Otimizados

```bash
# Instalar reflector para otimizar mirrors
pacman -S reflector

# Configurar mirrors brasileiros mais rápidos
reflector --country Brazil --latest 10 --sort rate --verbose --save /etc/pacman.d/mirrorlist
```

**O que faz**: 
- Atualiza a base de dados de pacotes
- Instala o reflector (ferramenta para otimizar mirrors)
- Seleciona os 10 mirrors brasileiros mais rápidos e os salva na configuração

---

## 8. Instalação do Sistema Base

```bash
# Instalar pacotes essenciais do sistema
pacstrap -K /mnt base base-devel linux linux-firmware linux-headers nano vim
```

**O que faz**:
- `base`: Pacotes fundamentais do sistema
- `base-devel`: Ferramentas de desenvolvimento (compiladores, etc.)
- `linux`: Kernel do Linux
- `linux-firmware`: Firmware para hardware
- `linux-headers`: Cabeçalhos do kernel (para módulos)
- `nano/neovim`: Editores de texto

> **Dica**: Para maior estabilidade, você pode usar `linux-lts` (Long Term Support) no lugar de `linux`.

---

## 9. Configuração do Sistema

### 9.1 Gerar Tabela de Sistemas de Arquivos

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**O que faz**: Gera automaticamente o arquivo `/etc/fstab` que define quais partições são montadas na inicialização.

### 9.2 Entrar no Sistema Instalado

```bash
arch-chroot /mnt
```

**O que faz**: Muda para o ambiente do sistema recém-instalado (chroot = change root).

### 9.3 Configurar Localização e Idioma

```bash
# Editar arquivo de locales
nano /etc/locale.gen
# Descomente a linha: pt_BR.UTF-8 UTF-8

# Gerar os locales
locale-gen

# Definir idioma padrão do sistema
echo "LANG=pt_BR.UTF-8" > /etc/locale.conf

# Configurar layout do teclado permanentemente
echo "KEYMAP=br-abnt2" > /etc/vconsole.conf
```

**O que fazem**:
- Define português brasileiro como idioma do sistema
- Configura o teclado ABNT2 permanentemente
- Gera as configurações de localização

### 9.4 Verificar Configurações de Localização

```bash
# Verificar se os locales foram gerados corretamente
locale -a | grep pt_BR

# Verificar variáveis de ambiente de idioma
locale
```

**O que fazem**: Confirmam se as configurações de idioma e localização foram aplicadas corretamente.

---

## 10. Configuração de Data e Hora

```bash
# Definir fuso horário (altere conforme sua região)
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

# Sincronizar relógio do hardware
hwclock --systohc

# Habilitar sincronização automática de horário
timedatectl set-ntp true

# Verificar configuração
timedatectl
```

**O que fazem**:
- Define o fuso horário do sistema
- Sincroniza o relógio do hardware com o do sistema
- Habilita sincronização automática via NTP
- Mostra o status atual da configuração de tempo

---

## 11. Configuração de Hostname

### 11.1 Definir Nome do Computador

```bash
# Substitua "meu-arch" pelo nome desejado
echo "meu-arch" > /etc/hostname
```

### 11.2 Configurar Arquivo Hosts

```bash
nano /etc/hosts

# Adicione as seguintes linhas:
127.0.0.1   localhost
::1         localhost
127.0.1.1   meu-arch.localdomain   meu-arch
```

**O que fazem**:
- Define o nome da máquina na rede
- Configura resolução de nomes locais

---

## 12. Configuração de Usuários

> **IMPORTANTE**: Você criará DOIS usuários com senhas diferentes:
> 1. **Root** - Administrador do sistema (emergências apenas)
> 2. **Seu usuário** - Conta pessoal para uso diário

### 12.1 Definir Senha do Root

```bash
passwd
```

**O que faz**: Define a senha do usuário **root** (administrador). Esta senha será usada apenas em emergências ou manutenção crítica do sistema.

**Exemplo de uso:**
```
New password: [digite uma senha forte]
Retype new password: [digite novamente]
```

### 12.2 Instalar e Configurar Sudo

```bash
# Instalar sudo
pacman -S sudo

# Configurar permissões do sudo
EDITOR=nano visudo
# Descomente a linha: %wheel ALL=(ALL) ALL
```

**O que faz**: Instala o `sudo` que permite que usuários comuns executem comandos administrativos. O `sudo` é mais seguro que usar root diretamente.

### 12.3 Criar Usuário Regular

```bash
# Criar usuário (substitua "seu_usuario" pelo nome desejado)
# Exemplo: useradd -m -g users -G wheel... -s /bin/bash joao
useradd -m -g users -G wheel,storage,power,audio,video,input,render -s /bin/bash seu_usuario

# Definir senha do usuário
passwd seu_usuario
```

**O que fazem**:
- Cria um usuário regular com diretório home
- Adiciona o usuário ao grupo `wheel` (permite usar `sudo`)
- Adiciona a outros grupos necessários (áudio, vídeo, etc.)
- Define bash como shell padrão

**Exemplo de definição de senha:**
```
New password: [digite a senha do SEU usuário]
Retype new password: [digite novamente]
```

### 12.4 Resumo - Duas Senhas Criadas

Ao final desta seção, você terá:

| Usuário | Senha | Quando Usar |
|---------|-------|-------------|
| **root** | Senha 1 (forte) | Emergências, recuperação do sistema |
| **seu_usuario** | Senha 2 (sua escolha) | Uso diário, login normal |

**No dia a dia**: Você fará login com **seu_usuario** e usará `sudo` quando precisar fazer algo administrativo (instalar programas, editar arquivos do sistema, etc.)

---

## 13. Instalação e Configuração do Bootloader

### 13.1 Instalar GRUB

```bash
# Instalar pacotes necessários
pacman -S grub efibootmgr

# Instalar GRUB na partição EFI
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
```

**Para instruções completas de dual-boot**, consulte: [ARCH_DUALBOOT_GUIDE.md](./ARCH_DUALBOOT_GUIDE.md)

### 13.2 Gerar Configuração do GRUB

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

**O que fazem**:
- Instalam e configuram o carregador de boot GRUB
- Detectam outros sistemas operacionais (dual-boot)
- Geram o menu de inicialização

### 13.3 Verificar Instalação do GRUB

```bash
# Verificar se o GRUB foi instalado na partição EFI
efibootmgr -v

# Verificar se o arquivo de configuração foi gerado
ls -la /boot/grub/grub.cfg
```

**O que fazem**: Confirmam se o GRUB foi instalado corretamente e se o arquivo de configuração foi criado.

---

## 14. Instalação do NetworkManager

```bash
# Instalar NetworkManager
pacman -S networkmanager nm-connection-editor

# Habilitar NetworkManager na inicialização
systemctl enable NetworkManager

# Habilitar serviço de otimização para SSDs (se aplicável)
systemctl enable fstrim.timer
```

**O que fazem**:
- Instalam o gerenciador de rede gráfico
- Habilitam os serviços para iniciar automaticamente
- Otimizam SSDs com TRIM automático

### 14.1 Verificar Serviços Habilitados

```bash
# Verificar se os serviços foram habilitados corretamente
systemctl is-enabled NetworkManager
systemctl is-enabled fstrim.timer
```

**O que fazem**: Confirmam se os serviços essenciais foram habilitados para inicialização automática.

---

## 15. Instalação de Microcódigo da CPU

```bash
# Para processadores Intel:
pacman -S intel-ucode

# Para processadores AMD:
pacman -S amd-ucode
```

**O que fazem**: Instalam microcódigo específico da CPU para correções e otimizações de segurança e performance.

**Após instalar o microcódigo, regenere a configuração do GRUB:**

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 16. Finalização da Instalação

```bash
# Sair do ambiente chroot
exit

# Desmontar todas as partições
umount -R /mnt

# Reiniciar o sistema
reboot
```

**O que fazem**:
- Saem do ambiente de instalação
- Desmontam as partições com segurança
- Reiniciam para iniciar o sistema instalado

---

## 17. Primeiro Boot

### O Que Esperar no Primeiro Boot

Após reiniciar, você verá:

1. **Menu do GRUB** - Selecione "Arch Linux"
2. **Tela preta com texto** - Sistema está iniciando
3. **Prompt de login** - Digite seu nome de usuário
4. **Senha** - Digite a senha que você criou
5. **Terminal** - Você está no seu novo Arch Linux!

### Comandos Úteis no Primeiro Boot

```bash
# Testar internet
ping -c 3 archlinux.org

# Conectar WiFi (se necessário)
nmtui

# Atualizar sistema
sudo pacman -Syu

# Verificar espaço em disco
df -h

# Verificar memória
free -h
```

---

## 18. Solução de Problemas Comuns

### 18.1 Se o sistema não inicializar

```bash
# Boot pelo pendrive de instalação e execute:
mount /dev/sda3 /mnt
mount /dev/sda1 /mnt/boot/efi
mount /dev/sda4 /mnt/home
arch-chroot /mnt

# Reinstalar GRUB:
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

### 18.2 Problemas de Rede

```bash
# Após fazer login no sistema instalado:
sudo systemctl status NetworkManager
sudo systemctl start NetworkManager
sudo nmtui  # Interface gráfica para configurar rede
```

### 18.3 Sistema Inicia Mas Não Tem Internet

```bash
# Verificar status do NetworkManager
sudo systemctl status NetworkManager

# Se não estiver rodando, iniciar
sudo systemctl start NetworkManager

# Habilitar para iniciar automaticamente
sudo systemctl enable NetworkManager

# Configurar conexão
nmtui
```

### 18.4 Esqueci a Senha do Root ou Usuário

```bash
# Boot pelo pendrive de instalação
mount /dev/sda3 /mnt
arch-chroot /mnt

# Redefinir senha do root
passwd

# Redefinir senha do usuário
passwd seu_usuario
```

---

## 19. Próximos Passos

### Guia Completo de Pós-Instalação

Para um guia detalhado sobre como configurar seu sistema Arch Linux do zero até um ambiente completo e personalizado, consulte:

**[ARCH_POST_INSTALL.md](./ARCH_POST_INSTALL.md)**

Este guia inclui:
- Otimizações do sistema
- Instalação de utilitários essenciais
- Configuração de AUR helpers
- Instalação de drivers
- Temas e personalizações
- E muito mais!
