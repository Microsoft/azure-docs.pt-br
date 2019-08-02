---
title: Configuração do cluster do Microsoft Azure FXT Edge Filer – adicionar nós
description: Como adicionar nós ao cache de armazenamento do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: d84b98b4ab936bbb6978144eb2e89b5e19df7069
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543185"
---
# <a name="tutorial-add-cluster-nodes"></a>Tutorial: Adicionar nós de cluster 

Um novo cluster do Azure FXT Edge Filer é criado com apenas um nó. Você deve adicionar pelo menos mais dois nós e habilitar a alta disponibilidade antes de fazer outra configuração. 

Este tutorial explica como adicionar nós do cluster e habilitar o recurso de Alta Disponibilidade (HA). 

Neste tutorial, você irá aprender: 

> [!div class="checklist"]
> * Como adicionar nós ao cluster FXT
> * Como habilitar a HA

São necessários cerca de 45 minutos para concluir as etapas neste tutorial.

Antes de iniciar o tutorial, ative os nós que você deseja adicionar e [defina as senhas inicias deles](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Carregar a página de Nós de Cluster

Abra o Painel de Controle do cluster em um navegador Web e entre como administrador. (As instruções detalhadas estão no artigo de visão geral, em [Abrir as páginas de Configurações](fxt-cluster-create.md#open-the-settings-pages)).

O Painel de Controle mostra a guia **Painel** ao abrir. 

![Painel do Painel de Controle (primeira guia)](media/fxt-cluster-config/dashboard-1-node.png)

Esta imagem mostra o painel de um cluster recém-criado, com um único nó.

## <a name="2-locate-the-node-to-add"></a>2. Localizar o nó para adicionar

Para adicionar nós, clique na guia **Configurações** e escolha a página **Nós FXT** na seção **Cluster**.

![Guia Configurações do painel de controle (segunda guia) com o Cluster > Nós FXT carregados](media/fxt-cluster-config/settings-fxt-nodes.png)

A lista **Nós FXT – Não associados** mostra todos os nós FXT não associados (a maioria dos data centers tem apenas alguns). Encontre os nós FXT que você deseja adicionar ao cluster.

> [!Tip] 
> Se você não conseguir encontrar o nó que deseja na lista **Não associados**, verifique se ele atende a estes requisitos:
> 
> * Está ligado e tem uma [senha raiz configurada](fxt-node-password.md).
> * Ele está conectado a uma rede que você pode acessar. Se você usa VLANs, ele deve estar na mesma VLAN que o cluster.
> * Ele pode ser detectado com o protocolo do Bonjour. 
>
>   Algumas configurações do firewall bloqueiam as portas TCP/UDP usadas pelo Bonjour, o que impede o sistema operacional do FXT de detectar automaticamente os nós.
> 
> Se o nó que você deseja adicionar não estiver na lista, experimente estas soluções: 
> 
> * Clique no botão **Descoberta Manual** para encontrá-lo pelo endereço IP.
> 
> * Atribua manualmente endereços IP temporários. Isso é raro, mas pode ser necessário se você usar VLANs e os nós não estiverem na rede correta ou sua rede não permitir endereços IP atribuídos automaticamente. Siga as instruções na versão herdada deste documento para [definir manualmente um endereço IP estático](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

O nome do nó, o endereço IP, a versão do software e o status de qualificação são exibidos na lista. Normalmente, a coluna **Status** diz "Deseja ingressar" ou descreve um problema de hardware ou do sistema que torna o nó não qualificado para se associar ao cluster.

A coluna **Ações** tem botões que permitem adicionar o nó ao cluster ou atualizar o software dele. O botão de atualização instala automaticamente a versão do software correspondente aos nós que já estão no cluster.

Todos os nós em um cluster devem usar a mesma versão do sistema operacional, mas não é necessário atualizar o software antes de adicionar um nó. Depois de clicar no botão **Permitir Associação**, o processo de associação ao cluster verifica e instala automaticamente o software do sistema operacional correspondente à versão no cluster.

Para saber mais sobre as opções nesta página, leia [**Cluster** > **Nós FXT**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) no Guia de Configuração do Cluster.

## <a name="3-click-the-allow-to-join-button"></a>3. Clique no botão "Permitir Associação" 

Clique no botão **Permitir Associação*** na coluna **Ações** do nó que você deseja adicionar.

Depois de clicar no botão, o status do nó pode mudar conforme o software é atualizado na preparação para adicioná-lo ao cluster. 

A imagem abaixo mostra um nó que está no processo de associação do cluster (provavelmente, ele está obtendo uma atualização do sistema operacional antes de ser adicionado). Nenhum botão é exibido na coluna **Ações** para os nós que estão em processo de adição ao cluster.

![uma linha da tabela de nó, mostrando um nome do nó, endereço IP, versão do software, a mensagem "Associação autorizada" e uma última coluna em branco](media/fxt-cluster-config/node-join-in-process.png)

Em alguns instantes, o novo nó deverá ser exibido na lista de nós do cluster na parte superior da página de configurações dos **Nós FXT**. 

Repita esse processo para adicionar outros nós ao cluster. Você não precisa esperar a conclusão da associação de um cluster para iniciar outra.

## <a name="enable-high-availability"></a>Habilitar alta disponibilidade

Depois de adicionar um segundo nó ao cluster, você verá uma mensagem de aviso no Painel do painel de controle informando que o recurso de alta disponibilidade não está configurado. 

A alta disponibilidade (HA) permite que os nós de cluster compensem uns aos outros, caso algum deles falhe. A HA está habilitada por padrão.

![A guia Painel com a mensagem "O cluster tem mais de um nó, mas a HA não está habilitada..." na tabela Condições](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Não habilite a HA até que você tenha pelo menos três nós no cluster.

Siga este procedimento para ativar a HA: 

1. Carregue a página **Alta Disponibilidade** na seção **Cluster** da guia **Configurações**.

   ![Página de configuração de alta disponibilidade (Cluster > Alta Disponibilidade). A caixa de seleção "Habilitar HA" está na parte superior e o botão Enviar está na parte inferior.](media/fxt-cluster-config/enable-ha.png)

2. Clique na caixa rotulada **Habilitar HA** e clique no botão **Enviar**. 

Um alerta será exibido no **Painel** para confirmar que a HA está habilitada.

![A tabela Painel mostrando a mensagem "A HA está totalmente configurada"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Próximas etapas

Depois de adicionar todos os nós no cluster, continue a instalação configurando o armazenamento de longo prazo do seu cluster.

> [!div class="nextstepaction"]
> [Adicionar armazenamento de back-end e configurar o namespace virtual](fxt-add-storage.md)