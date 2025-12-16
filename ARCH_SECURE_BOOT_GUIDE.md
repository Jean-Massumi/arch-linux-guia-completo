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

## Método 2: sbctl (Recomendado)

Ferramenta oficial do Arch Wiki. Configuração única, assinatura automática de kernels.

### Instalação Completa

```bash
# 1. Instalar
sudo pacman -S sbctl

# 2. Verificar status
sbctl status
# Setup Mode deve estar: Enabled
# Se não estiver, limpe as chaves na BIOS primeiro

# 3. Criar chaves
sudo sbctl create-keys

# 4. Matricular chaves
# ESCOLHA UM (leia as explicações abaixo):

# Opção A: Só Arch (mais seguro)
sudo sbctl enroll-keys

# Opção B: Dual-boot com Windows (mantém chaves Microsoft)
sudo sbctl enroll-keys --microsoft
# ou de forma abreviada:
sudo sbctl enroll-keys -m

# Opção C: Dual-boot + Notebook de marca (Framework, Dell, HP, Lenovo)
# Mantém chaves Microsoft + certificados OEM (para atualizações de firmware)
sudo sbctl enroll-keys --microsoft --firmware-builtin
# ou de forma abreviada:
sudo sbctl enroll-keys -m -f

# 5. Assinar bootloader e kernel
# Para GRUB:
sudo sbctl sign -s /boot/efi/EFI/GRUB/grubx64.efi
sudo sbctl sign -s /boot/vmlinuz-linux

# Para systemd-boot:
sudo sbctl sign -s /boot/efi/EFI/systemd/systemd-bootx64.efi
sudo sbctl sign -s /boot/efi/EFI/BOOT/BOOTX64.EFI
sudo sbctl sign -s /boot/vmlinuz-linux

# 6. Verificar se tudo está assinado (IMPORTANTE!)
sbctl verify
# Saída esperada: Todos os arquivos com ✓ (assinados)
# Se aparecer ✗ (não assinado), assine antes de continuar:
# sudo sbctl sign -s /caminho/do/arquivo

# 7. Agora SIM: Reinicie e habilite Secure Boot na BIOS

# 8. Após boot, verificar se Secure Boot está ativo
sbctl status
# Deve mostrar: Secure Boot: enabled

# Verificação adicional
bootctl status | grep "Secure Boot"
# Deve mostrar: Secure Boot: enabled

# Ou verificar diretamente no kernel
dmesg | grep -i "secure boot"
# Deve mostrar: secureboot: Secure boot enabled
```

### Pronto!

Atualizações de kernel serão assinadas automaticamente. Zero manutenção.

---

## Dual-Boot: IMPORTANTE

**Se você tem Windows, use a flag `-m` (ou `--microsoft`):**

```bash
# Forma abreviada (mais comum)
sudo sbctl enroll-keys -m

# Forma completa (mesmo efeito)
sudo sbctl enroll-keys --microsoft
```

**Se você tem notebook de marca (Framework, Dell, HP, Lenovo):**

```bash
# Adicione também -f para manter certificados OEM
sudo sbctl enroll-keys -m -f
```

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
sbctl verify | sed 's/✗ /sbctl sign -s /e'
```

---

## Recursos

- [Arch Wiki - Secure Boot](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot)
- [sbctl GitHub](https://github.com/Foxboron/sbctl)

---

**Conclusão:** Secure Boot com sbctl é configuração única, manutenção zero. Se não precisa agora, deixe desabilitado e configure quando quiser.
