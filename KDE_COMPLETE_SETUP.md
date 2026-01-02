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
- [2. Temas e Ícones](#2-temas-e-ícones)
- [3. Ativação e Primeiro Boot](#3-ativação-e-primeiro-boot)
- [4. Configurações Essenciais](#4-configurações-essenciais)
- [5. Atalhos de Teclado](#5-atalhos-de-teclado)
- [6. Solução de Problemas](#6-solução-de-problemas)
- [7. Verificação Final](#7-verificação-final)

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

# X11 (opcional)
sudo pacman -S plasma-x11-session
```

---

## 2. Temas e Ícones

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

## 3. Ativação e Primeiro Boot

### 3.1 Habilitar SDDM

```bash
# Habilitar SDDM para iniciar automaticamente
sudo systemctl enable sddm

# Reiniciar o sistema
sudo reboot
```

### 3.2 Primeiro Boot

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

## 4. Configurações Essenciais

### 4.1 Abrir Configurações do Sistema

```bash
systemsettings
```

O KDE Plasma oferece amplas opções de personalização. Explore as configurações e ajuste conforme sua preferência:

**Principais seções:**
- **Aparência**: Temas, ícones, fontes, cores
- **Área de Trabalho**: Efeitos, comportamento de janelas, atalhos
- **Hardware**: Dispositivos de entrada, telas, energia
- **Personalização**: Notificações, aplicações padrão

**Dicas:**
- Clique direito no painel para editá-lo e adicionar widgets
- Clique direito na área de trabalho para adicionar widgets

---

## 5. Atalhos de Teclado

O KDE Plasma possui um sistema robusto e altamente personalizável de atalhos de teclado. Diferente de listas estáticas, os atalhos podem variar dependendo da versão do Plasma, idioma do sistema e configurações personalizadas.

### Como Descobrir Seus Atalhos

**Método 1: Configurações do Sistema**
```bash
# Abrir configurações de atalhos
systemsettings kcm_keys
```
- Acesse **Configurações do Sistema** → **Atalhos**
- Aqui você encontra TODOS os atalhos organizados por categoria:
  - **Atalhos Globais**: Funcionam em qualquer aplicação
  - **Atalhos de Aplicações**: Específicos de cada programa
- Você pode pesquisar, modificar ou criar novos atalhos facilmente

**Método 2: Busca Rápida no KRunner**
- Pressione `Alt + Space` ou `Alt + F2` para abrir o KRunner
- Digite "atalhos" ou "shortcuts"
- Acesse diretamente as configurações

### Atalhos Universais

Alguns atalhos funcionam em praticamente qualquer instalação KDE:
- **`Alt + Space` ou `Alt + F2`**: Abre o KRunner (lançador de aplicações e comandos)
- **`Alt + Tab`**: Alterna entre janelas abertas
- **`Ctrl + Alt + Esc`**: Fecha aplicações travadas (cursor vira uma caveira)
- **`Ctrl + F12`**: Mostra todas as janelas abertas (Present Windows)

### Personalizando Seus Atalhos

O KDE é extremamente flexível em personalização. Você pode:
- Criar atalhos para qualquer aplicação ou comando
- Definir gestos de mouse e touchpad
- Configurar atalhos para scripts personalizados
- Importar/exportar esquemas de atalhos

**Dica**: Explore a categoria "KWin" nos atalhos globais para descobrir recursos poderosos de gerenciamento de janelas, como tiles, áreas de trabalho virtuais e efeitos especiais!

---

## 6. Solução de Problemas

<details>
<summary><b>SDDM não inicia após reboot</b></summary>

<br>

```bash
# Verificar status
sudo systemctl status sddm

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

---

## 7. Verificação Final

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
