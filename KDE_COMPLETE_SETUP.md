# Guia Completo de Instalação do KDE Plasma no Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![KDE](https://img.shields.io/badge/KDE-1D99F3?style=for-the-badge&logo=kde&logoColor=white)

> **Instalação completa do ambiente desktop KDE Plasma**

---

## Pré-requisito

Este guia assume que você completou o [Guia Essencial de Pós-Instalação](./ARCH_POST_INSTALL.md).

---

## Índice

- [1. Instalação do KDE Plasma](#1-instalação-do-kde-plasma)
- [2. Aplicações KDE](#2-aplicações-kde)
- [3. Temas e Ícones](#3-temas-e-ícones)
- [4. Ativação e Primeiro Boot](#4-ativação-e-primeiro-boot)
- [5. Configurações Essenciais](#5-configurações-essenciais)
- [6. Atalhos de Teclado](#6-atalhos-de-teclado)
- [7. Solução de Problemas](#7-solução-de-problemas)
- [8. Verificação Final](#8-verificação-final)

---

## 1. Instalação do KDE Plasma

### 1.1 Plasma Base

```bash
# Grupo KDE Plasma completo
sudo pacman -S plasma
```

### 1.2 Aplicações KDE Essenciais

```bash
# Suite completa de aplicações KDE
sudo pacman -S kde-applications
```

---

## 3. Temas e Ícones

```bash
# Ícones modernos
sudo pacman -S papirus-icon-theme

# Motor de temas Qt (para mais opções de personalização)
sudo pacman -S kvantum
```

**Aplicar temas:**
1. Abra **Configurações do Sistema**
2. Vá em **Aparência** → **Aparência Global**
3. Escolha temas, ícones e cores

**Baixar mais temas:**
- **Configurações do Sistema** → **Aparência** → botão **Obter Novos...**
- Ou visite: [store.kde.org](https://store.kde.org)

---

## 4. Ativação e Primeiro Boot

### 4.1 Habilitar SDDM

```bash
# Habilitar SDDM para iniciar automaticamente
sudo systemctl enable sddm

# Reiniciar o sistema
sudo reboot
```

### 4.2 Primeiro Boot

Após reiniciar:

1. **Tela de login (SDDM)**
   - Digite sua senha
   - Clique no ícone de **sessão** (canto inferior esquerdo):
     - **Plasma (Wayland)** - padrão, mais moderno
     - **Plasma (X11)** - mais estável

2. **Desktop KDE Plasma**
   - Interface familiar estilo Windows
   - Clique no **Lançador de Aplicações** (canto inferior esquerdo)

---

## 5. Configurações Essenciais

### 5.1 Abrir Configurações do Sistema

```bash
systemsettings
```

### 5.2 Configurações Recomendadas

**Aparência:**
- **Aparência Global**: Escolher tema (Breeze Light/Dark)
- **Estilo de Aplicação**: Estilo dos widgets
- **Decorações de Janela**: Bordas das janelas
- **Ícones**: Trocar conjunto de ícones
- **Cursores**: Estilo do cursor

**Área de Trabalho:**
- **Comportamento da Área de Trabalho**: Efeitos, cantos ativos
- **Gerenciamento de Janelas**: Comportamento de janelas
- **Atalhos**: Configurar atalhos de teclado

**Personalização:**
- **Notificações**: Gerenciar notificações de apps
- **Gerenciamento de Energia**: Perfis (AC/Bateria)
- **Inicialização e Desligamento**: Apps ao iniciar

**Hardware:**
- **Dispositivos de Entrada**: Mouse, touchpad, teclado
- **Telas**: Configuração de monitores múltiplos
- **Energia**: Economia de energia e suspensão

### 5.3 Personalizar Painel

**Editar Painel:**
1. Clique direito no painel
2. Selecione **Editar Painel**
3. Ajuste altura, posição
4. Adicione/remova widgets
5. Clique em **Concluído**

**Adicionar Widgets:**
1. Clique direito na área de trabalho
2. Selecione **Adicionar Widgets**
3. Arraste widgets para o painel ou desktop

### 5.4 Efeitos de Desktop

**Habilitar/Desabilitar efeitos:**
1. **Configurações do Sistema** → **Comportamento da Área de Trabalho** → **Efeitos de Desktop**
2. Escolha efeitos desejados:
   - **Animações de janelas**
   - **Desktop Cube** (visual)
   - **Minimizar/Maximizar animados**
   - **Transparência e blur**

**Atalho rápido**: `Alt + Shift + F12` desabilita todos os efeitos temporariamente

---

## 6. Atalhos de Teclado

### 6.1 Atalhos Essenciais

| Atalho | Ação |
|--------|------|
| `Meta` (Windows) | Abrir lançador de aplicações |
| `Alt + F2` | Executar comando (KRunner) |
| `Ctrl + Alt + L` | Bloquear tela |
| `Alt + Tab` | Alternar entre janelas |
| `Ctrl + Esc` | Gerenciador de tarefas |
| `Alt + F1` | Menu de aplicações |

### 6.2 Gerenciamento de Janelas

| Atalho | Ação |
|--------|------|
| `Alt + F3` | Menu da janela |
| `Alt + F4` | Fechar janela |
| `Meta + ↑` | Maximizar janela |
| `Meta + ↓` | Minimizar janela |
| `Meta + ←` | Janela metade esquerda |
| `Meta + →` | Janela metade direita |

### 6.3 Áreas de Trabalho Virtuais

| Atalho | Ação |
|--------|------|
| `Ctrl + F1-F12` | Trocar entre áreas de trabalho |
| `Meta + Tab` | Visão geral de áreas de trabalho |

### 6.4 Captura de Tela

| Atalho | Ação |
|--------|------|
| `Print` | Captura de tela completa |
| `Shift + Print` | Captura de região selecionada |
| `Meta + Print` | Captura da janela ativa |

### 6.5 Personalizar Atalhos

**Configurações do Sistema** → **Atalhos** → **Atalhos Personalizados**

---

## 7. Solução de Problemas

<details>
<summary><b>SDDM não inicia após reboot</b></summary>

<br>

```bash
# Verificar status
sudo systemctl status sddm

# Ver logs
journalctl -xe | grep sddm

# Verificar se está habilitado
sudo systemctl is-enabled sddm

# Habilitar e iniciar
sudo systemctl enable sddm
sudo systemctl start sddm
```

</details>

<details>
<summary><b>Tela preta após login</b></summary>

<br>

**Possíveis causas:**
- Drivers de GPU incompatíveis
- Wayland não suportado

**Solução:**
1. Na tela de login (SDDM), clique no ícone de sessão (canto inferior esquerdo)
2. Selecione **Plasma (X11)** em vez de Wayland

**Para instalar Xorg:**
```bash
sudo pacman -S xorg-server xorg-apps
```

</details>

<details>
<summary><b>Plasma lento ou travando</b></summary>

<br>

**Desabilitar efeitos temporariamente:**
```bash
# Atalho rápido
Alt + Shift + F12
```

**Desabilitar efeitos permanentemente:**
- **Configurações do Sistema** → **Comportamento da Área de Trabalho** → **Efeitos de Desktop**
- Desmarque efeitos pesados

**Desabilitar indexação de arquivos (Baloo):**
- **Configurações do Sistema** → **Pesquisar** → **Indexação de Arquivos**
- Desmarque "Habilitar indexação de arquivos"

**Reduzir widgets:**
- Remova widgets desnecessários do painel e desktop

**Verificar drivers de GPU:**
- Consulte: [GPU_DRIVERS_GUIDE.md](./GPU_DRIVERS_GUIDE.md)

</details>

<details>
<summary><b>Som não funciona</b></summary>

<br>

```bash
# Verificar se PipeWire está rodando
systemctl --user status pipewire pipewire-pulse

# Iniciar se necessário
systemctl --user start pipewire pipewire-pulse
systemctl --user enable pipewire pipewire-pulse

# Abrir mixer de áudio
pavucontrol
```

</details>

<details>
<summary><b>Wayland ou X11 - Qual usar?</b></summary>

<br>

**Use Wayland se:**
- Tem laptop (melhor para touchpad e gestos)
- Quer melhor suporte a telas HiDPI
- GPU Intel ou AMD recente

**Use X11 se:**
- Tem NVIDIA (especialmente com drivers proprietários)
- Precisa de compartilhamento de tela confiável
- Usa aplicações antigas incompatíveis com Wayland
- Precisa de máxima estabilidade

**Trocar entre sessões:**
- Faça logout
- Na tela de login (SDDM), clique no ícone de sessão
- Escolha Plasma (Wayland) ou Plasma (X11)

</details>

<details>
<summary><b>Painel desapareceu</b></summary>

<br>

```bash
# Recriar painel padrão
# Pressione Alt + F2 e digite:
plasma-apply-desktoptheme breeze

# Ou adicionar novo painel:
# Clique direito na área de trabalho → Adicionar Painel → Painel Padrão
```

</details>

---

## 8. Verificação Final

```bash
# Verificar sessão KDE
echo $XDG_CURRENT_DESKTOP
# Deve mostrar: KDE

# Verificar servidor gráfico
echo $XDG_SESSION_TYPE
# Deve mostrar: wayland ou x11

# Ver versão do Plasma
plasmashell --version

# Verificar se SDDM está ativo
systemctl is-active sddm
# Deve mostrar: active

# Informações do sistema
fastfetch
```

---

## Referências

- [Arch Wiki - KDE](https://wiki.archlinux.org/title/KDE)
- [KDE Official Documentation](https://docs.kde.org/)
- [KDE Store - Temas e Widgets](https://store.kde.org/)
- [Arch Linux Packages - Plasma](https://archlinux.org/groups/x86_64/plasma/)
- [KDE UserBase Wiki](https://userbase.kde.org/)

---
