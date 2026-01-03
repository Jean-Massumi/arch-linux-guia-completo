# Recomendações de Aplicativos - Arch Linux

---

## Flatpak

Além dos repositórios oficiais e AUR, você pode instalar aplicativos via Flatpak:

```bash
sudo pacman -S flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Procure aplicativos em: [https://flathub.org](https://flathub.org)

---

## Índice
- [Navegadores Web](#navegadores-web)
- [Editores de Texto e IDEs](#editores-de-texto-e-ides)
- [IDEs Completas](#ides-completas)
- [Suítes de Escritório](#suítes-de-escritório)
- [Comunicação](#comunicação)
- [Reprodutores de Mídia](#reprodutores-de-mídia)
- [Streaming](#streaming)
- [Edição de Imagens](#edição-de-imagens)
- [Modelagem 3D](#modelagem-3d)
- [Gravação de Tela](#gravação-de-tela)
- [Edição de Vídeo](#edição-de-vídeo)
- [Programação e Desenvolvimento](#programação-e-desenvolvimento)
- [Linguagens de Programação](#linguagens-de-programação)
- [Compiladores e Build Tools](#compiladores-e-build-tools)
- [Banco de Dados](#banco-de-dados)
- [Terminais](#terminais)
- [Matemática e Computação Científica](#matemática-e-computação-científica)
- [Modelagem CAD](#modelagem-cad)
- [Desenvolvimento de Jogos](#desenvolvimento-de-jogos)
- [Gerenciadores de Download](#gerenciadores-de-download)
- [Gaming](#gaming)
- [Compatibilidade Windows](#compatibilidade-windows)
- [Segurança](#segurança)

---

## Navegadores Web

**Firefox** - Navegador open-source focado em privacidade  
**Chromium** - Versão open-source do Chrome

```bash
sudo pacman -S firefox chromium
```

---

## Editores de Texto

**VS Code** - Editor moderno com suporte a extensões  
**Neovim** - Editor modal rápido e configurável via terminal  
**Emacs** - Editor extensível com ecossistema completo de plugins

```bash
sudo pacman -S code neovim emacs
```

---

## IDEs Completas

**IntelliJ IDEA Community** - IDE para Java, Kotlin e desenvolvimento JVM  
**PyCharm Community** - IDE especializada em Python  
**NetBeans** - IDE multiplataforma para Java e outras linguagens

```bash
sudo pacman -S intellij-idea-community-edition pycharm-community-edition netbeans
```

---

## Suítes de Escritório

**LibreOffice** - Suíte completa compatível com MS Office (Writer, Calc, Impress)

```bash
sudo pacman -S libreoffice-fresh-pt-br
```

---

## Comunicação

**Discord** - Chat para comunidades e gaming  
**Telegram Desktop** - Mensageiro rápido e multiplataforma  
**Slack** - Comunicação profissional e colaboração

```bash
sudo pacman -S discord telegram-desktop
yay -S slack-desktop
```

---

## Reprodutores de Mídia

**VLC** - Player versátil que reproduz qualquer formato  
**MPV** - Player minimalista e leve para terminal/GUI  
**Spotify** - Streaming de música

```bash
sudo pacman -S vlc mpv
yay -S spotify
```

---

## Streaming

**Stremio** - Plataforma para streaming de filmes e séries com suporte a addons

```bash
yay -S stremio
```

---

## Edição de Imagens

**GIMP** - Editor de imagens completo (alternativa ao Photoshop)  
**Inkscape** - Editor de gráficos vetoriais (alternativa ao Illustrator)  
**Krita** - Focado em pintura digital e ilustração

```bash
sudo pacman -S gimp inkscape krita
```

---

## Modelagem 3D

**Blender** - Suíte completa de modelagem, animação, renderização e composição 3D

```bash
sudo pacman -S blender
```

---

## Gravação de Tela

**OBS Studio** - Gravação e transmissão ao vivo com recursos profissionais

```bash
sudo pacman -S obs-studio
```

---

## Edição de Vídeo

**Kdenlive** - Editor não-linear completo e poderoso  
**ShotCut** - Editor simples e intuitivo para edições básicas

```bash
sudo pacman -S kdenlive shotcut
```

---

## Programação e Desenvolvimento

**Git** - Sistema de controle de versão  
**Docker** - Plataforma de containers para desenvolvimento  
**Markdown** - Visualizadores e editores de markdown

```bash
sudo pacman -S git docker docker-compose marksman
sudo systemctl enable docker
sudo systemctl start docker
```

---

## Linguagens de Programação

**Python** - Linguagem de programação e gerenciador pip  
**Java (JDK)** - Kit de desenvolvimento Java  
**Node.js / npm** - Runtime JavaScript e gerenciador de pacotes  
**Rust** - Linguagem focada em segurança e performance  
**Go** - Linguagem do Google para aplicações concorrentes

```bash
sudo pacman -S python python-pip jdk-openjdk nodejs npm rust go
```

---

## Compiladores e Build Tools

**GCC** - Compilador C/C++  
**Clang** - Compilador alternativo C/C++/Objective-C  
**Make** - Ferramenta de automação de build  
**CMake** - Sistema de build multiplataforma

```bash
sudo pacman -S gcc clang make cmake
```

---

## Banco de Dados

**MySQL** - Banco de dados relacional popular  
**PostgreSQL** - Banco de dados relacional avançado  
**MongoDB** - Banco de dados NoSQL orientado a documentos  
**Redis** - Banco de dados em memória para cache

```bash
sudo pacman -S mysql postgresql mongodb-bin redis
```

**Iniciar serviços:**
```bash
# MySQL
sudo systemctl enable mysqld
sudo systemctl start mysqld

