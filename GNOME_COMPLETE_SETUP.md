# Guia Completo de Instalação do GNOME no Arch Linux

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![GNOME](https://img.shields.io/badge/GNOME-4A86CF?style=for-the-badge&logo=gnome&logoColor=white)

> **Instalação completa do ambiente desktop GNOME**

---

## Pré-requisito

Este guia assume que você completou o [Guia Essencial de Pós-Instalação](./ARCH_POST_INSTALL.md).

---

## Índice

- [1. Instalação do GNOME](#1-instalação-do-gnome)
- [2. Ferramentas de Personalização](#2-ferramentas-de-personalização)
- [3. Aplicações Adicionais](#3-aplicações-adicionais)
- [4. Temas e Ícones](#4-temas-e-ícones)
- [5. Ativação e Primeiro Boot](#5-ativação-e-primeiro-boot)
- [6. Configurações Essenciais](#6-configurações-essenciais)
- [7. Atalhos de Teclado](#7-atalhos-de-teclado)
- [8. Solução de Problemas](#8-solução-de-problemas)
- [9. Verificação Final](#9-verificação-final)

---

## 1. Instalação do GNOME

### 1.1 GNOME Base + Extras

```bash
# Instalação completa recomendada
sudo pacman -S gnome gnome-extra

# Display Manager (tela de login gráfica)
sudo pacman -S gdm
```

---

## 2. Ferramentas de Personalização

```bash
# Tweaks (configurações avançadas - ESSENCIAL)
sudo pacman -S gnome-tweaks

# Suporte a extensões via navegador
# Permite instalar extensões diretamente do site extensions.gnome.org
sudo pacman -S gnome-browser-connector

# Extensões básicas pré-instaladas
# Inclui: window-list, places-menu, apps-menu, etc.
sudo pacman -S gnome-shell-extensions
```

---

## 3. Aplicações Adicionais do GNOME

```bash
# Editor de texto avançado
sudo pacman -S gedit

# Player de vídeo nativo GNOME
sudo pacman -S totem
```

---

## 4. Temas e Ícones

### 4.1 Temas Populares (Exemplos)

```bash
# Ícones modernos
sudo pacman -S papirus-icon-theme

# Cursor themes
sudo pacman -S breeze
```

### 4.2 Aplicar Temas

1. Abra `gnome-tweaks`
2. Vá em "Aparência"
3. Escolha os temas instalados

> **Dica**: Você também pode baixar temas de sites como [gnome-look.org](https://www.gnome-look.org) e extrair em `~/.themes` (temas GTK) ou `~/.icons` (ícones).

---

## 5. Ativação e Primeiro Boot

### 5.1 Habilitar GDM

```bash
# Habilitar GDM para iniciar automaticamente
sudo systemctl enable gdm

# Reiniciar o sistema
sudo reboot
```

### 5.2 Primeiro Boot

Após reiniciar:

1. **Tela de login (GDM)**
   - Digite sua senha
   - Clique no ícone de **engrenagem** (canto inferior direito) para escolher:
     - **GNOME** (Wayland - padrão)
     - **GNOME CLASSIC**

2. **Tour inicial** (primeira vez)
   - Configuração de idioma e região
   - Configuração de contas online (opcional)
   - Aprenda os atalhos básicos

3. **Desktop GNOME**
   - Pressione `Super` (tecla Windows) para Activities
   - Acesse aplicações pelo menu

---

## 6. Configurações Essenciais

### 6.1 Configurações Básicas

```bash
# Abrir configurações do sistema
gnome-control-center
```

**Configure:**
- **Wi-Fi/Rede**: Conectar à internet
- **Aparência**: Tema claro/escuro
- **Energia**: Suspensão automática, brilho
- **Notificações**: Gerenciar aplicações
- **Dispositivos**: Mouse, touchpad, teclado

### 6.2 GNOME Tweaks

```bash
# Abrir tweaks
gnome-tweaks
```

**Ajustes recomendados:**
- **Aparência**: Trocar temas e ícones
- **Barra superior**: Mostrar bateria em porcentagem, data
- **Janelas**: Habilitar botões minimizar/maximizar
- **Workspaces**: Configurar áreas de trabalho
- **Extensões**: Ativar/desativar extensões

---

## 7. Atalhos de Teclado

### 7.1 Atalhos Essenciais

| Atalho | Ação |
|--------|------|
| `Super` | Abrir Activities (visão geral) |
| `Super + A` | Mostrar aplicações |
| `Super + L` | Bloquear tela |
| `Super + D` | Mostrar desktop |
| `Super + M` | Abrir bandeja de mensagens |
| `Alt + Tab` | Alternar entre janelas |
| `Super + Tab` | Alternar entre aplicações |
| `Ctrl + Alt + T` | Abrir terminal (se configurado) |

### 7.2 Gerenciamento de Janelas

| Atalho | Ação |
|--------|------|
| `Super + ↑` | Maximizar janela |
| `Super + ↓` | Restaurar janela |
| `Super + ←` | Janela metade esquerda da tela |
| `Super + →` | Janela metade direita da tela |
| `Super + H` | Esconder janela |
| `Alt + F4` | Fechar janela |
| `Alt + F2` | Executar comando |

### 7.3 Personalizar Atalhos

- Abra **Configurações** → **Teclado** → **Atalhos de Teclado**
- Clique no atalho que deseja modificar
- Pressione a nova combinação de teclas

---

## 8. Solução de Problemas

<details>
<summary><b>GDM não inicia após reboot</b></summary>

<br>

```bash
# Verificar status
sudo systemctl status gdm

# Ver logs
journalctl -xe | grep gdm

# Verificar se o serviço está habilitado
sudo systemctl is-enabled gdm

# Se não estiver, habilitar
sudo systemctl enable gdm

# Tentar iniciar manualmente
sudo systemctl start gdm
```

</details>

<details>
<summary><b>Tela preta após login</b></summary>

<br>

**Possíveis causas:**
- Drivers de GPU incompatíveis
- Wayland não suportado (NVIDIA antiga)

**Solução:**
1. Na tela de login (GDM), clique no ícone de engrenagem
2. Selecione **"GNOME on Xorg"**
3. Faça login

**Para instalar Xorg:**
```bash
sudo pacman -S xorg-server xorg-apps
```

</details>

<details>
<summary><b>Extensões não aparecem</b></summary>

<br>

```bash
# Verificar se o conector está instalado
sudo pacman -S gnome-browser-connector

# Reiniciar GNOME Shell (apenas em Xorg)
# Pressione Alt + F2, digite 'r', pressione Enter

# Ou reiniciar sessão
```

</details>

---

## 9. Verificação Final

```bash
# Verificar sessão GNOME
echo $XDG_CURRENT_DESKTOP
# Deve mostrar: GNOME

# Verificar servidor gráfico
echo $XDG_SESSION_TYPE
# Deve mostrar: wayland (ou x11 se usar Xorg)

# Ver versão do GNOME
gnome-shell --version

# Verificar se GDM está ativo
systemctl is-active gdm
# Deve mostrar: active

# Informações do sistema
neofetch
```

---

## Referências

- [Arch Wiki - GNOME](https://wiki.archlinux.org/title/GNOME)
- [GNOME Official Documentation](https://help.gnome.org/)
- [GNOME Extensions](https://extensions.gnome.org/)
- [GNOME Look - Temas e Ícones](https://www.gnome-look.org)
- [Arch Linux Packages - GNOME](https://archlinux.org/groups/x86_64/gnome/)

---
