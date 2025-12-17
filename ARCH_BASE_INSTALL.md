# Guia Completo de Instalação do Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

> **Instalação Base do Sistema Arch Linux**

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

### Antes de Formatar - REGRAS CRÍTICAS:

1. **O nome do disco varia**: Pode ser `/dev/sda`, `/dev/nvme0n1`, `/dev/vda`, etc.
2. **A numeração das partições depende da sua criação**: `/dev/sda1`, `/dev/sda2`...
3. **SEMPRE execute `lsblk` antes de formatar** para confirmar o dispositivo correto

### Exemplo de Estrutura Final (se seguir este guia):

**Para disco SATA (/dev/sda):**
- `/dev/sda1` - EFI (FAT32)
- `/dev/sda2` - Swap 
- `/dev/sda3` - Root / (ext4)
- `/dev/sda4` - Home (ext4)

**Para disco NVMe (/dev/nvme0n1):**
- `/dev/nvme0n1p1` - EFI (FAT32)
- `/dev/nvme0n1p2` - Swap
- `/dev/nvme0n1p3` - Root / (ext4)
- `/dev/nvme0n1p4` - Home (ext4)

> **REGRA DE OURO**: Execute `lsblk` antes de cada formatação!

---

### 3.1 Identificar Discos Disponíveis

```bash
lsblk
fdisk -l
```

**O que faz**: Lista todos os discos e partições disponíveis no sistema para identificar onde instalar.

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

┌──────────────────────────────────────────────┐
│ Você tem Windows instalado neste computador? │
└──────────────────────────────────────────────┘
       │                           │
       │                           │
      SIM                         NÃO
       │                           │
       ↓                           ↓
   Dual-Boot                   Instalação
   (Pule para                    Única
   seção 7 e                (Continue aqui)
   veja o guia                  
   específico)

Se você já tem Windows instalado e quer manter os dois sistemas:

1. **Não siga as seções 4 a 6** deste guia
2. **Consulte**: [ARCH_DUALBOOT_GUIDE.md](./ARCH_DUALBOOT_GUIDE.md)

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

### 5.1 (Opcional) Verificar Formatação

```bash
lsblk -f
```

**O que faz**: Lista todas as partições mostrando o tipo de sistema de arquivos, útil para confirmar que a formatação foi bem-sucedida.

---

## 6. Montagem das Partições

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

### 7.1 Configurar Mirrors Otimizados

```bash
# Instalar reflector para otimizar mirrors
pacman -S reflector

# Configurar mirrors brasileiros mais rápidos
reflector --country Brazil --latest 10 --sort rate --verbose --save /etc/pacman.d/mirrorlist
```

**O que faz**: 
- Instala o reflector (ferramenta para otimizar mirrors)
- Seleciona os 10 mirrors brasileiros mais rápidos e os salva na configuração

---

## 8. Instalação do Sistema Base

```bash
# Instalar pacotes essenciais do sistema
pacstrap -K /mnt base base-devel linux linux-firmware linux-headers nano neovim
```

**O que faz**:
- `base`: Pacotes fundamentais do sistema
- `base-devel`: Ferramentas de desenvolvimento (compiladores, etc.)
- `linux`: Kernel do Linux
- `linux-firmware`: Firmware para hardware
- `linux-headers`: Cabeçalhos do kernel (para módulos)
- `nano/neovim`: Editores de texto

> **Dica**: Para maior estabilidade, você pode usar `linux-lts` (Long Term Support) no lugar de `linux`.

### 8.1 Instalar Documentação do Sistema (Recomendado)
```bash
# Instalar páginas de manual e documentação
pacstrap /mnt man-db man-pages texinfo
```

**O que fazem:**
- `man-db`: Sistema de páginas de manual
- `man-pages`: Documentação de comandos Linux
- `texinfo`: Documentação adicional do GNU

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

Você criará dois usuários:
- **root**: Administrador do sistema (apenas para emergências)
- **seu_usuario**: Sua conta pessoal (uso diário)

### 12.1 Definir Senha do Root
````bash
passwd
# Digite uma senha forte quando solicitado
````

### 12.2 Instalar e Configurar Sudo
````bash
# Instalar sudo
pacman -S sudo

# Configurar permissões
EDITOR=nano visudo
# Descomente a linha: %wheel ALL=(ALL) ALL
````

### 12.3 Criar Usuário Regular
````bash
# Substitua "seu_usuario" pelo nome desejado
useradd -m -g users -G wheel,storage,power,audio,video,input,render -s /bin/bash seu_usuario

# Definir senha do usuário
passwd seu_usuario
````

**No uso diário**: Faça login com seu usuário e use `sudo` para tarefas administrativas.

---

## 13. Instalação e Configuração do Bootloader

### 13.1 Instalar GRUB

```bash
# Instalar pacotes necessários
pacman -S grub efibootmgr

# Instalar GRUB na partição EFI
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
```

### 13.2 Gerar Configuração do GRUB

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

**O que fazem**:
- Instalam e configuram o carregador de boot GRUB
- Detectam outros sistemas operacionais (dual-boot)
- Geram o menu de inicialização

### 13.3 (Opcional) Verificar Instalação do GRUB

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

### 14.1 (Opcional) Verificar Serviços Habilitados

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

1. **Menu do GRUB**: Selecione "Arch Linux"
2. **Prompt de login**: Digite seu nome de usuário
3. **Senha**: Digite sua senha
4. **Terminal**: Você está no Arch Linux!

### Comandos Úteis no Primeiro Boot

```bash
# Testar internet
ping -c 3 archlinux.org

# Conectar WiFi (se necessário)
nmtui

# Atualizar sistema
sudo pacman -Syu
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
