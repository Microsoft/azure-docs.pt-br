---
title: Adicionar armazenamento de back-end ao cluster do Microsoft Azure FXT Edge Filer
description: Como configurar o armazenamento de back-end e o pseudonamespace voltado ao cliente para o Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 4a69aa7838e08c83b47c5f0248e821edf86b3990
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543295"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Tutorial: Adicionar armazenamento de back-end e configurar o namespace virtual 

Neste tutorial, você aprenderá a adicionar armazenamento de back-end ao cache e a configurar o sistema de arquivos virtual voltado ao cliente. 

O cluster se conecta aos sistemas de armazenamento de back-end para acessar a solicitação do cliente de dados e armazenar as alterações de modo mais permanente que no cache. 

O namespace é o pseudossistema de arquivos voltado ao cliente que permite trocar o armazenamento de back-end sem alterar os fluxos de trabalho no lado do cliente. 

Neste tutorial, você irá aprender: 

> [!div class="checklist"]
> * Como adicionar armazenamento de back-end ao cluster do Azure FXT Edge Filer 
> * Como definir o caminho voltado ao cliente para armazenamento

## <a name="about-back-end-storage"></a>Sobre o armazenamento de back-end

O cluster do Azure FXT Edge Filer usa uma definição de *arquivista principal* para vincular um sistema de armazenamento de back-end ao cluster do FXT.

O Azure FXT Edge Filer é compatível com vários sistemas de hardware NAS conhecidos. Além disso, pode usar contêineres vazios do Blob do Azure ou outro armazenamento em nuvem. 

Os contêineres de armazenamento em nuvem precisam estar vazios ao serem adicionados. Desse modo, o sistema operacional do FXT poderá gerenciar completamente todos os dados no volume de armazenamento em nuvem. É possível migrar os dados existentes para o contêiner em nuvem após adicionar o contêiner ao cluster como um arquivista principal.

Use o painel de controle para adicionar um arquivista principal ao sistema.

