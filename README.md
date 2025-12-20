# Manual Completo de Instalação do Arch Linux

<div align="center">
  <img src="https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png" alt="Arch Linux" width="400"/>
  
  ![Status](https://img.shields.io/badge/Status-Atualizado_Dez_2025-green?style=for-the-badge)
  ![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)
  ![Portuguese](https://img.shields.io/badge/Idioma-Português-green?style=for-the-badge)
</div>

> **LEIA PRIMEIRO**: [**AVISO DE RESPONSABILIDADE IMPORTANTE**](./DISCLAIMER.md) - Leia antes de prosseguir!

> **IMPORTANTE**: Este manual é atualizado regularmente e testado para 2025. Devido à natureza rolling release do Arch Linux, alguns comandos podem mudar no futuro. Sempre consulte a [documentação oficial](https://wiki.archlinux.org/) para verificar atualizações.

> **Tempo estimado de leitura**: 5 minutos  
> **Tempo estimado de instalação completa**: 2-4 horas

---

## Índice
- [Quick Start](#quick-start)
- [Fluxo de Instalação](#fluxo-de-instalação)
- [Tempo Estimado](#tempo-estimado)
- [Pré-requisitos](#pré-requisitos)
- [Documentação](#documentação)
  - [Preparação](#preparação)
  - [Instalação Base](#instalação-base)
  - [Configurações Avançadas](#configurações-avançadas)
  - [Pós-Instalação](#pós-instalação)
  - [Ambientes Desktop](#ambientes-desktop-completos)
- [FAQ Rápido](#faq-rápido)
- [Contribuições](#contribuições)
- [Suporte e Recursos](#suporte-e-recursos)

---

## Quick Start

### Instalação Padrão (Sistema Único)

```
1. Baixe a ISO oficial: archlinux.org/download
2. Crie o pendrive bootável: BOOTABLE_USB_GUIDE.md
3. Instalação base: ARCH_BASE_INSTALL.md
4. [OPCIONAL] Secure Boot: ARCH_SECURE_BOOT_GUIDE.md
5. Pós-instalação: ARCH_POST_INSTALL.md
6. Escolha seu ambiente desktop
```

### Instalação Dual-Boot com Windows

```
1. ATENÇÃO: Faça backup completo dos seus dados
2. Baixe a ISO oficial: archlinux.org/download
3. Crie o pendrive bootável: BOOTABLE_USB_GUIDE.md
4. Instalação base + Dual-Boot: ARCH_BASE_INSTALL.md + ARCH_DUALBOOT_GUIDE.md
5. [OPCIONAL] Secure Boot: ARCH_SECURE_BOOT_GUIDE.md
6. Pós-instalação: ARCH_POST_INSTALL.md
7. Escolha seu ambiente desktop
```

---

## Tempo Estimado

| Etapa | Tempo | Observação |
|-------|-------|------------|
| **Preparação** | 30 min | Download da ISO e criação do pendrive |
| **Instalação Base** | 30-60 min | Particionamento, instalação do sistema |
| **Dual-Boot** (opcional) | +15-30 min | Apenas se instalar junto com Windows |
| **Secure Boot** (opcional) | +15-30 min | Configuração de segurança avançada |
| **Pós-Instalação** | 30-60 min | Drivers, ferramentas essenciais |
| **Ambiente Desktop** | 30-90 min | GNOME/KDE/Hyprland + customização |
| **TOTAL** | **2-4 horas** | Varia conforme experiência e opções escolhidas |

---

## Pré-requisitos

### Nível de Conhecimento
- **Iniciante**: Pode seguir o guia, mas requer atenção aos detalhes
- **Intermediário**: Ideal para quem já usou Linux antes
- **Avançado**: Terá facilidade com os procedimentos

### Requisitos Técnicos
Antes de começar, certifique-se de ter:

- **Conhecimento básico** de terminal Linux
- **Conexão à internet** durante a instalação
- **Backup completo** de dados importantes (especialmente para dual-boot)
- **Pendrive** de no mínimo 4GB
- **Espaço em disco**: Mínimo 20GB (recomendado 60GB+)
- **Paciência** e disposição para aprender

---

## Documentação

### Preparação

**[BOOTABLE_USB_GUIDE.md](./BOOTABLE_USB_GUIDE.md)** - Como criar pendrive bootável

**Conteúdo:**
- Guia para Windows (Rufus, Balena Etcher)
- Guia para Linux (dd, Ventoy)
- Verificação de integridade da ISO

---

### Instalação Base

**[ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md)** - Instalação base do Arch Linux (OBRIGATÓRIO)

**Conteúdo:**
- Preparação e particionamento do disco
- Instalação do sistema base
- Configuração de bootloader (GRUB)
- Configuração inicial do sistema (idioma, fuso horário, usuários)
- Solução de problemas comuns

**IMPORTANTE**: Este é o guia principal de instalação. Todos os usuários devem seguir este documento primeiro.

---

**[ARCH_DUALBOOT_GUIDE.md](./ARCH_DUALBOOT_GUIDE.md)** - Complemento para Dual-Boot com Windows (OPCIONAL)

**Conteúdo:**
- Preparação do Windows
- Redimensionamento de partições
- Particionamento para dual-boot
- Configuração do GRUB para detectar Windows
- Troubleshooting específico de dual-boot

**Quando usar**: Este guia é um COMPLEMENTO do ARCH_BASE_INSTALL.md. Use apenas se você vai instalar o Arch Linux junto com o Windows. As instruções de dual-boot devem ser seguidas DURANTE a instalação base, não depois.

---

### Configurações Avançadas

**[ARCH_SECURE_BOOT_GUIDE.md](./ARCH_SECURE_BOOT_GUIDE.md)** - Configurar Secure Boot (OPCIONAL)

**Conteúdo:**
- O que é Secure Boot e quando usar
- Configuração com sbctl (método recomendado)
- Dual-boot com Secure Boot
- Troubleshooting completo

**Quando configurar**: 
- APÓS a instalação base estar completa e funcionando
- ANTES de instalar drivers e ambientes desktop (pós-instalação)
- Configure agora e teste o boot antes de prosseguir

**Por que antes da pós-instalação?**
- Permite testar o boot sem perder tempo com reinstalação
- Configuração mais limpa e menos propensa a erros

**Você pode pular esta etapa**: O sistema funciona perfeitamente sem Secure Boot. Configure apenas se tiver necessidade específica de segurança ou dual-boot com Windows 11.

---

### Pós-Instalação

**[ARCH_POST_INSTALL.md](./ARCH_POST_INSTALL.md)** - Configuração completa do ambiente (OBRIGATÓRIO)

**Conteúdo:**
- Ferramentas essenciais do sistema
- Otimizações e configurações de performance
- Preparação para ambiente desktop

**[GPU_DRIVERS_GUIDE.md](./GPU_DRIVERS_GUIDE.md)** - Instalação de drivers de vídeo (RECOMENDADO)

**Conteúdo:**
- Drivers Intel (integrated graphics)
- Drivers AMD (open-source e AMDGPU)
- Drivers NVIDIA (proprietary e open-source)
- Vulkan e aceleração de hardware
- Troubleshooting de drivers de vídeo

**IMPORTANTE**: Configure os drivers de GPU apropriados para seu hardware antes de instalar ambientes desktop para evitar problemas de performance e compatibilidade.

---

### Ambientes Desktop Completos

**[GNOME_COMPLETE_SETUP.md](./GNOME_COMPLETE_SETUP.md)** - Setup GNOME otimizado e completo
- Interface moderna e intuitiva
- Melhor para iniciantes
- Integração completa com Wayland

**[KDE_COMPLETE_SETUP.md](./KDE_COMPLETE_SETUP.md)** - Setup KDE Plasma completo
- Altamente customizável
- Visual moderno e polido
- Rico em recursos e configurações

**[HYPRLAND_COMPLETE_SETUP.md](./HYPRLAND_COMPLETE_SETUP.md)** - Setup Hyprland (Wayland compositor)
- Gerenciador de janelas tiling
- Extremamente leve e rápido
- Requer conhecimento intermediário

---

## FAQ Rápido

<details>
<summary><b>Quanto tempo leva a instalação?</b></summary>

Entre 2 a 4 horas no total, dependendo da sua experiência, velocidade da internet e ambiente desktop escolhido.
</details>

<details>
<summary><b>Posso instalar junto com Windows (dual-boot)?</b></summary>

Sim! O guia ARCH_DUALBOOT_GUIDE.md é um complemento do ARCH_BASE_INSTALL.md. Pontos críticos:
- Faça backup completo antes de começar
- Desabilite Fast Boot e Secure Boot na BIOS
- Desabilite BitLocker se estiver ativo
- Reserve no mínimo 60GB para o Arch Linux
- Siga as instruções de dual-boot DURANTE a instalação base
</details>

<details>
<summary><b>Preciso de internet durante a instalação?</b></summary>

Sim, a instalação baixa pacotes diretamente dos repositórios oficiais. Certifique-se de ter uma conexão estável.
</details>

<details>
<summary><b>Qual ambiente desktop devo escolher?</b></summary>

- Iniciantes: GNOME (mais simples e intuitivo)
- Customização: KDE Plasma (infinitas opções de personalização)
- Performance: Hyprland (leve e moderno, mas requer mais conhecimento)
</details>

<details>
<summary><b>Posso mudar de ambiente desktop depois?</b></summary>

Sim! Você pode instalar múltiplos ambientes e alternar entre eles no login.
</details>

<details>
<summary><b>Preciso configurar Secure Boot?</b></summary>

Não é obrigatório. O sistema funciona perfeitamente sem Secure Boot. Configure apenas se:
- Você tem necessidade específica de segurança
- Quer manter a mesma configuração de segurança do Windows
- Está confortável com procedimentos avançados

Se decidir configurar, faça ANTES da pós-instalação para evitar problemas.

Consulte: ARCH_SECURE_BOOT_GUIDE.md
</details>

<details>
<summary><b>Por que configurar Secure Boot antes da pós-instalação?</b></summary>

Configurar Secure Boot antes de instalar drivers e ambientes desktop:
- Evita conflitos com drivers de GPU que podem não funcionar com Secure Boot mal configurado
- Permite testar se o sistema boota corretamente antes de investir tempo na configuração completa
- Facilita o troubleshooting caso algo dê errado
- Evita necessidade de reinstalação completa do sistema
</details>

<details>
<summary><b>O Arch Linux é difícil?</b></summary>

O Arch tem a reputação de ser difícil, mas com este guia passo a passo, qualquer pessoa com conhecimento básico de Linux pode instalá-lo. A dificuldade está mais na quantidade de decisões a tomar do que na complexidade técnica em si.
</details>

<details>
<summary><b>Por que escolher Arch Linux?</b></summary>

- Controle total: Você decide o que instalar
- Sempre atualizado: Rolling release com últimas versões
- Documentação excelente: Arch Wiki é a melhor referência
- Aprendizado: Você entende como o sistema funciona
- Performance: Sistema leve sem bloatware
</details>

---

## Contribuições

Contribuições são bem-vindas! Sinta-se livre para:

- Reportar erros ou problemas encontrados
- Sugerir melhorias nos guias
- Adicionar traduções para outros idiomas
- Criar pull requests com correções ou novos conteúdos
- Dar uma estrela no projeto se ele foi útil para você

---

## Suporte e Recursos

### Documentação Oficial
- **Arch Wiki**: [wiki.archlinux.org](https://wiki.archlinux.org/) - A bíblia do Arch Linux
- **Installation Guide**: [Guia Oficial](https://wiki.archlinux.org/title/Installation_guide)

### Comunidades
- **Fórum Oficial**: [Arch Linux Forums](https://bbs.archlinux.org/)
- **Reddit**: [r/archlinux](https://www.reddit.com/r/archlinux/)
- **Telegram**: Grupos brasileiros de Linux e Arch Linux

### Ajuda Adicional
Este manual não inclui suporte técnico oficial. Para problemas específicos:
1. Consulte a Arch Wiki (90% dos problemas estão documentados lá)
2. Use o fórum oficial para perguntas complexas
3. Pesquise no Reddit para experiências de outros usuários

---

## Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](./LICENSE) para mais detalhes.

---

**Última atualização**: Dezembro 2025
