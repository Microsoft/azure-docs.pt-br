---
title: Criação do cluster do Microsoft Azure FXT Edge Filer
description: Como criar um cluster de cache de armazenamento híbrido com o Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 94ec2b088940f4f1f683a4f88ae312879d909bc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543614"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Tutorial: Criar o cluster do Azure FXT Edge Filer

Após instalar e iniciar os nós do hardware do Azure FXT Edge Filer no cache, use o software do cluster do FXT para criar o cluster de cache. 

Neste tutorial, você verá as etapas para configurar os nós do hardware como um cluster. 

Neste tutorial, você irá aprender: 

> [!div class="checklist"]
> * As informações necessárias antes de começar a criar o cluster
> * A diferença entre a rede de gerenciamento do cluster, a rede de cluster e a rede voltada ao cliente
> * Como se conectar a um nó de cluster 
> * Como criar um cluster inicial usando um nó do Azure FXT Edge Filer
> * Como entrar no painel de controle do cluster para definir suas configurações

Esse procedimento leva entre 15 e 45 minutos, dependendo de quanta pesquisa você precisará fazer para identificar endereços IP e recursos de rede.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua estes pré-requisitos:

* Instalar os sistemas de hardware do Azure FXT Edge Filer no data center 

  Só é necessário um nó para criar o cluster, mas você precisa [adicionar ao menos dois outros nós](fxt-add-nodes.md) antes de configurar o cluster e prepará-lo para uso. 

