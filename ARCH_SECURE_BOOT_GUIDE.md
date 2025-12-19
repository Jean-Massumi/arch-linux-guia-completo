# Guia de Secure Boot para Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![Security](https://img.shields.io/badge/Security-FF0000?style=for-the-badge&logo=security&logoColor=white)

> **Habilite o Secure Boot no Arch Linux de forma simples e automatizada**

---

## Decisão Rápida

```
Quer Secure Boot?
│
├─ SIM → Use sbctl
│  ├─ Só Arch: sudo sbctl enroll-keys
│  └─ Dual-boot: sudo sbctl enroll-keys -m
│
└─ NÃO → Deixe desabilitado
   └─ Configure depois se quiser
```

---

## O Que é Secure Boot

Secure Boot verifica assinaturas digitais de bootloaders e kernels antes de executá-los, impedindo malware de infectar o processo de boot.

**Você precisa?**
- Dual-boot com Windows 11: Provavelmente sim
- Computador em ambiente inseguro: Recomendado
- Desktop doméstico com senhas fortes: Opcional

**Protege contra:** Malware de bootloader, modificação física do sistema  
**NÃO protege contra:** Senhas fracas, exploits de rede, dados não criptografados

---

## Pré-requisitos

**IMPORTANTE:** A imagem oficial de instalação do Arch NÃO suporta Secure Boot. Você DEVE instalar o Arch com Secure Boot DESABILITADO e só habilitar após a instalação estar completa e funcionando.

```bash
# 1. Verificar se é UEFI (não BIOS)
[ -d /sys/firmware/efi ] && echo "OK - Sistema UEFI" || echo "ERRO - Sistema BIOS"

# 2. Secure Boot deve estar DESABILITADO (habilita no final)
bootctl status | grep "Secure Boot"
# Deve mostrar: disabled

# 3. Backup (importante!)
# Copia partição EFI (restauração se algo der errado)
sudo cp -r /boot/efi /boot/efi.backup
# Salva entradas de boot atuais
efibootmgr -v > ~/boot-backup.txt
```

### AVISO: GPU Dedicada e CPUs sem Gráficos Integrados

**CRÍTICO se você tem:**
- Placa de vídeo dedicada (Nvidia, AMD)
- CPU sem gráficos integrados (ex: Ryzen sem "G", Intel F-series)
- Placa de rede para boot PXE

Algumas placas de vídeo têm firmware assinado pelas chaves Microsoft. Se você remover as chaves Microsoft (usar `enroll-keys` sem `-m`), a GPU pode não funcionar ANTES do sistema operacional carregar.

**Sintomas:**
- Tela preta ao ligar o PC
- Não consegue acessar BIOS
- GPU só funciona após sistema iniciar

**Solução:** SEMPRE use a flag `-m` se você tem GPU dedicada ou CPU sem gráficos integrados:

```bash
sudo sbctl enroll-keys -m  # Mantém chaves Microsoft
```

---

## Método 1: Deixar Desabilitado

**Quando usar:** Primeira instalação, não precisa de Secure Boot agora, quer simplicidade.

**Configuração:** Nenhuma! Sistema já funciona assim por padrão.

Seu sistema é seguro sem Secure Boot se você usa senhas fortes, mantém sistema atualizado e tem criptografia LUKS.

---

## Método 2: sbctl 

**Compatibilidade:** O sbctl funciona na maioria dos PCs modernos, mas alguns fabricantes têm firmwares problemáticos. Se encontrar erros persistentes, pode ser limitação do hardware. Ferramenta oficial do Arch Wiki

Este guia tem DOIS caminhos diferentes:
- **Caminho A**: Apenas Linux (sem Windows)
- **Caminho B**: Dual boot (Windows + Linux) - VOCÊ PROVAVELMENTE QUER ESTE!

**Escolha o seu caminho e siga APENAS ele!**

---

### CAMINHO A: Apenas Linux (sem Windows)

### Passo 1: Limpar chaves na BIOS
1. Entre na BIOS/UEFI
2. Procure "Secure Boot"
3. Escolha "Clear Keys" ou "Delete All Keys"
4. Salve e saia

#### Passo 2: Instalar e verificar
```bash
# Instalar sbctl
sudo pacman -S sbctl

# Verificar status
sbctl status
# Setup Mode DEVE estar: Enabled
```

#### Passo 3: Criar e matricular chaves
```bash
# Criar suas próprias chaves
sudo sbctl create-keys

# Matricular chaves (SEM -m, pois não tem Windows)
sudo sbctl enroll-keys

# Para notebooks de marca (Dell, HP, Lenovo, Framework):
# sudo sbctl enroll-keys --firmware-builtin
# ou: sudo sbctl enroll-keys -f
```

