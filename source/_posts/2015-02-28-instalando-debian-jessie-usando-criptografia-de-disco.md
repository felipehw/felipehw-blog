title: Instalando Debian Jessie usando criptografia de disco
tags:
  - debian
  - criptografia
categories:
  - gnu/linux
date: 2015-02-28 17:27:02
---

O primeiro tema do blog é a instalação do Debian Jessie usando criptografia de disco. Escrevi o post porque consegui comprar meu primeiro notebook e ao contrário dos desktops que tive antes ... tenho de me preocupar com os dados em caso de ~~perca~~ perda ou roubo.

A melhor maneira de se prevenir contra ~~perca~~ perda ou roubo é criptografando os dados e para tanto optei em instalar o Debian numa partição completamente criptografada. Assim ... nem a PF consegue os dados :D.

O post **não** é indicado para quem nunca instalou um GNU/Linux na vida (não desista, há vários por aí :D), mas para quem nunca instalou um Debian.

A versão do Debian que optei é a Jessie (v8), ela está quase saindo do forno (é **testing** mas está em *freeze*, portanto só aceita correção de bugs). Existem pouquíssimos bugs nela (me deparei mais com eles na instalação do que no uso do dia-a-dia) e certamente o que está aqui valerá para a versão final que sairá em algumas semanas.

{% asset_img jessie.png "Jessie - Mascote do Debian 8" %}

<!-- more -->

**Bom, vamos lá:**

## 0. Backup dos dados existentes (disco será integralmente utilizado)

O passo zero não será descrito e depende de cada um. O instalador do Debian automatiza o processo de formatar, particionar e configurar a criptografia em disco desde que optemos por instalá-lo usando inteiramente um disco. Portanto, faça um backup dos dados e deixe o disco pronto para a instalação.

Caso não desejes reservar um disco inteiro para o Debian, é plenamente possível instalá-lo em apenas uma parte do disco com criptografia e etc. (o sobrenome do GNU/Linux é configurabilidade) mas não será coberto nesse post :P.

## 1. Download da imagem do Debian:

Primeiro baixe uma imagem do Debian Jessie em https://www.debian.org/CD/http-ftp/ e grave numa pendrive (ou CD/DVD - alguém usa isso?).

