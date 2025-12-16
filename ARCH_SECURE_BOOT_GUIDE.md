# Guia de Secure Boot para Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![Security](https://img.shields.io/badge/Security-FF0000?style=for-the-badge&logo=security&logoColor=white)

> **Reabilite o Secure Boot no seu dual-boot Arch + Windows**

---

## Índice

- [O Que é Secure Boot](#o-que-é-secure-boot)
- [Pré-requisitos](#pré-requisitos)
- [Método 1: Manter Desabilitado](#método-1-manter-desabilitado)
- [Método 2: Shim + PreLoader](#método-2-shim--preloader-recomendado)
- [Método 3: Assinar com Próprias Chaves](#método-3-assinar-com-próprias-chaves-avançado)
- [Verificação e Testes](#verificação-e-testes)
- [Solução de Problemas](#solução-de-problemas)

---

## O Que é Secure Boot

**Secure Boot** é uma funcionalidade UEFI que verifica assinaturas digitais de bootloaders e kernels antes de executá-los, impedindo malware de infectar o processo de boot.

### Como Funciona

1. UEFI contém chaves públicas confiáveis (geralmente da Microsoft)
2. Bootloader precisa estar assinado com chave correspondente
3. Se assinatura não bater, UEFI bloqueia o boot

### No Dual-Boot

- **Windows**: Já vem assinado pela Microsoft
- **Arch Linux**: GRUB padrão não é assinado
- **Solução**: Usar shim assinado pela Microsoft ou assinar você mesmo

---

## Pré-requisitos

- Arch Linux + Windows dual-boot funcionando
- Secure Boot atualmente **DESABILITADO**
- Acesso à BIOS/UEFI
- Backup da configuração atual

**IMPORTANTE**: Configure o Secure Boot SOMENTE após o dual-boot estar funcionando perfeitamente.

---

## Método 1: Manter Desabilitado

### Quando Usar

- Primeira instalação do Arch
- Não há necessidade específica de Secure Boot
- Quer simplicidade máxima

### Segurança

O sistema é **seguro** sem Secure Boot se você:
- Mantém sistema atualizado
- Usa senhas fortes
- Não deixa computador desprotegido fisicamente

**Secure Boot protege contra**: Malware que infecta o bootloader (raro em desktop doméstico)

**Secure Boot NÃO protege contra**: Senhas fracas, exploits de software, acesso físico não autorizado

---

## Método 2: Shim + PreLoader

### O Que é Shim

**Shim** é um bootloader intermediário assinado pela Microsoft que carrega o GRUB. Como é assinado pela Microsoft, funciona com as chaves já presentes na sua UEFI (do Windows).

### Vantagens

- Usa chaves Microsoft existentes (não precisa criar suas próprias)
- Compatível com Windows out-of-the-box
- Processo relativamente simples
- Suficiente para 95% dos casos

### Instalação Passo a Passo

#### 2.1 Instalar Pacotes Necessários

```bash
sudo pacman -S shim-signed sbsigntools efibootmgr
```

#### 2.2 Backup do EFI Atual

```bash
# Criar backup da configuração atual
sudo cp -r /boot/efi/EFI/GRUB /boot/efi/EFI/GRUB.backup
```

#### 2.3 Copiar Shim para Partição EFI

```bash
# Copiar shim e MokManager
sudo cp /usr/share/shim-signed/shimx64.efi /boot/efi/EFI/GRUB/
sudo cp /usr/share/shim-signed/mmx64.efi /boot/efi/EFI/GRUB/

# Copiar GRUB para ser carregado pelo shim
sudo cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/GRUB/grubx64.efi.original
```

#### 2.4 Registrar Shim no UEFI

```bash
# Verificar entradas atuais
efibootmgr -v

# Criar nova entrada para shim (ajuste /dev/sda e --part 2 conforme seu sistema)
sudo efibootmgr --create --disk /dev/sda --part 2 \
  --loader /EFI/GRUB/shimx64.efi \
  --label "Arch Linux (Secure Boot)" --unicode

# Verificar se foi criada
efibootmgr -v
```

#### 2.5 Configurar Ordem de Boot

```bash
# Listar entradas (anote o número da entrada do Shim, ex: 0000)
efibootmgr -v

# Colocar Shim como primeira opção (substitua 0000 pelo número correto)
sudo efibootmgr -o 0000,0001,0002
```

#### 2.6 Reabilitar Secure Boot na BIOS

1. Reinicie o computador
2. Entre na BIOS/UEFI (F2, DEL, F10)
3. Procure "Secure Boot"
4. Mude para **"Enabled"**
5. Salve e reinicie (F10)

#### 2.7 Primeiro Boot com Secure Boot

Ao iniciar, você verá o **menu do GRUB normal**. Se funcionar, pronto!

**Se aparecer tela azul do MOK Manager** (Machine Owner Key):
1. Selecione "Enroll MOK"
2. Selecione "Continue"
3. Selecione "Yes"
4. Sistema reiniciará e bootará normalmente

### Atualizações do Sistema

O shim continua funcionando automaticamente após atualizações do kernel. Não precisa refazer nada.

**Exceção**: Se reinstalar o GRUB, repita os passos 2.3 a 2.5.

---

## Método 3: Assinar com Próprias Chaves (AVANÇADO)

### Quando Usar

- Você precisa de controle total sobre o processo de boot
- Trabalha com segurança da informação
- Usa criptografia LUKS
- Tem experiência com certificados digitais

### Avisos

- Processo complexo e pode travar o sistema se errado
- Requer manutenção constante (assinar após cada atualização de kernel)
- Pode causar incompatibilidade com Windows em algumas BIOS

### Instalação Passo a Passo

#### 3.1 Instalar Ferramentas

```bash
sudo pacman -S sbsigntools efitools
```

#### 3.2 Gerar Suas Chaves

```bash
# Criar diretório para chaves
mkdir -p ~/secure-boot-keys
cd ~/secure-boot-keys

# Gerar chaves (PK, KEK, db)
# Platform Key (PK)
openssl req -newkey rsa:4096 -nodes -keyout PK.key -new -x509 -sha256 -days 3650 -subj "/CN=Minha PK/" -out PK.crt
openssl x509 -outform DER -in PK.crt -out PK.cer
cert-to-efi-sig-list -g "$(uuidgen)" PK.crt PK.esl
sign-efi-sig-list -g "$(uuidgen)" -k PK.key -c PK.crt PK PK.esl PK.auth

# Key Exchange Key (KEK)
openssl req -newkey rsa:4096 -nodes -keyout KEK.key -new -x509 -sha256 -days 3650 -subj "/CN=Minha KEK/" -out KEK.crt
openssl x509 -outform DER -in KEK.crt -out KEK.cer
cert-to-efi-sig-list -g "$(uuidgen)" KEK.crt KEK.esl
sign-efi-sig-list -g "$(uuidgen)" -a -k PK.key -c PK.crt KEK KEK.esl KEK.auth

# Database Key (db)
openssl req -newkey rsa:4096 -nodes -keyout db.key -new -x509 -sha256 -days 3650 -subj "/CN=Minha db/" -out db.crt
openssl x509 -outform DER -in db.crt -out db.cer
cert-to-efi-sig-list -g "$(uuidgen)" db.crt db.esl
sign-efi-sig-list -g "$(uuidgen)" -a -k KEK.key -c KEK.crt db db.esl db.auth
```

#### 3.3 Backup das Chaves Microsoft (IMPORTANTE)

```bash
# Extrair chaves Microsoft atuais (para manter Windows funcionando)
efi-readvar -v PK -o old_PK.esl
efi-readvar -v KEK -o old_KEK.esl
efi-readvar -v db -o old_db.esl

# Mesclar suas chaves com as da Microsoft
cat old_db.esl db.esl > combined_db.esl
sign-efi-sig-list -g "$(uuidgen)" -k KEK.key -c KEK.crt db combined_db.esl combined_db.auth
```

#### 3.4 Matricular Chaves na UEFI

**ATENÇÃO**: Este passo é irreversível sem acesso físico à BIOS.

```bash
# Copiar arquivos .auth para EFI
sudo cp *.auth /boot/efi/

# Reiniciar no modo Setup da UEFI
sudo systemctl reboot --firmware-setup

# Na BIOS:
# 1. Entrar em "Secure Boot"
# 2. Entrar em "Key Management"
# 3. Selecionar "Install Custom Keys" ou similar
# 4. Navegar até /boot/efi/ e instalar:
#    - PK.auth
#    - KEK.auth
#    - combined_db.auth (ou db.auth se não usar Windows)
```

#### 3.5 Assinar Kernel e Bootloader

```bash
# Assinar GRUB
sudo sbsign --key ~/secure-boot-keys/db.key --cert ~/secure-boot-keys/db.crt \
  --output /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/GRUB/grubx64.efi

# Assinar kernel
sudo sbsign --key ~/secure-boot-keys/db.key --cert ~/secure-boot-keys/db.crt \
  --output /boot/vmlinuz-linux /boot/vmlinuz-linux
```

#### 3.6 Automatizar Assinatura em Atualizações

Criar hook do pacman:

```bash
sudo nano /etc/pacman.d/hooks/999-sign_kernel_for_secureboot.hook
```

Conteúdo:

```ini
[Trigger]
Operation = Install
Operation = Upgrade
Type = Package
Target = linux
Target = linux-lts
Target = linux-hardened

[Action]
Description = Signing kernel with Machine Owner Key for Secure Boot
When = PostTransaction
Exec = /usr/bin/find /boot -maxdepth 1 -name 'vmlinuz-*' -exec /usr/bin/sh -c 'if ! /usr/bin/sbverify --list {} 2>/dev/null | /usr/bin/grep -q "signature certificates"; then /usr/bin/sbsign --key /root/secure-boot-keys/db.key --cert /root/secure-boot-keys/db.crt --output {} {}; fi' ;
Depends = sbsigntools
```

```bash
# Mover chaves para /root (mais seguro)
sudo mkdir -p /root/secure-boot-keys
sudo cp ~/secure-boot-keys/db.key ~/secure-boot-keys/db.crt /root/secure-boot-keys/
sudo chmod -R 600 /root/secure-boot-keys
```

#### 3.7 Habilitar Secure Boot

1. Reinicie
2. Entre na BIOS
3. Habilite "Secure Boot"
4. Salve e reinicie

---

## Verificação e Testes

### Verificar Status do Secure Boot

```bash
# Ver se Secure Boot está ativo
bootctl status

# Alternativa
mokutil --sb-state

# Verificar assinaturas
sbverify --list /boot/vmlinuz-linux
```

**Saída esperada**:
```
Secure Boot: enabled
```

### Testar Ambos os Sistemas

1. **Boot no Arch Linux**: Deve funcionar normalmente
2. **Boot no Windows**: Deve funcionar normalmente
3. **Alternar várias vezes**: Garantir estabilidade

Se ambos funcionam, configuração bem-sucedida!

---

## Solução de Problemas

### Sistema Não Boota Após Habilitar Secure Boot

**Causa**: Bootloader não está assinado corretamente.

**Solução**:

1. Entre na BIOS
2. Desabilite Secure Boot temporariamente
3. Boot pelo Arch
4. Verifique assinaturas:
   ```bash
   sbverify --list /boot/efi/EFI/GRUB/shimx64.efi  # Método 2
   # OU
   sbverify --list /boot/vmlinuz-linux  # Método 3
   ```
5. Se não houver assinatura, refaça o processo

### Windows Não Aparece no Menu do GRUB

**Solução**:

```bash
sudo pacman -S os-prober
sudo nano /etc/default/grub
# Adicionar: GRUB_DISABLE_OS_PROBER=false

sudo grub-mkconfig -o /boot/grub/grub.cfg

# Se usando Método 3, reassinar GRUB
sudo sbsign --key /root/secure-boot-keys/db.key \
  --cert /root/secure-boot-keys/db.crt \
  --output /boot/efi/EFI/GRUB/grubx64.efi \
  /boot/efi/EFI/GRUB/grubx64.efi
```

### Tela Azul "Verification Failed"

**Causa**: Assinatura não confiada pela UEFI.

**Método 2 (Shim)**:
- Isso é normal no primeiro boot
- Siga processo de MOK Manager (seção 2.7)

**Método 3 (Próprias chaves)**:
- Verifique se chaves foram matriculadas corretamente na UEFI
- Pode precisar reinstalar chaves pelo modo Setup

### Kernel Não Assina Após Atualização (Método 3)

**Solução**:

```bash
# Verificar se hook está ativo
ls -la /etc/pacman.d/hooks/

# Assinar manualmente
sudo sbsign --key /root/secure-boot-keys/db.key \
  --cert /root/secure-boot-keys/db.crt \
  --output /boot/vmlinuz-linux /boot/vmlinuz-linux

# Verificar
sbverify --list /boot/vmlinuz-linux
```

### Resetar Secure Boot (Voltar ao Padrão)

**Se algo der errado e você quiser resetar tudo**:

1. Boot na BIOS/UEFI
2. Procure "Restore Factory Keys" ou "Reset to Setup Mode"
3. Confirme
4. Isso restaura as chaves Microsoft originais
5. Windows voltará a funcionar
6. Arch só funcionará com Secure Boot desabilitado (refaça Método 2 se quiser)

---

## Comparação dos Métodos

| Aspecto | Desabilitado | Shim (Método 2) | Próprias Chaves (Método 3) |
|---------|--------------|-----------------|---------------------------|
| **Complexidade** | Nenhuma | Baixa | Alta |
| **Segurança** | Boa | Muito Boa | Excelente |
| **Manutenção** | Zero | Mínima | Constante |
| **Compatibilidade Windows** | 100% | 100% | 95% |
| **Risco** | Zero | Baixo | Médio |
| **Recomendado para** | Iniciantes | Maioria dos usuários | Especialistas |

---

## Recursos Adicionais

- **Arch Wiki - Secure Boot**: [wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot)
- **Rod Smith's Secure Boot Guide**: [rodsbooks.com/efi-bootloaders/secureboot.html](http://www.rodsbooks.com/efi-bootloaders/secureboot.html)
- **Shim Documentation**: [github.com/rhboot/shim](https://github.com/rhboot/shim)

---

**Lembre-se**: Secure Boot é **opcional**. Seu sistema é seguro sem ele se você seguir boas práticas de segurança básicas.

*Última atualização: Dezembro 2025*