#### Passo 4: Assinar arquivos - Pule para "Assinatura de Arquivos"

---

### CAMINHO B: Dual Boot (Windows + Linux)

#### Passo 1: NÃO limpe as chaves!
**MANTENHA as chaves Microsoft! O Windows precisa delas!**
- Setup Mode ficará "Disabled" - Isso é NORMAL para dual boot!

#### Passo 2: Instalar e verificar
```bash
# Instalar sbctl
sudo pacman -S sbctl

# Verificar status
sbctl status
# Setup Mode estará: Disabled (NORMAL para dual boot!)
```

#### Passo 3: Criar e matricular chaves
```bash
# Criar suas próprias chaves
sudo sbctl create-keys

# Matricular chaves MANTENDO as da Microsoft (-m é OBRIGATÓRIO!)
sudo sbctl enroll-keys --microsoft
# ou forma abreviada:
sudo sbctl enroll-keys -m

# Para notebooks de marca + dual boot:
sudo sbctl enroll-keys --microsoft --firmware-builtin
# ou: sudo sbctl enroll-keys -m -f
```

#### Passo 4: Assinar arquivos - Continue para "Assinatura de Arquivos"

---

### Assinatura de Arquivos (AMBOS OS CAMINHOS)

### Descobrir configuração do sistema:
```bash
# Ver onde EFI está montado
mount | grep -i efi
# Se mostrar /boot/efi -> use /boot/efi/ nos comandos
# Se mostrar apenas /boot -> use /boot/ nos comandos

# Ver qual bootloader está instalado
ls /boot/efi/EFI/  # ou ls /boot/EFI/
# GRUB/ -> use OPÇÃO A
# systemd/ -> use OPÇÃO B
```

#### OPÇÃO A: GRUB (comum em dual boot)
```bash
# Assinar bootloader GRUB
sudo sbctl sign -s /boot/efi/EFI/GRUB/grubx64.efi

# Assinar kernel e initramfs (IMPORTANTES!)
sudo sbctl sign -s /boot/vmlinuz-linux
sudo sbctl sign -s /boot/initramfs-linux.img
sudo sbctl sign -s /boot/initramfs-linux-fallback.img

# Verificar se shim/MOK existem (se dual boot com Windows pode ter):
ls /boot/efi/EFI/GRUB/shimx64.efi 2>/dev/null && echo "shimx64.efi existe"
ls /boot/efi/EFI/GRUB/mmx64.efi 2>/dev/null && echo "mmx64.efi existe"

# Se os comandos acima mostraram que existem, assine-os:
# sudo sbctl sign -s /boot/efi/EFI/GRUB/shimx64.efi
# sudo sbctl sign -s /boot/efi/EFI/GRUB/mmx64.efi
```

#### OPÇÃO B: systemd-boot
```bash
# Assinar bootloader systemd
sudo sbctl sign -s /boot/efi/EFI/systemd/systemd-bootx64.efi
sudo sbctl sign -s /boot/efi/EFI/BOOT/BOOTX64.EFI

# Assinar kernel e initramfs (IMPORTANTES!)
sudo sbctl sign -s /boot/vmlinuz-linux
sudo sbctl sign -s /boot/initramfs-linux.img
sudo sbctl sign -s /boot/initramfs-linux-fallback.img
```

---

## Verificação e Ativação
```bash
# SEMPRE verifique antes de reiniciar!
sudo sbctl verify

# Saída esperada: todos com ✓ Signed
# Se algo mostrar ✗ Not signed:
# sudo sbctl sign -s /caminho/do/arquivo/nao-assinado
```

### Habilitar Secure Boot:

1. Reinicie o computador
2. Entre na BIOS/UEFI
3. Habilite "Secure Boot"
4. Salve e reinicie

### Verificar se funcionou:
```bash
sbctl status
# Deve mostrar: Secure Boot: enabled
```

---

## Automação de Assinatura (após atualizações do kernel)

Configure o sbctl para assinar automaticamente após cada atualização:
```bash
# Habilitar hooks do pacman
sudo sbctl generate-bundles

# Ou manualmente criar hook
sudo nano /etc/pacman.d/hooks/99-sbctl.hook
```

Conteúdo do arquivo:
```
[Trigger]
Operation = Install
Operation = Upgrade
Type = Path
Target = boot/vmlinuz-*
Target = usr/lib/modules/*/vmlinuz
Target = boot/*-ucode.img

[Action]
Description = Signing kernel and bootloader with sbctl
When = PostTransaction
Exec = /usr/bin/sbctl sign-all
```

