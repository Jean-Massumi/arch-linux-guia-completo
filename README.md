# Manual Completo de Instalação do Arch Linux

<div align="center">
  <img src="https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png" alt="Arch Linux" width="400"/>
  
  ![Status](https://img.shields.io/badge/Status-Atualizado_2026-green?style=for-the-badge)
  ![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)
  ![Portuguese](https://img.shields.io/badge/Idioma-Português-green?style=for-the-badge)
</div>

> **LEIA PRIMEIRO**: [**AVISO DE RESPONSABILIDADE IMPORTANTE**](./DISCLAIMER.md) - Leia antes de prosseguir!

> **IMPORTANTE**: Este manual está em **constante evolução** e é atualizado regularmente por mim (e futuramente pela comunidade). Os procedimentos descritos foram testados e funcionam perfeitamente para **2026**. No entanto, devido à natureza rolling release do Arch Linux, **alguns comandos podem se tornar obsoletos em anos futuros**. Sempre consulte a [documentação oficial](https://wiki.archlinux.org/) para verificar mudanças recentes.

>  **Tempo estimado de leitura**: 5 minutos  
>  **Tempo estimado de instalação completa**: 1-2 horas

---

## Índice
- [Quick Start](#quick-start)
- [Pré-requisitos](#pré-requisitos)
- [Documentação](#documentação)
  - [Preparação](#preparação)
  - [Instalação Base](#instalação-base)
  - [Pós-Instalação](#pós-instalação)
  - [Ambientes Desktop](#ambientes-desktop-completos)
- [FAQ Rápido](#faq-rápido)
- [Contribuições](#contribuições)
- [Suporte e Recursos](#suporte-e-recursos)

---

## **Quick Start**
```bash
# 1. Baixe a ISO do Arch Linux
# 2. Crie um pendrive bootável (veja BOOTABLE_USB_GUIDE.md)
# 3. Siga o guia: ARCH_BASE_INSTALL.md
# 4. Após a instalação base: ARCH_POST_INSTALL.md
# 5. Escolha seu ambiente desktop
```

---

## Pré-requisitos
Antes de começar, certifique-se de ter:

- **Conhecimento básico** de terminal Linux
- **Conexão à internet** durante a instalação
- **Backup** de dados importantes
- **Pendrive** de no mínimo 4GB
- **Paciência** e disposição para aprender

---

## **Documentação**

### **Preparação**
**[BOOTABLE_USB_GUIDE.md](./BOOTABLE_USB_GUIDE.md)** - Como criar pendrive bootável
- Guia para Windows
- Guia para Linux

---

### **Instalação Base**
**[ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md)** - Guia completo de instalação do sistema base

**Conteúdo:**
- Preparação e particionamento do disco
- Instalação do sistema base
- Configuração de bootloader (GRUB)
- Configuração inicial do sistema
- Solução de problemas comuns

---

### **Pós-Instalação**
**[ARCH_POST_INSTALL.md](./ARCH_POST_INSTALL.md)** - Configuração completa do ambiente

---

### **Ambientes Desktop Completos**

**[GNOME_COMPLETE_SETUP.md](./GNOME_COMPLETE_SETUP.md)** - Setup GNOME otimizado e completo

**[KDE_COMPLETE_SETUP.md](./KDE_COMPLETE_SETUP.md)** - Setup KDE Plasma completo

**[HYPRLAND_COMPLETE_SETUP.md](./HYPRLAND_COMPLETE_SETUP.md)** - Setup Hyprland (Wayland compositor)

---

## FAQ Rápido

<details>
<summary><b>Quanto tempo leva a instalação?</b></summary>

Entre 30 minutos a 2 horas, dependendo da sua experiência, velocidade da internet e ambiente desktop escolhido.
</details>

<details>
<summary><b>Posso instalar junto com Windows (dual-boot)?</b></summary>

Sim! Veja a seção específica de dual-boot em [ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md).
</details>

<details>
<summary><b>Preciso de internet durante a instalação?</b></summary>

Sim, a instalação baixa pacotes diretamente dos repositórios oficiais. Certifique-se de ter uma conexão estável.
</details>

<details>
<summary><b>Qual ambiente desktop devo escolher?</b></summary>

- **Iniciantes**: GNOME (mais simples e intuitivo)
- **Customização**: KDE Plasma (infinitas opções)
- **Performance**: Hyprland (leve e moderno, mas requer mais conhecimento)
</details>

<details>
<summary><b>Posso mudar de ambiente desktop depois?</b></summary>

Sim! Você pode instalar múltiplos ambientes e alternar entre eles no login.
</details>

---

## **Contribuições**

Contribuições são bem-vindas! Sinta-se livre para:

-  **Reportar erros** ou problemas encontrados
-  **Sugerir melhorias** nos guias
-  **Adicionar traduções** para outros idiomas
-  **Criar pull requests** com correções ou novos conteúdos
-  **Dar uma estrela** no projeto se ele foi útil para você

---

## **Suporte e Recursos**

### Documentação Oficial
- **Arch Wiki**: [wiki.archlinux.org](https://wiki.archlinux.org/) - A bíblia do Arch Linux
- **Installation Guide**: [Guia Oficial](https://wiki.archlinux.org/title/Installation_guide)

### Comunidades
- **Fórum Oficial**: [Arch Linux Forums](https://bbs.archlinux.org/)
- **Reddit**: [r/archlinux](https://www.reddit.com/r/archlinux/)

---

## Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](./LICENSE) para mais detalhes.