* Conectar os cabos de rede e alimentação corretos ao sistema  
* Ative ao menos um nó do Azure FXT Edge Filer e [definir sua senha raiz](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Coletar informações para o cluster 

É necessário ter as seguintes informações para criar o cluster do Azure FXT Edge Filer:

* Nome do cluster

* Senha administrativa a ser definida para o cluster

* Endereços IP:

  * Um endereço IP exclusivo para gerenciamento do cluster, bem como a máscara de rede e o roteador para usar na rede de gerenciamento
  * O primeiro e o último endereço IP em um intervalo contíguo de endereços IP para comunicação (nó para nó) do cluster. Confira [Distribuição de endereço IP](#ip-address-distribution) abaixo para ver detalhes. 
  * Os endereços IP voltados ao cliente são definidos após a criação do cluster.

* Informações sobre a infraestrutura de rede:

  * O endereço IP do servidor DNS do cluster
  * O nome do domínio DNS do cluster
  * O nome ou o endereço IP dos servidores NTP do cluster (um servidor ou três ou mais) 
  * Se você quiser habilitar a agregação de links IEEE 802.1AX-2008 nas interfaces do cluster
  * Se você habilitar a agregação de links, independentemente de usar ou não a agregação dinâmica IEEE 802.3ad (LACP)

É possível configurar esses itens de infraestrutura de rede após criar o cluster, mas é melhor fazer isso no momento da criação. 

### <a name="ip-address-distribution"></a>Distribuição de endereço IP

O cluster de cache de armazenamento híbrido do Azure FXT Edge Filer usa endereços IP em três categorias:

* IP de gerenciamento: um endereço IP exclusivo para gerenciamento de clusters

  Esse endereço serve como ponto de entrada para acessar os utilitários de configuração do cluster (o painel de controle baseado na Web ou a API XML-RPC). Esse endereço é atribuído automaticamente ao nó primário no cluster, além de se mover automaticamente se o nó primário for alterado.

  Outros endereços IP podem ser usados para acessar o painel de controle, mas o endereço IP de gerenciamento é projetado para permitir acesse mesmo se há failover dos nós individuais.

* Rede de cluster: um intervalo de endereços IP para comunicação de cluster

  A rede de cluster é usada para comunicação entre os nós de cluster e para recuperar arquivos do armazenamento de back-end (arquivistas principais).

  **Prática recomendada:** alocar um endereço IP por porta física usada para comunicação de clusters em cada nó do Azure FXT Edge Filer. O cluster atribui automaticamente os endereços no intervalo especificado aos nós individuais.

* Rede voltada ao cliente: o intervalo de endereços IP usado pelos clientes para solicitar e gravar arquivos

  Os endereços da rede do cliente são usados por eles para acessar os dados do arquivista principal por meio do cluster. Por exemplo, um cliente NFS pode montar um desses endereços.

  **Prática recomendada:** alocar um endereço IP por porta física usada para comunicação de clientes em cada nó do FXT Series.

  O cluster distribui endereços IP voltados ao cliente para todos os nós constituintes da maneira mais uniforme possível.

  Para manter a simplicidade, vários administradores configuram um nome DNS exclusivo com a configuração RRDNS (DNS round-robin) para facilitar a distribuição de solicitações do cliente para todo o intervalo de endereços. Essa configuração também permite que os clientes usem o mesmo comando de montagem para acessar o cluster. Leia [Configurar DNS](fxt-configure-network.md#configure-dns-for-load-balancing) para saber mais.

O endereço IP de gerenciamento e um intervalo de endereços da rede de cluster precisam ser especificados para criar um novo cluster. Os endereços voltados ao cliente são especificados após a criação do cluster.

## <a name="connect-to-the-first-node"></a>Conectar-se ao primeiro nó

É possível se conectar a qualquer um dos nós instalados do FXT e usar o software do sistema operacional para configurar o cluster.

Se você ainda não tiver feito isso, ative ao menos um dos nós do FXT do cluster e verifique se ele tem uma conexão de rede e um endereço IP. É necessário definir uma nova senha raiz para ativar o nó. Portanto, siga as etapas em [Definir senhas de hardware](fxt-node-password.md) se ainda não tiver feito isso.

Para verificar a conexão de rede, confira se os LEDs do link de rede do nó estão iluminados (e, se necessário, os indicadores na chave de rede em que eles estão anexados). Os LEDs indicadores são descritos em [Monitorar o status de hardware do Azure FXT Edge Filer](fxt-monitor.md).

Após ser iniciado, o nó solicitará um endereço IP. Se estiver conectado a um servidor DHCP, aceitará o endereço IP fornecido pelo DHCP. Esse endereço IP é temporário. Ele será alterado quando você criar o cluster.

Se não estiver conectado a um servidor DHCP ou não receber uma resposta, o nó usará o software Bonjour para definir um endereço IP atribuído automaticamente no formulário 169.254.\*.\*. No entanto, é preciso definir um endereço IP estático temporário em um dos cartões de rede do nó antes de usá-lo para criar um cluster. As instruções estão incluídas neste documento herdado. Entre em contato com o Serviço de Suporte e Atendimento ao Cliente Microsoft para obter informações atualizadas: [Apêndice A: Definir um endereço IP estático em um nó do FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Encontrar o endereço IP

Conecte-se ao nó do Azure FXT Edge Filer para encontrar o endereço IP. É possível usar um cabo serial, uma conexão direta com as portas USB e VGA ou conectar-se por meio de uma chave KVM. Para ver detalhes sobre a conexão de porta, confira [Definir senhas iniciais](fxt-node-password.md).

Após se conectar, entre com o nome de usuário `root` e a senha definida ao iniciar o nó pela primeira vez.  

Depois de entrar, é necessário determinar o endereço IP do nó.

Use o comando `ifconfig` para ver os endereços atribuídos ao sistema.

Por exemplo, o comando `ifconfig | grep -B5 inet` busca portas com endereços de Internet e fornece cinco linhas de contexto para mostrar o identificador de porta.

Anote todos os endereços IP mostrados no relatório ifconfig. Os endereços listados com nomes de porta, como e0a ou e0b, são boas opções. Não use endereços IP listados com nomes e7*, pois eles só são usados para portas de serviço iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Carregar o assistente de configuração do cluster

Use a ferramenta de configuração de cluster baseada no navegador para criá-lo. 

Insira o endereço IP do nó em um navegador da Web. Se o navegador exibir uma mensagem dizendo que o site não é confiável, entre mesmo assim. Os nós individuais do Azure FXT Edge Filer não têm certificados de segurança da AC.

![Página de entrada do painel de controle na janela do navegador](media/fxt-cluster-create/unconfigured-node-gui.png)

Deixe os campos **Nome de usuário** e **Senha** em branco. Clique em **Logon** para carregar a página de criação do cluster.

![Tela de configuração inicial de um nó não configurado no painel de controle da GUI baseada em navegador. Mostra as opções de atualizar o software, configurar o cluster manualmente ou configurar um cluster no arquivo de instalação.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Criar o cluster

A ferramenta de configuração de cluster usa um conjunto de telas para orientar você na criação do cluster do Azure FXT Edge Filer. Verifique se você tem as [informações necessárias](#gather-information-for-the-cluster) antes de começar. 

### <a name="creation-options"></a>Opções de criação

A primeira tela oferece três opções. Use a opção de configuração manual, a menos que você tenha instruções especiais da equipe de suporte.

Clique em **Configurar o cluster manualmente** para carregar a nova tela de opções de configuração do cluster. 

As outras opções raramente são usadas:

* "Atualizar a imagem do sistema" solicita que você instale o novo software do sistema operacional antes de criar o cluster. A versão instalada no momento está listada na parte superior da tela. É necessário fornecer o arquivo de pacote do software – uma URL e o nome de usuário/senha ou carregar um arquivo do computador. 

* Às vezes, a opção de arquivo de instalação de cluster é usada pelo Serviço de Suporte e Atendimento ao Cliente Microsoft. 

## <a name="cluster-options"></a>Opções de cluster

A tela seguinte solicitará que você configure as opções para o novo cluster.

A página é dividida em duas seções principais, **Configuração Básica** e **Configuração de Rede**. A seção de configuração de rede também tem subseções: uma para o **Gerenciamento** de rede e outra para a rede de **Cluster**.

### <a name="basic-configuration"></a>Configuração básica

Na seção superior, preencha as informações básicas do novo cluster.

![Detalhes da seção "Configuração básica" na página da GUI no navegador. Três campos são mostrados (nome do cluster, senha do administrador e confirmar senha)](media/fxt-cluster-create/basic-configuration.png) 

* **Nome do Cluster**: insira um nome exclusivo para o cluster.

  O nome do cluster deve atender a estes critérios:
  
  * De 1 a 16 caracteres
  * Pode incluir letras, números, o hífen (-) e caracteres de sublinhado (_) 
  * Não deve incluir outras pontuações ou caracteres especiais
  
  É possível alterar esse nome mais tarde na página de configuração **Cluster** > **Configuração Geral**. Para saber mais sobre as configurações do cluster, leia o [Guia de Configuração de Clusters](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), que não faz parte deste conjunto de documentação.

  > [!NOTE] 
  > O nome do cluster é usado para identificar as informações do sistema carregadas para dar suporte ao monitoramento ou à solução de problemas. Assim, é útil incluir o nome da empresa.

* **Senha do administrador**: defina a senha do usuário administrativo padrão, `admin`.
  
  Configure contas de usuário individuais para cada pessoa que administra o cluster, mas não remova o usuário `admin`. Entre como `admin` se precisar criar outros usuários.
 
  É possível alterar a senha de `admin` na página de configurações **Administração** > **Usuários** no cluster Painel de Controle. Para saber detalhes, leia a documentação **Usuários** no [Guia de Configuração de Clusters](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Configuração de rede

A seção **Rede** solicita que você especifique a infraestrutura de rede que será usada pelo cluster. 

Há duas redes separadas para configurar:

* A *rede de gerenciamento* permite acesso de administrador ao cluster para configuração e monitoramento. O endereço IP especificado aqui é usado ao se conectar com o Painel de Controle ou para acesso SSH. 

  A maioria dos clusters usa somente um endereço IP de gerenciamento, mas se você quiser adicionar interfaces, faça-o após a criação do cluster.

* A *rede de cluster* é usada para comunicação nos nós de cluster e entre os nós de cluster e o armazenamento de back-end (arquivistas principais).

A rede voltada ao cliente será configurada posteriormente, após a criação do cluster.

Esta seção também inclui a configuração de servidores DNS e NTP usados por ambas as redes.

### <a name="configure-the-management-network"></a>Configurar a rede de gerenciamento

As configurações da seção **Gerenciamento** são para a rede que permite acesso de administrador ao cluster.

![Detalhes da seção "Gerenciamento" com campos para cinco opções e uma caixa de seleção para a rede de gerenciamento de 1 GB](media/fxt-cluster-create/management-network-filled.png)

* **Gerenciamento de IP**: especifique o endereço IP a ser usado para acessar o painel de controle do cluster. Esse endereço será solicitado pelo nó primário do cluster, mas será movido automaticamente para um nó íntegro se o nó primário original ficar não disponível.

  A maioria dos clusters usa apenas um endereço IP de gerenciamento. Se você quiser mais de um, poderá adicioná-los após criar o cluster usando a página de configurações **Cluster** > **Rede Administrativa**. Leia mais no [Guia de Configuração de Clusters](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Máscara de rede**: especifique a máscara de rede da rede de gerenciamento.

* **Roteador**: insira o endereço de gateway padrão usado pela rede de gerenciamento.

* **Tag VLAN (opcional)** : se o cluster usar tags VLAN, especifique a tag da rede de gerenciamento.

  Outras configurações de VLAN são definidas na página de configurações **Cluster** > **VLAN**. Leia [Trabalhar com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) e [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) no Guia de Configuração de Clusters para saber mais.

* **MTU**: se necessário, ajuste a MTU (unidade máxima de transmissão) da rede de gerenciamento do cluster.

* **Usar a rede de gerenciamento de 1 GB**: marque esta caixa de seleção se quiser atribuir as duas portas de rede de 1 GbE nos nós do FXT somente à rede de gerenciamento. É necessário ter portas de 25 GbE/10 GbE disponíveis para todo o tráfego restante. Se você não marcar essa caixa, a rede de gerenciamento usará a porta de velocidade mais alta disponível. 

### <a name="configure-the-cluster-network"></a>Configurar a rede de cluster 

As configurações da rede de cluster se aplicam ao tráfego nos nós de cluster e entre os nós de cluster e os arquivistas principais.

![Detalhes da seção "Cluster" com campos para inserir seis valores](media/fxt-cluster-create/cluster-network-filled.png)

* **Primeiro IP** e **Último IP**: insira os endereços IP que definem o intervalo a ser usado para comunicação interna do cluster. Os endereços IP usados aqui precisam ser contíguos e não atribuídos pelo DHCP.

  É possível adicionar mais endereços IP após criar o cluster. Use a página de configurações **Cluster** > **Redes de Cluster** (documentação [Guia de Configuração de Clusters](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  O valor em **Número de IPs no Intervalo** é calculado e exibido automaticamente.

* **Máscara de rede sem gerenciamento (opcional)** : especifique a máscara de rede da rede de cluster. 

  O sistema sugere automaticamente o valor de máscara de rede que você inseriu para a rede de gerenciamento; altere se necessário.

* **Roteador de cluster (opcional)** : especifique o endereço de gateway padrão usado pela rede de cluster. 

  O sistema sugere automaticamente o mesmo endereço de gateway que você forneceu para a rede de gerenciamento.

* **Tag VLAN do cluster (opcional)** : se o cluster usar tags VLAN, especifique a tag da rede de cluster.

* **MTU sem gerenciamento (opcional)** : se necessário, ajuste a MTU (unidade máxima de transmissão) da rede de cluster.

### <a name="configure-cluster-dns-and-ntp"></a>Configurar o DNS e o NTP do cluster 

Abaixo da seção **Cluster**, há campos para especificar os servidores DNS e NTP e para habilitar a agregação de links. Essas configurações se aplicam a todas as redes usadas pelo cluster.

![Detalhes da seção de configuração de DNS/NTP, com três campos para servidores DNS, campos para domínio DNS e pesquisa DNS, três campos para servidores NTP e um menu suspenso de opções de agregação de links](media/fxt-cluster-create/dns-ntp-filled.png)

* **Servidores DNS**: insira o endereço IP de um ou mais servidores DNS (Sistema de Nomes de Domínio).

  O DNS é recomendado para todos os clusters e obrigatório se você deseja usar o SMB, o AD ou o Kerberos. 
  
  Para otimizar o desempenho, configure o servidor DNS do cluster para balanceamento de carga round-robin, conforme descrito em [Configurar o DNS do cluster do Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domínio DNS**: insira o nome de domínio de rede a ser usado pelo cluster.

* **Pesquisa DNS**: como opção, insira outros nomes de domínio que o sistema deve pesquisar para resolver consultas DNS. Você pode adicionar até seis nomes de domínio, separados por espaços.

* **Servidores NTP**: especifique um ou três servidores de protocolo NTP nos campos fornecidos. Você pode usar nomes de host ou endereços IP.

* **Agregação de links**: a agregação de links permite personalizar como as portas Ethernet em nós do cluster do FXT processam vários tipos de tráfego. Para saber mais, leia [Agregação de links](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) no Guia de Configuração de Clusters.

### <a name="click-the-create-button"></a>Clique no botão para criar

Após inserir todas as configurações necessárias no formulário, clique no botão **Criar Cluster**.

![Parte inferior do formulário preenchido com o cursor sobre o botão Criar no canto inferior direito](media/fxt-cluster-create/create-cluster.png)

O sistema exibe uma mensagem ao criar o cluster.

![Mensagem de status da configuração do cluster no navegador: "O nó do FXT está criando o cluster. Isso levará alguns minutos. Após a criação do cluster, acesse este link para concluir a configuração." com hiperlink em "acesse este link"](media/fxt-cluster-create/creating-message.png)

Após alguns instantes, você poderá clicar no link na mensagem para acessar o painel de controle do cluster. Este link levará você até o endereço IP especificado no **IP de gerenciamento**. Leva de 15 segundos a um minuto para o link ser ativado após você clicar no botão de criar. Se a interface da Web não for carregada, aguarde mais alguns segundos e, em seguida, clique no link novamente. 

O cluster demora um minuto ou mais para ser criado, mas você pode entrar no Painel de Controle durante o processo. É normal a página do painel de controle mostrar avisos até a conclusão do processo de criação do cluster. 

## <a name="open-the-settings-pages"></a>Abrir a página Configurações 

Após a criação do cluster, é necessário personalizar a configuração da rede e do fluxo de trabalho. 

Use a interface da Web do painel de controle para configurar o novo cluster. Acesse o link da tela de status de criação do cluster ou navegue para o endereço IP de gerenciamento definido no cluster.

Entre na interface da Web com o nome de usuário `admin` e a senha definida ao criar o cluster.

![Navegador da Web mostrando os campos de logon do painel de controle](media/fxt-cluster-create/admin-login.png)

O painel de controle abre e mostra a página **Painel**. Com o fim do processo de criação do cluster, todas as mensagens de aviso desaparecerão.

Clique na guia **Configurações** para configurar o cluster.

Na guia **Configurações**, a barra lateral esquerda mostra um menu de páginas de configuração. Essas páginas são organizadas por categoria. Clique nos controles + ou - ao lado do nome da categoria para expandir ou ocultar as páginas individuais.

![Guia "Configurações" do painel de controle (no navegador) com a página Cluster > Configuração Geral carregada](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Etapas de instalação do cluster

Neste ponto do processo, o cluster existe, mas tem somente um nó, nenhum endereço IP voltado ao cliente e nenhum armazenamento de back-end. Mais etapas de instalação são necessárias para sair de um cluster recém-criado para um sistema de cache pronto para processar o fluxo de trabalho.

### <a name="required-configuration"></a>Configuração necessária

Essas etapas são necessárias para a maioria ou todos os clusters. 

* Adicionar nós ao cluster 

  O padrão é de três nós, mas vários clusters de produção têm mais – até um máximo de 24 nós.

  Leia [Adicionar nós de cluster](fxt-add-nodes.md) para saber como adicionar outras unidades do Azure FXT Edge Filer ao cluster e habilitar a Alta Disponibilidade.

* Especifique o armazenamento de back-end

  Adicione as definições de *arquivista principal* a cada sistema de armazenamento de back-end a ser usado pelo cluster. Leia [Adicionar armazenamento de back-end e configurar o namespace virtual](fxt-add-storage.md#about-back-end-storage) para saber mais.

* Configure o acesso de cliente e o namespace virtual 

  Crie ao menos um servidor virtual (vserver) e atribua-o a um intervalo de endereços IP para uso dos computadores cliente. Também é necessário configurar o namespace do cluster (às vezes chamado de Namespace Global ou GNS), um recurso de sistema de arquivos que permite mapear as exportações do armazenamento de back-end para caminhos virtuais. O namespace de cluster oferece aos clientes uma estrutura de sistema de arquivos consistente e acessível, mesmo se você alternar a mídia de armazenamento de back-end. O namespace também pode fornecer uma hierarquia de armazenamento virtual amigável para contêineres de Blob do Azure ou outro armazenamento de objeto de nuvem compatível.

  Leia [Configurar o namespace](fxt-add-storage.md#configure-the-namespace) para saber detalhes. Esta etapa inclui:
  * Criar vservers
  * Configurar junções entre a exibição de rede do cliente e o armazenamento de back-end 
  * Definir quais endereços IP do cliente serão exibidos para cada vserver

  > [!Note] 
  > É recomendável planejar bastante antes de começar a configurar o GNS do cluster. Leia as seções [Usar um namespace global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) e [Criar e trabalhar com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) no Guia de Configuração de Clusters para obter ajuda.

* [Ajustar as configurações de rede](fxt-configure-network.md)

  Há várias configurações relacionadas à rede que devem ser verificadas ou personalizadas para um novo cluster. Leia [Ajustar as configurações de rede](fxt-configure-network.md) para saber detalhes sobre esses itens:

  * Verificar a configuração de DNS e NTP 
  * Configurar serviços de diretório, se necessário 
  * Configurar VLANs
  * Configurar servidores proxy
  * Adicionar endereços IP à rede de cluster
  * Armazenar certificados de criptografia

* [Configurar o suporte ao monitoramento](#enable-support)

  É necessário aceitar a política de privacidade da ferramenta de configuração e definir as configurações de upload de suporte ao mesmo tempo.

  O cluster pode fazer upload automaticamente dos dados de solução de problemas sobre o cluster, incluindo estatísticas e depuração de arquivos. Esses carregamentos permitem que o Serviço de Suporte e Atendimento ao Cliente Microsoft forneça o melhor serviço possível. É possível personalizar o que é monitorado e, opcionalmente, habilitar o suporte proativo e a solução de problemas de serviço remoto.  

### <a name="optional-configuration"></a>Configuração opcional

Estas etapas não são necessárias para todos os clusters. São necessárias para alguns tipos de fluxos de trabalho ou para determinados estilos de gerenciamento de cluster. 

* Personalizar as configurações do nó

  É possível definir nomes de nó e configurar portas IPMI de nó no nível do cluster ou individualmente. Se você definir essas configurações antes de adicionar nós ao cluster, os novos nós poderão escolher as configurações automaticamente quando entrarem. As opções são descritas no documento de criação de cluster herdado [Personalizar configurações de nó](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Partes da documentação desse produto ainda não estão disponíveis no site de documentação do Microsoft Azure. Links para o [Guia de Configuração de Clusters](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) e a versão herdada do [Guia de Criação de Clusters](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) levarão você a um site separado hospedado pelo GitHub. 

* Configurar o SMB

  Se você quiser permitir o acesso SMB ao cluster, bem como NFS, configure o SMB e ative-o. O SMB (às vezes chamado de CIFS) normalmente é usado para dar suporte a clientes do Microsoft Windows.

  Planejar e configurar o SMB é mais que clicar em alguns botões no painel de controle. De acordo com os requisitos do sistema, o SMB pode influenciar como os arquivistas principais são definidos, quantos vservers serão criados, como as junções e o namespace são configurados, as permissões de acesso e outras configurações.

  Para saber mais, leia a seção [Configurar o acesso SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) no Guia de Configuração de Clusters.

* Instalar licenças adicionais

  Se você quiser usar um armazenamento em nuvem que não seja o Blob do Azure, instale uma licença adicional do recurso. Entre em contato com o representante da Microsoft para saber detalhes sobre a compra da licença do FlashCloud<sup>TM</sup>. Os detalhes são explicados em [Adicionar armazenamento de back-end e configurar o namespace virtual](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Habilitar o suporte

O cluster do Azure FXT Edge Filer pode fazer uplaod automaticamente de dados de suporte sobre o cluster. Esses carregamentos permitem que a equipe forneça o melhor serviço possível para o cliente.

Siga estas etapas para configurar os carregamentos de suporte.

1. Navegue até a página de configurações **Cluster** > **Suporte**. Aceite a política de privacidade. 

   ![Captura de tela mostrando o painel de controle e uma janela pop-up com o botão Confirmar para aceitar a política de privacidade](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Clique no triângulo à esquerda de **Informações do cliente** para expandir a seção.
1. Clique no botão **Revalidar informações de carregamento**.
1. Defina o nome do suporte do cluster em **Nome do Cluster Exclusivo** - certifique-se de que ele identifique exclusivamente seu cluster para suportar a equipe.
1. Marque as caixas de **Monitoramento de Estatísticas**, **Upload de Informações Gerais** e **Upload de Informações de Falhas**.
1. Clique em **Enviar**.  

   ![Captura de tela que contém a seção de informações do cliente concluída da página de configurações de suporte](media/fxt-cluster-create/fxt-support-info.png)

1. Clique no triângulo à esquerda de **Secure Proactive Support (SPS)** para expandir a seção.
1. Marque a caixa **habilitar Link do SPS**.
1. Clique em **Enviar**.

   ![Captura de tela que contém a seção Suporte Proativo Seguro na página de configurações de suporte](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Próximas etapas

Após criar um cluster básico e aceitar a política de privacidade, adicione o restante dos nós de cluster. 

> [!div class="nextstepaction"]
> [Adicionar nós de cluster](fxt-add-nodes.md)
