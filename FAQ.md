# FAQ - Perguntas Frequentes

## Índice
- [Antes da Instalação](#antes-da-instalação)
- [Durante a Instalação](#durante-a-instalação)
- [Dual-Boot](#dual-boot)
- [Secure Boot](#secure-boot)
- [Pós-Instalação](#pós-instalação)
- [Sobre o Arch Linux](#sobre-o-arch-linux)

---

## Antes da Instalação

<details>
<summary><b>Quanto tempo leva a instalação completa?</b></summary>

Entre 2 a 5 horas, dependendo de:
- Sua experiência com Linux
- Velocidade da internet
- Ambiente desktop escolhido
- Configurações opcionais (dual-boot, Secure Boot, otimizações)
</details>

<details>
<summary><b>Quais são os requisitos mínimos de hardware?</b></summary>

**Mínimo:**
- Processador: x86_64 (64 bits)
- RAM: 4GB (recomendado 8GB+)
- Disco: 120GB (recomendado 250GB+)
- Internet durante a instalação

**Para ambientes desktop modernos (GNOME/KDE):**
- RAM: 8GB+ recomendado
- Disco: 120GB+
</details>

<details>
<summary><b>Preciso de internet durante a instalação?</b></summary>

Sim, é obrigatório. O Arch baixa todos os pacotes diretamente dos repositórios durante a instalação. Certifique-se de ter uma conexão estável.
</details>

<details>
<summary><b>Posso instalar o Arch em uma máquina virtual primeiro?</b></summary>

Sim! É altamente recomendado para iniciantes. Use VirtualBox ou VMware para praticar antes de instalar no hardware real.
</details>

<details>
<summary><b>Devo fazer backup antes de instalar?</b></summary>

**SIM!** Sempre faça backup completo, especialmente em dual-boot. O particionamento pode causar perda de dados se feito incorretamente.
</details>

---

## Durante a Instalação

<details>
<summary><b>Qual ambiente desktop devo escolher?</b></summary>

**GNOME:** Moderno, intuitivo, melhor para iniciantes
**KDE Plasma:** Altamente customizável, rico em recursos
**Hyprland:** Leve e rápido, requer conhecimento intermediário

Você pode instalar múltiplos e alternar entre eles.
</details>

<details>
<summary><b>UEFI ou BIOS? Qual escolher?</b></summary>

Depende do seu hardware:
- Computadores modernos (2012+): Geralmente UEFI
- Computadores antigos: BIOS Legacy

Para verificar: `ls /sys/firmware/efi/efivars`
- Se mostrar arquivos: UEFI
- Se der erro: BIOS
</details>

<details>
<summary><b>Qual esquema de particionamento usar?</b></summary>

**Para UEFI:**
- EFI System Partition (ESP): 512MB-1GB (FAT32)
- Root (/): 30GB+ (ext4 ou btrfs)
- Swap: Tamanho da RAM (se hibernar) ou 4-8GB
- Home (/home): Resto do espaço

**Para BIOS:**
- Root (/): 30GB+
- Swap: 4-8GB
- Home (/home): Resto do espaço
</details>

<details>
<summary><b>Encontrei erro ao instalar pacotes, o que fazer?</b></summary>

Soluções comuns:
1. Atualize os mirrors: `reflector --latest 10 --sort rate --save /etc/pacman.d/mirrorlist`
2. Sincronize bancos de dados: `pacman -Syy`
3. Atualize keyrings: `pacman -S archlinux-keyring`
</details>

---

## Dual-Boot

<details>
<summary><b>Posso instalar junto com Windows (dual-boot)?</b></summary>

Sim! Pontos importantes:
- Faça backup completo antes
- Desabilite Fast Boot no Windows
- Desabilite Secure Boot na BIOS (ou configure-o depois)
- Desabilite BitLocker se ativo
- Reserve no mínimo 120GB para o Arch
- Siga ARCH_DUALBOOT_GUIDE.md DURANTE a instalação base
</details>

<details>
<summary><b>O GRUB não detecta o Windows, o que fazer?</b></summary>

Execute:
```bash
sudo pacman -S os-prober
sudo os-prober
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Se ainda não funcionar, consulte TROUBLESHOOTING.md
</details>

<details>
<summary><b>Posso instalar o Arch depois do Windows?</b></summary>

Sim, é o método recomendado. Instale sempre o Windows primeiro, depois o Arch. O GRUB detectará o Windows automaticamente.
</details>

---

## Secure Boot

<details>
<summary><b>Preciso configurar Secure Boot?</b></summary>

Não é obrigatório. Configure apenas se:
- Você tem necessidade específica de segurança
- Quer manter dual-boot com Windows 11 (que exige Secure Boot)
- Está confortável com procedimentos avançados

O sistema funciona perfeitamente sem Secure Boot.
</details>

<details>
<summary><b>Quando configurar Secure Boot?</b></summary>

Configure APÓS a instalação base estar funcionando e ANTES de instalar drivers e ambientes desktop. Isso facilita troubleshooting e evita conflitos.
</details>

<details>
<summary><b>Secure Boot funciona com drivers NVIDIA?</b></summary>

Sim, mas requer assinatura dos módulos do kernel. O guia ARCH_SECURE_BOOT_GUIDE.md cobre este processo.
</details>

---

## Pós-Instalação

<details>
<summary><b>O sistema não tem internet após instalação, o que fazer?</b></summary>

**Para WiFi:**
```bash
nmcli device wifi list
nmcli device wifi connect "NOME_REDE" password "SENHA"
```

**Para Ethernet:**
```bash
sudo systemctl enable --now NetworkManager
```
</details>

<details>
<summary><b>Posso mudar de ambiente desktop depois?</b></summary>

Sim! Você pode instalar múltiplos ambientes e alternar no login. Para remover um ambiente antigo, desinstale seus pacotes com `pacman -Rns`.
</details>

<details>
<summary><b>Como atualizar o sistema?</b></summary>
```bash
sudo pacman -Syu
```

Faça isso regularmente. O Arch é rolling release, então sempre há atualizações.
</details>

<details>
<summary><b>Devo instalar um AUR helper?</b></summary>

Recomendado: `yay` ou `paru`. Facilitam a instalação de pacotes do AUR.
```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
</details>

---

## Sobre o Arch Linux

<details>
<summary><b>O Arch Linux é difícil?</b></summary>

O Arch tem reputação de ser difícil, mas com guias passo a passo, qualquer pessoa com conhecimento básico de Linux pode instalá-lo. A dificuldade está mais na quantidade de decisões do que na complexidade técnica.
</details>

<details>
<summary><b>Por que escolher Arch Linux?</b></summary>

- Controle total sobre o sistema
- Rolling release: sempre atualizado
- Arch Wiki: melhor documentação do mundo Linux
- Aprendizado: você entende como o sistema funciona
- Performance: sistema leve sem bloatware
- Comunidade ativa e prestativa
</details>

<details>
<summary><b>O que é "rolling release"?</b></summary>

Significa que você recebe atualizações contínuas sem precisar reinstalar o sistema. Não existem "versões" como Ubuntu 22.04, 24.04. Você instala uma vez e atualiza sempre.
</details>

<details>
<summary><b>Preciso reinstalar o Arch periodicamente?</b></summary>

Não! Devido ao modelo rolling release, você mantém o sistema sempre atualizado com `pacman -Syu`. Muitos usuários têm instalações com anos sem reinstalação.
</details>