# PostgreSQL
sudo systemctl enable postgresql
sudo systemctl start postgresql

# MongoDB
sudo systemctl enable mongodb
sudo systemctl start mongodb

# Redis
sudo systemctl enable redis
sudo systemctl start redis
```

---

## Terminais

**Alacritty** - Terminal acelerado por GPU, rápido e configurável  
**Kitty** - Terminal moderno com suporte a imagens  
**Terminator** - Terminal com suporte a múltiplos painéis

```bash
sudo pacman -S alacritty kitty terminator
```

---

## Matemática e Computação Científica

**GeoGebra** - Software de matemática interativa (geometria, álgebra, cálculo)  
**SageMath** - Sistema de matemática computacional (alternativa ao Mathematica)

```bash
sudo pacman -S geogebra sagemath
```

---

## Modelagem CAD

**FreeCAD** - Modelagem CAD 3D paramétrica  
**LibreCAD** - CAD 2D para desenho técnico  
**OpenSCAD** - CAD 3D programável via script

```bash
sudo pacman -S freecad librecad openscad
```

---

## Desenvolvimento de Jogos

**Godot** - Engine de jogos 2D/3D open-source  
**Unity Hub** - Engine profissional de jogos (via AUR)  
**Unreal Engine** - Engine AAA para jogos de alta qualidade

```bash
sudo pacman -S godot
yay -S unityhub
```

---

## Gerenciadores de Download

**qBittorrent** - Cliente torrent com interface gráfica completa  
**Transmission** - Cliente torrent leve e simples  
**aria2** - Download acelerado via terminal (HTTP/FTP/Torrent)

```bash
sudo pacman -S qbittorrent transmission-gtk aria2
```

---

## Gaming

**Steam** - Plataforma principal de jogos no Linux  
**Lutris** - Gerenciador para jogos de múltiplas plataformas  
**Heroic Games Launcher** - Cliente para Epic Games e GOG

```bash
sudo pacman -S steam lutris
yay -S heroic-games-launcher-bin
```

---

## Compatibilidade Windows

**Wine** - Camada de compatibilidade para executar aplicativos Windows  
**Winetricks** - Script auxiliar para instalar bibliotecas Windows  
**PlayOnLinux** - Interface gráfica para gerenciar aplicações Wine

```bash
sudo pacman -S wine winetricks playonlinux
```

---

## Segurança

**ProtonVPN** - VPN para proteger conexão e privacidade online  
**ClamAV** - Antivírus open-source (opcional, útil para verificar arquivos compartilhados)

```bash
sudo pacman -S protonvpn-cli clamav
```

**Nota sobre antivírus**: Linux geralmente não precisa de antivírus para uso pessoal. ClamAV é útil se você compartilha arquivos com usuários Windows ou gerencia servidores.
