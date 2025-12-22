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
- [Início Rápido](#inicio-rapido)
- [Tempo Estimado](#tempo-estimado)
- [Pré-requisitos](#pré-requisitos)
- [Documentação](#documentação)
  - [Preparação](#preparação)
  - [Instalação Base](#instalação-base)
  - [Configurações Avançadas](#configurações-avançadas)
  - [Pós-Instalação](#pós-instalação)
  - [Ambientes Desktop](#ambientes-desktop-completos)
- [FAQ Rápido](#faq-rápido)
- [Como Contribuir](#como-contribuir)
- [Suporte e Recursos](#suporte-e-recursos)
- [Licença](#licença)

---

## Início Rápido

### Passos Comuns a Todas as Instalações

1. Baixe a ISO oficial: [archlinux.org/download](https://archlinux.org/download)
2. Crie o pendrive bootável: [BOOTABLE_USB_GUIDE.md](./BOOTABLE_USB_GUIDE.md)
3. Instalação base: [ARCH_BASE_INSTALL.md](./ARCH_BASE_INSTALL.md)
4. Pós-instalação: [ARCH_POST_INSTALL.md](./ARCH_POST_INSTALL.md)
5. Escolha seu ambiente desktop

### Configurações Adicionais (se aplicável)

- **Dual-Boot com Windows**: Siga [ARCH_DUALBOOT_GUIDE.md](./ARCH_DUALBOOT_GUIDE.md) durante a etapa 3
  - **ATENÇÃO**: Faça backup completo antes de começar
- **Secure Boot**: Configure com [ARCH_SECURE_BOOT_GUIDE.md](./ARCH_SECURE_BOOT_GUIDE.md) após a etapa 3

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

### Requisitos Técnicos
- Conhecimento básico de terminal Linux
- Conexão à internet durante a instalação
- Backup completo de dados importantes
- Pendrive de no mínimo 4GB
- Espaço em disco: Mínimo 20GB (recomendado 60GB+)

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
- [Arch Wiki](https://wiki.archlinux.org/) - Referência principal
- [Guia Oficial de Instalação](https://wiki.archlinux.org/title/Installation_guide)

### Comunidades
- [Fórum Oficial](https://bbs.archlinux.org/)
- [r/archlinux](https://www.reddit.com/r/archlinux/)

**Para problemas**: Consulte primeiro a Arch Wiki, depois use o fórum oficial ou Reddit.

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

## Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](./LICENSE) para mais detalhes.

---

**Última atualização**: Dezembro 2025