Verificar se funcionou:
```bash
# Atualizar sistema para testar
sudo pacman -Syu

# Verificar se foi assinado automaticamente
sbctl verify
```

---

## Manutenção Manual (se não configurou automação)

Toda vez que atualizar o kernel, assine novamente:
```bash
sudo sbctl sign -s /boot/vmlinuz-linux
sudo sbctl sign -s /boot/initramfs-linux.img
sudo sbctl sign -s /boot/initramfs-linux-fallback.img

# Verificar
sudo sbctl verify
```

### Pronto!

Atualizações de kernel serão assinadas automaticamente. Zero manutenção.

---

**Explicação das flags:**

| Comando | Chaves Matriculadas | Windows funciona? | Updates OEM? |
|---------|---------------------|-------------------|--------------|
| `enroll-keys` | Só suas | NÃO | NÃO |
| `enroll-keys -m` | Suas + Microsoft | SIM | NÃO |
| `enroll-keys -m -f` | Suas + Microsoft + OEM | SIM | SIM |

**As chaves são permanentes:** Atualizações do sistema (pacman -Syu) NÃO afetam as chaves na UEFI. Windows continua funcionando indefinidamente.

---

## Solução de Problemas

### Setup Mode está Disabled

Entre na BIOS → Procure "Clear Secure Boot Keys" ou "Reset to Setup Mode" → Execute → Tente novamente

### Erro "You need to chattr -i files in efivarfs"

Se ao tentar `sbctl enroll-keys` aparecer este erro, os arquivos da UEFI estão protegidos:

```bash
# Remover proteção dos arquivos
sudo chattr -i /sys/firmware/efi/efivars/{PK,KEK,db}*

# Tentar novamente
sudo sbctl enroll-keys -m
```

### Sistema não boota após habilitar Secure Boot

1. Entre na BIOS e desabilite Secure Boot temporariamente
2. Boot no Arch
3. Verifique: `sbctl verify`
4. Reassine arquivos faltando: `sudo sbctl sign -s /caminho/arquivo`
5. Tente habilitar novamente

### Windows não aparece no GRUB

```bash
sudo pacman -S os-prober
sudo nano /etc/default/grub
# Adicionar: GRUB_DISABLE_OS_PROBER=false
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo sbctl sign -s /boot/efi/EFI/GRUB/grubx64.efi
reboot
```

### Windows não boota (dual-boot)

Você esqueceu a flag `-m`. Refaça o processo:

```bash
sudo rm -rf /usr/share/secureboot/keys
sudo sbctl create-keys
sudo sbctl enroll-keys -m  # Desta vez COM -m
sudo sbctl sign -s /boot/efi/EFI/GRUB/grubx64.efi
sudo sbctl sign -s /boot/vmlinuz-linux
```

### Resetar tudo

Entre na BIOS → "Restore Factory Keys" → Confirme → Windows volta a funcionar, Arch precisa Secure Boot desabilitado

---

## Verificação

```bash
# Verificar status
sbctl status
bootctl status | grep "Secure Boot"

# Listar arquivos assinados
sbctl list-files

# Verificar assinatura específica
sbctl verify /boot/vmlinuz-linux
```

---

## FAQ Rápido

**Posso voltar atrás?** Sim, desabilite na BIOS ou restaure chaves de fábrica.

**E se perder as chaves?** Sistema atual funciona, mas não pode assinar novos kernels. Desabilite Secure Boot ou reconfigure.

**Preciso reassinar após atualização?** Não! Hook automático faz isso.

**Múltiplos kernels?** Assine cada um: `sudo sbctl sign -s /boot/vmlinuz-linux-lts`

**Muitos arquivos para assinar?** Use este comando para assinar tudo automaticamente:
```bash
# Assina automaticamente todos os arquivos não assinados
# Opção 1 (simples, funciona na maioria dos casos):
sbctl verify | sed 's/✗ /sbctl sign -s /e'

# Opção 2 (mais robusta, funciona com qualquer caminho):
sbctl verify | sed -E 's|^.* (/.+) is not signed$|sbctl sign -s "\1"|e'
```

---

## Recursos

- [Arch Wiki - Secure Boot](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot)
- [sbctl GitHub](https://github.com/Foxboron/sbctl)

---

**Conclusão:** Secure Boot com sbctl é configuração única, manutenção zero. Se não precisa agora, deixe desabilitado e configure quando quiser.
