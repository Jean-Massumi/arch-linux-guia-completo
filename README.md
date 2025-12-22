# Manual Completo de Instalação do Arch Linux

<div align="center">
  <img src="https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png" alt="Arch Linux" width="400"/>
  
  ![Status](https://img.shields.io/badge/Status-Atualizado_Dez_2025-green?style=for-the-badge)
  ![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)
  ![Portuguese](https://img.shields.io/badge/Idioma-Português-green?style=for-the-badge)
</div>

## Sobre

Guia completo e atualizado para instalação do Arch Linux em português brasileiro. Criado para tornar o processo acessível desde iniciantes até usuários avançados, com instruções detalhadas e troubleshooting.

> **LEIA PRIMEIRO**: [**AVISO DE RESPONSABILIDADE IMPORTANTE**](./DISCLAIMER.md) - Leia antes de prosseguir!

> **IMPORTANTE**: Este manual é atualizado regularmente e testado para 2025. Devido à natureza rolling release do Arch Linux, alguns comandos podem mudar no futuro. Sempre consulte a [documentação oficial](https://wiki.archlinux.org/) para verificar atualizações.

> **Tempo estimado de leitura**: 5 minutos  
> **Tempo estimado de instalação completa**: 2-4 horas

---

## Índice
- [Tempo Estimado](#tempo-estimado)
- [Pré-requisitos](#pré-requisitos)
- [Documentação](#documentação)
  - [Preparação](#preparação)
  - [Instalação Base](#instalação-base)
  - [Configurações Avançadas](#configurações-avançadas)
  - [Pós-Instalação](#pós-instalação)
  - [Ambientes Desktop](#ambientes-desktop-completos)
  - [Aplicativos Recomendados](#aplicativos-recomendados)
  - [Otimização e Performance](#otimização-e-performance)
  - [Gaming](#gaming)
- [FAQ](#faq)
- [Troubleshooting](#troubleshooting)
- [Como Contribuir](#como-contribuir)
- [Suporte e Recursos](#suporte-e-recursos)
- [Licença](#licença)

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
| **Aplicativos e Otimização** (opcional) | +30-60 min | Instalação de apps e melhorias de performance |
| **TOTAL** | **2-5 horas** | Varia conforme experiência e opções escolhidas |

---

## Pré-requisitos

### Requisitos Técnicos
- Conhecimento básico de terminal Linux
- Conexão à internet durante a instalação
- Backup completo de dados importantes
- Pendrive de no mínimo 4GB
- Espaço em disco: Mínimo 120GB (recomendado 250GB+)

---

## Documentação

### Preparação

**[BOOTABLE_USB_GUIDE.md](./BOOTABLE_USB_GUIDE.md)** - Criação do pendrive bootável
- Download e verificação da ISO
- Guias para Windows e Linux
- Configuração da BIOS/UEFI

---

### Instalação Base

**[ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md)** - Instalação base (OBRIGATÓRIO)

Este é o guia principal. Todos devem começar por aqui.

**[ARCH_DUALBOOT_GUIDE.md](./ARCH_DUALBOOT_GUIDE.md)** - Dual-Boot com Windows (OPCIONAL)

Complemento do guia base. Use apenas se for instalar junto com Windows.

---

### Configurações Avançadas

**[ARCH_SECURE_BOOT_GUIDE.md](./ARCH_SECURE_BOOT_GUIDE.md)** - Secure Boot (OPCIONAL)

Configure APÓS a instalação base funcionar e ANTES da pós-instalação.

---

### Pós-Instalação

**[ARCH_POST_INSTALL.md](./ARCH_POST_INSTALL.md)** - Configuração do ambiente (OBRIGATÓRIO)

**[GPU_DRIVERS_GUIDE.md](./GPU_DRIVERS_GUIDE.md)** - Drivers de vídeo (RECOMENDADO)

Configure antes de instalar ambientes desktop.

---

### Ambientes Desktop Completos

**[GNOME_COMPLETE_SETUP.md](./GNOME_COMPLETE_SETUP.md)** - Interface moderna e intuitiva (recomendado para iniciantes)

**[KDE_COMPLETE_SETUP.md](./KDE_COMPLETE_SETUP.md)** - Altamente customizável

**[HYPRLAND_COMPLETE_SETUP.md](./HYPRLAND_COMPLETE_SETUP.md)** - Tiling window manager (requer conhecimento intermediário)

---

### Aplicativos Recomendados

**[RECOMMENDED_APPS.md](./RECOMMENDED_APPS.md)** - Lista de aplicativos essenciais

Navegadores, comunicação, desenvolvimento, multimídia, produtividade etc.

---

### Otimização e Performance

**[OPTIMIZATION_GUIDE.md](./OPTIMIZATION_GUIDE.md)** - Melhorias de performance (OPCIONAL)

Configurações para otimizar o sistema: swap, zram, systemd services etc

---

### Gaming

**[GAMING_SETUP.md](./GAMING_SETUP.md)** - Configuração para jogos (OPCIONAL)

Steam, Proton, Lutris e otimizações para gaming.

---

## FAQ

Dúvidas frequentes sobre instalação, dual-boot, Secure Boot e mais.

Consulte: [FAQ.md](./FAQ.md)

---

## Troubleshooting

Problemas comuns e soluções para instalação, boot, drivers e sistema.

Consulte: [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)

---

## Como Contribuir

Contribuições são bem-vindas:

- Reporte erros abrindo uma issue
- Sugira melhorias via pull request
- Dê uma estrela se o guia foi útil

Para contribuir com código:
1. Fork o repositório
2. Crie uma branch com sua modificação
3. Envie um pull request com descrição clara

---

## Suporte e Recursos

### Documentação Oficial
- [Arch Wiki](https://wiki.archlinux.org/) - Referência principal
- [Guia Oficial de Instalação](https://wiki.archlinux.org/title/Installation_guide)

### Comunidades
- [Fórum Oficial](https://bbs.archlinux.org/)
- [r/archlinux](https://www.reddit.com/r/archlinux/)

**Para problemas**: Consulte primeiro a Arch Wiki, depois use o fórum oficial ou Reddit.

---

## Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](./LICENSE.md) para mais detalhes.

---

**Última atualização**: Dezembro 2025