> [!NOTE]
> 
> Se você quiser usar o Amazon AWS ou o Google Cloud Storage, precisará instalar uma licença do recurso FlashCloud<sup>TM</sup>. Entre em contato com o representante da Microsoft para obter uma chave de licença. Em seguida, siga as instruções no guia de configuração herdado para [Adicionar ou remover licenças de recurso](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Há compatibilidade com o Armazenamento de Blobs do Azure na licença de software do Azure FXT Edge Filer. 

Para saber mais detalhes sobre como adicionar arquivistas principais, leia estas seções do Guia de Configuração de Clusters:

* Para saber mais sobre como escolher e se preparar para adicionar um arquivista principal, leia [Trabalhar com arquivistas principais](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Para saber mais detalhes sobre os pré-requisitos e ver instruções passo a passo, leia estes artigos:

  * [Adicionar um novo arquivista principal do NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Adicionar um novo arquivista principal em nuvem](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Após adicionar um arquivista principal, é possível atualizar as configurações na página "Detalhes do arquivista principal".

## <a name="add-a-core-filer"></a>Adicionar um arquivista principal

Defina um arquivista principal clicando no botão **Criar** em **Arquivista Principal** > **Gerenciar Arquivistas Principais** na página de configurações.

![Clique no botão "Criar" acima da lista de arquivistas principais na página "Gerenciar Arquivistas Principais"](media/fxt-cluster-config/create-core-filer-button.png)

O assistente **Adicionar Novo Arquivista Principal** orientará você durante o processo de criar um arquivista principal vinculado ao armazenamento de back-end. O Guia de Configuração de Clusters mostra as descrições passo a passo do processo, que são diferentes para o armazenamento NFS/NAS e para o armazenamento em nuvem (links acima). 

As subtarefas incluem:

* Especificar o tipo do arquivista principal (NAS ou em nuvem)

  ![Primeira página do assistente de novo arquivista principal NAS do hardware. A opção "Arquivista principal em nuvem" está desabilitada e mostra uma mensagem de erro sobre a falta de uma licença.](media/fxt-cluster-config/new-nas-1.png)

* Definir o nome do arquivista principal. Escolha um nome que ajuda os administradores de cluster a entender qual sistema de armazenamento que ele representa.

* Para os arquivistas principais do NAS, fornecer o FQDN (nome de domínio totalmente qualificado) ou o endereço IP. O FQDN é recomendado para todos os arquivistas principais e é obrigatório para acesso SMB.

* Selecionar uma política de cache – A segunda página do assistente lista as políticas de cache disponíveis para o novo arquivista principal. Para saber detalhes, leia a [seção de políticas de cache do Guia de Configuração de Clusters](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Segunda página do assistente de novo arquivista principal do hardware. O menu suspenso "Política de Cache" está aberto, mostrando várias opções desabilitadas e três opções válidas de política de cache (bypass, ler cache e ler/gravar cache).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Para o armazenamento em nuvem, é necessário especificar o serviço de nuvem e as credenciais de acesso, entre outros parâmetros. Para saber detalhes, leia [Serviço de nuvem e protocolo](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) no Guia de Configuração de Clusters.

  ![Informações sobre o arquivista principal em nuvem no assistente "Novo Arquivista Principal"](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Se você já tiver adicionado as credenciais de acesso à nuvem deste cluster, elas aparecerão na lista. Atualize e adicione as credenciais na página de configurações **Cluster** > **Credenciais de Nuvem**. 

Após preencher todas as configurações obrigatórias no assistente, clique no botão **Adicionar Arquivista** para enviar a alteração.

Depois de alguns instantes, o sistema de armazenamento será exibido na lista de arquivistas principais no **Painel** e poderá ser acessado por meio das páginas de configuração do arquivista principal.

![O arquivista principal "Flurry-NAS" na página de configurações "Gerenciar Arquivistas Principais", com a exibição de detalhes do arquivista expandida](media/fxt-cluster-config/core-filer-in-manage-page.png)

O arquivista principal nesta captura de tela não tem um vserver. É necessário vincular o arquivista principal a um vserver e criar uma junção de modo que os clientes possam acessar o armazenamento. Essas etapas serão descritas abaixo em [Configurar o namespace](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Configurar o namespace

O cluster do Azure FXT Edge Filer cria um sistema de arquivos virtual chamado *namespace do cluster*, que simplifica o acesso do cliente aos dados armazenados em vários sistemas de back-end. Como os clientes solicitam arquivos usando um caminho virtual, os sistemas de armazenamento podem ser adicionados ou substituídos sem precisar alterar o fluxo de trabalho do cliente. 

O namespace do cluster também permite apresentar os sistemas de armazenamento NAS e em nuvem em uma estrutura de arquivos semelhante. 

Os vservers do cluster mantêm o namespace e exibem conteúdo aos clientes. Há duas etapas para criar o namespace do cluster: 

1. Criar um vserver 
1. Configurar as junções entre os sistemas do armazenamento de back-end e os caminhos do sistema de arquivos voltado ao cliente 

### <a name="create-a-vserver"></a>Criar um vserver

Os VServers são servidores de arquivos virtuais que controlam os fluxos de dados entre o cliente e os arquivistas principais do cluster:

* Os VServers hospedam endereços IP voltados ao cliente
* Os VServers criam o namespace e definem as junções que mapeiam a estrutura de diretório virtual voltada ao cliente para as exportações no armazenamento de back-end
* Os VServers aplicam controles de acesso a arquivos, incluindo políticas de exportação de arquivistas principais e sistemas de autenticação de usuário
* Os VServers oferecem infraestrutura SMB

Antes de começar a configurar o vserver do cluster, leia a documentação vinculada e consulte seu representante da Microsoft para entender melhor o namespace e os vservers. Se estiver usando VLANs, [crie-as](fxt-configure-network.md#adjust-network-settings) antes de criar o vserver. 

Estas seções do Guia de Configuração de Clusters ajudarão você a conhecer melhor os recursos de vserver e namespace global do FXT:

* [Criar e trabalhar com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Usar um namespace global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Criar um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Você precisa pelo menos um vserver para o cluster. 

Para criar um novo vserver, são necessárias as seguintes informações:

* O nome a ser definido para o vserver

* O intervalo de endereços IP voltados ao cliente a ser processado pelo vserver

  É necessário fornecer um único intervalo de endereços IP contíguos ao criar o vserver. Você pode adicionar mais endereços mais tarde usando a página de configurações **Rede Voltada ao Cliente**.

* Se a rede tiver VLANs, qual VLAN usar para o vserver

Use a página de configurações **VServer** > **Gerenciar VServers** para criar um novo vserver. Para saber detalhes, leia [Criar um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) no Guia de Configuração de Clusters. 

![Janela pop-up para criar um novo vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Criar uma junção

Uma *junção* mapeia um caminho do armazenamento de back-end para o namespace visível pelo cliente.

É possível usar esse sistema para simplificar o caminho usado nos pontos de montagem do cliente e aumentar a capacidade de maneira fácil, porque um caminho virtual pode acomodar o armazenamento de vários arquivistas principais.

![Página do assistente "Adicionar Nova Junção" com as configurações preenchidas](media/fxt-cluster-config/add-junction-full.png)

Confira [**VServer** > **Namespace**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) no Guia de Configuração de Clusters ver todos os detalhes sobre a criação de uma junção de namespace.

![Página de configurações VServer > Namespace mostrando detalhes da junção](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Configurar regras de exportação

Com o vserver e o arquivista principal, personalize as regras e as políticas de exportação que controlam o modo como os clientes acessam os arquivos nas exportações do arquivista principal.

Primeiro, use a página **VServer** > **Regras de Exportação** para adicionar regras novas, modificar a política padrão ou criar uma política de exportação personalizada.

Depois, use a página **VServer** > **Políticas de Exportação** para aplicar a política personalizada às exportações do arquivista principal quando ele for acessado por meio do vserver.

Leia o artigo do Guia de Configuração de Clusters [Controlar o acesso às exportações do arquivista principal](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) para saber mais detalhes.


## <a name="next-steps"></a>Próximas etapas

Após adicionar armazenamento e configurar o namespace voltado ao cliente, conclua a configuração inicial do cluster: 

> [!div class="nextstepaction"]
> [Definir as configurações de rede do cluster](fxt-configure-network.md)