No momento que escrevo o Jessie é testing, portanto é o link [Official CD/DVD images of the "testing" distribution (regenerated weekly)](http://cdimage.debian.org/cdimage/weekly-builds/) mas em breve será o link [Official CD/DVD images of the "stable" release](https://www.debian.org/CD/http-ftp/#stable).

## 2. Instalando c/ criptografia:

Reinicie a máquina com a pendrive espetada e siga as seguintes opções:

- Ao iniciar a imagem de instalação, escolha *Graphical install*:

  {% asset_img debian8amd64-graphical_install.png "Início da instalação do Debian Jessie" %}

- Selecione como linguaguem "Português Brasil" e na sequência a localidade como "Brasil":

  {% asset_img debian8amd64-select_a_language.png "Select a language" %}

- Na próxima tela ("Configure o teclado") escolha o teclado apropriado. "Português brasileiro" para teclado ABNT (com cedilha) ou "Inglês americano" para teclado sem cedilha.


- **Caso seu computador possua hardware de rede que necessita de firmware proprietário** chegaremos à parte mais chatinha (**se não precisar** o instalador pula essa etapa, agradeça ao seus fabricantes de hardware e **[pule uma seção](#configurarrede)** :D).

  O Debian perguntará (tela "Detectar hardware de rede") se você deseja oferecer o firmware (ausente na instalação pq ñ é software livre). É um caso comum p/ placas de rede wireless e algumas cabeadas.

  A vantagem de usar a rede na instalação é que são baixadas atualizações além de configurarmos a rede e o endereço do repositório de pacotes utilizado pelo sistema. No meu caso, tive azar ... tanta a placa de rede cabeada quanto wireless precisam de firmware proprietário. **Caso seja o seu caso** (malditos fabricantes que ~~não colaboram com~~ boicotam o software livre):
    1. Anote os firmwares necessários informados pelo Debian (no meu caso, ele informou os arquivos `iwlwifi-6000g2b-6.ucode` - wifi- e, `rtl_nic/rtl8411-1.fw`  -cabeada-).
    2. Faça os procedimentos informados abaixo em [Firmwares](#firmwares) para obter os arquivos.
    3. Ofereça os arquivos nesse ponto da instalação, inserindo uma segunda pendrive quando pedido.

  **Ou** dê um belo f@#$-@# nos dois últimos passos, continue sem os firmwares e instale manualmente os pacotes com os firmwares após a instalação (tb explicado em [Firmwares](#firmwares)).

- <span id="configurarrede"></span>Após o suplício causado pelas empresas que não lançam nem colaboram com drivers livres (ou caso seu computador esteja livre desses hardwares), chegamos à configuração da rede (Tela "Configurar a rede").

  A configuração é simples. No meu caso, fiz a configuração mais "complicada" (rede sem fio):
  - Escolhi a interface: `wlan0` (`wlan0` era a placa wifi, `eth0` a placa cabeada).
  - Em "Escolher SSID", informei o nome da rede sem fio.
  - Em "Tipo da rede sem fio ...": `WPA/WPA2 PSK` (`WPA/WPA2 PSK` é a + comum e segura; `Rede Aberta/WEP` é + antiga e insegura).
  - Em "Frase secreta": Senha da rede.

  Depois de configurar a forma de acesso à rede é só informar o "Nome da máquina" (qualquer um) e o "Nome de domínio" (se não souber/tiver, qualquer um).

- O próximo passo é configurar usuários e senhas (tela "Configurar usuários e senhas"). Defina a senha do root bem como login e senha de um usuário normal:

  {% asset_img debian8amd64-configurar_usuarios_e_senhas.png "Configurar usuários e senhas" %}

- Na tela "Configurar o relógio", defina o fuso horário correto escolhendo seu estado na lista (ex: "Santa Catarina").

- Finalmente, chegamos à tela do particionador de discos:

  {% asset_img debian8amd64-particionar_discos.png "Particionar discos" %}

  É aqui que definiremos que o sistema operacional seja instalado num sistema de arquivo sob criptografia. Iremos utilizar um disco inteiro (portanto, apagaremos qualquer coisa que exista nele)

  Siga os seguintes passos:
    1. Escolha "Assistido - usar disco todo e LVM criptografado"
    2. Selecione o disco a ser formatado e particionado (escolhi o disco `sda`).
    3. Na tela "Esquema de particionamento" escolha "Todos os arquivos em uma partição (para iniciantes)" pois como estamos usando [LVM](https://en.wikipedia.org/wiki/Logical_Volume_Manager_%28Linux%29) é fácil redimensionar depois e criar as partições desejadas (post futuro).
    4. Responda "Sim" à pergunta "Gravar as mudanças nos discos e configurar LVM?".
    O disco será sobrescrito com dados aleatório para aumentar a segurança (dificulta diferenciar o que possui dados do que ainda está vazio).
    5. Agora é solicitada uma "frase criptográfica" para desbloquear o disco sempre que o sistema *bootar*. Atenção, se você esquecer essa senha ... adeus dados :D.
    6. Finalmente, confirme tudo escolhendo a opção "Finalizar o particionamento e escrever as mudanças no disco" e depois respondendo "Sim" à pergunta "Escrever as mudanças nos discos?".
    As partições serão criadas e o sistema básico instalado.

- Na tela "Configurar o gerenciador de pacotes":
  1. Responda "Não" à pergunta "Ler outro CD ou DVD?"
  2. Responda "Sim" à pergunta "Usar um espelho de rede?" se você tiver acesso à internet durante à instalação.
    2.1. Escolha o Brasil, como país do espelho e algum dos repositóris disponíveis.
    2.2. Opcionalmente, informe um proxy (se você não sabe o que é ... não precisa de um :D).
    Será configurado o acesso ao repositório, baixada e instalada as atualizações existentes.

- Na tela "Configurando popularity-contest" podemos optar por enviar dados anônimos sobre os pacotes instalados. Essa informação é usada para decidir os pacotes que são colocados no primeiro CD da distribuição.

- Na tela "Seleção de software" podemos escolher coleções de software para serem instaladas além do sistema básico. Na dúvida, a seleção padrão possui o necessário para ambientes desktop.

  {% asset_img debian8amd64-selecao_de_software.png "Seleção de software" %}

- Por fim na tela "Instalar o carregador de inicialização GRUB em um disco rígido":
  1. Responsa "Sim" à pergunta "Instalar o carregador de inicialização GRUB no registro mestre de inicialização?".
  2. Escolha o disco rídigo para gravar o GRUB (responsável por viabilizar o boot do sistema operacional): No meu caso, escolhi meu primeiro disco (`/dev/sda`).

- Pronto, basta reiniciar o sistema:

  {% asset_img debian8amd64-instalacao_completada.png "Instalação completada" %}

- No boot será pedido a "frase criptográfica" (sem ela é impossível acessar os dados da partição):

  {% asset_img debian8amd64-pedindo_frase_criptografica.png "Debian pedindo frase criptográfica no boot" %}

- Após o boot, eis o GNOME do Debian Jessie em toda sua glória (e com a merecida segurança oferecida pela criptografia):

  {% asset_img debian8amd64-desktop.png "GNOME no Debian Jessie" %}

## Extras

### <span id="firmwares">Firmwares</span>

- Para conseguir exatamente os firmwares faltando:
  1. Pesquise pelos arquivos de firmware mencionados pelo instalador no site [Debian -- Packages](https://www.debian.org/distrib/packages), por meio do instrumento de busca "Search the contents of packages". Será indicado o pacote .deb contendo o firmware.
  2. Realize o download do pacote.
  3. Copie o pacote para o diretório raiz de um pendrive formatado em FAT.
  4. Insira a pendrive quando solicitada durante a instalação.

  No caso dos arquivos `rtlwifi/rtl8723befw.bin` e `rtl_nic/rtl8411-1.fw`, ambos estavam no pacote `firmware-realtek`.
  No caso do arquivo `iwlwifi-6000g2b-6.ucode`, está no pacote `firmware-iwlwifi`.

- Para baixar num único arquivo os pacotes .debs contendo os firmwares mais populares, basta fazer o download do arquivo compactado correspondente ao release da distribuição em <http://cdimage.debian.org/cdimage/unofficial/non-free/firmware/>. Aí é só extrair seu conteúdo para uma pendrive e seguir os passos acima.

- Opcionalmente, os pacotes contendo os firmwares podem ser instalados manualmente após a instalação. (No meu caso, os pacotes `firmware-realtek_0.43_all.deb` e `firmware-iwlwifi`)

- Mais info: [6.4. Loading Missing Firmware](https://www.debian.org/releases/stable/amd64/ch06s04.html.en)

### Bugs, etc.

Os bugs que encontrei durante a instalação seguem abaixo. Lembre-se que o Jessie está na reta final e esses bugs provavelmente estarão corrigidos à tempo do lançamento. Ainda assim, vale lembrar que não topei com bugs após a instalação.

- Não funcionou o touchpad durante a instalação (após a instalação, sim).
- Após configurar os discos com a opção "Assistido - usar o disco todo e LVM criptografado", não posso voltar para configurar mais nada relacionado à discos pois dessa forma o instalador fará configurações erradas que após a instalação inviabilizarão o boot sem intervenção manual. Parece semelhante ao problema relatado em <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=739299>.
- Não consegui instalar os firmwares a partir de mídia removível durante a instalação, acabei instalando os pacotes .deb contendo os firmwares manualmente após a instalação.
