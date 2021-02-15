---
title: Como funciona o roteamento com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que o Power Azure Dev Spaces e como funciona o roteamento
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977962"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Como funciona o roteamento com Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos Kubernetes e colaborar com sua equipe em um cluster do AKS (Serviço de Kubernetes do Azure). Depois que o projeto estiver sendo executado em um espaço de desenvolvimento, Azure Dev Spaces fornecerá recursos adicionais de roteamento e rede para seu projeto.

Este artigo descreve como o roteamento funciona com espaços de desenvolvimento.

## <a name="how-routing-works"></a>Como funciona o roteamento

Um espaço de desenvolvimento é criado sobre o AKS e usa os mesmos [conceitos de rede](../aks/concepts-network.md). Azure Dev Spaces também tem um serviço de *ingressmanager* centralizado e implanta seu próprio controlador de entrada no cluster AKs. O serviço *ingressmanager* monitora clusters AKs com espaços de desenvolvimento e aumenta o controlador de entrada Azure dev Spaces no cluster com objetos de entrada para roteamento para pods de aplicativo. O contêiner devspaces-proxy em cada pod adiciona um `azds-route-as` cabeçalho HTTP para o tráfego HTTP para um espaço de desenvolvimento com base na URL. Por exemplo, uma solicitação para a URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* obteria um cabeçalho HTTP com `azds-route-as: azureuser` . O contêiner devspaces-proxy não adicionará um `azds-route-as` cabeçalho se já houver um.

Quando uma solicitação HTTP é feita a um serviço de fora do cluster, a solicitação vai para o controlador de entrada. O controlador de entrada roteia a solicitação diretamente para o Pod apropriado com base em seus objetos de entrada e regras. O contêiner devspaces-proxy no pod recebe a solicitação, adiciona o `azds-route-as` cabeçalho com base na URL e, em seguida, roteia a solicitação para o contêiner do aplicativo.

Quando uma solicitação HTTP é feita a um serviço de outro serviço dentro do cluster, a solicitação primeiro passa pelo contêiner devspaces-proxy do serviço de chamada. O contêiner devspaces-proxy examina a solicitação HTTP e verifica o `azds-route-as` cabeçalho. Com base no cabeçalho, o contêiner devspaces-proxy pesquisará o endereço IP do serviço associado ao valor do cabeçalho. Se um endereço IP for encontrado, o contêiner devspaces-proxy redirecionará a solicitação para esse endereço IP. Se um endereço IP não for encontrado, o contêiner devspaces-proxy roteará a solicitação para o contêiner do aplicativo pai.

Por exemplo, os aplicativos *servicea* e *serviceB* são implantados em um espaço de desenvolvimento pai chamado *padrão*. o *servicea* se baseia em *serviceB* e faz chamadas http para ele. O usuário do Azure cria um espaço de desenvolvimento filho com base no espaço *padrão* chamado *azureuser*. O usuário do Azure também implanta sua própria versão do *servicea* em seu espaço filho. Quando uma solicitação é feita para *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Roteamento de Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. O controlador de entrada procura o IP para o Pod associado à URL, que é *servicea. azureuser*.
1. O controlador de entrada localiza o IP para o pod no espaço de desenvolvimento do usuário do Azure e roteia a solicitação para o Pod *servicea. azureuser* .
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a solicitação e adiciona `azds-route-as: azureuser` como um cabeçalho http.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* roteia a solicitação para o contêiner do aplicativo *servicea* no pod *servicea. azureuser* .
1. O aplicativo *servicea* no pod *servicea. azureuser* faz uma chamada para *serviceB*. O aplicativo *servicea* também contém código para preservar o `azds-route-as` cabeçalho existente, que nesse caso é `azds-route-as: azureuser` .
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a solicitação e pesquisa o IP de *serviceB* com base no valor do `azds-route-as` cabeçalho.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* não encontra um IP para *serviceB. azureuser*.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* procura o IP para *serviceB* no espaço pai, que é *serviceB. Default*.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* localiza o IP de *serviceB. Default* e roteia a solicitação para o Pod *serviceB. Default* .
1. O contêiner devspaces-proxy no *serviceB.* o Pod padrão recebe a solicitação e roteia a solicitação para o contêiner do aplicativo *ServiceB* no pod *serviceB. Default* .
1. O aplicativo *serviceB* no *serviceB. Pod padrão* retorna uma resposta para o Pod *servicea. azureuser* .
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a resposta e roteia a resposta para o contêiner do aplicativo *servicea* no pod *servicea. azureuser* .
1. O aplicativo *servicea* recebe a resposta e, em seguida, retorna sua própria resposta.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a resposta do contêiner de aplicativos *servicea* e roteia a resposta para o chamador original fora do cluster.

Todos os outros tráfegos TCP que não são HTTP passam pelo controlador de entrada e contêineres devspaces-proxy não modificados.

## <a name="sharing-a-dev-space"></a>Compartilhando um espaço de desenvolvimento

Ao trabalhar com uma equipe, você pode compartilhar um espaço de desenvolvimento em toda a equipe e criar espaços de desenvolvimento derivados. Um espaço de desenvolvimento pode ser usado por qualquer pessoa com acesso de colaborador ao grupo de recursos do espaço de desenvolvimento.

Você também pode criar um novo espaço de desenvolvimento que é derivado de outro espaço de desenvolvimento. Quando você cria um espaço de desenvolvimento derivado, o rótulo *azds.Io/Parent-Space=Parent-Space-Name* é adicionado ao namespace do espaço de desenvolvimento derivado. Além disso, todos os aplicativos do espaço de desenvolvimento pai são compartilhados com o espaço de desenvolvimento derivado. Se você implantar uma versão atualizada de um aplicativo no espaço de desenvolvimento derivado, ele só existirá no espaço de desenvolvimento derivado e o espaço de desenvolvimento pai permanecerá inalterado. Você pode ter um máximo de três níveis de espaços de desenvolvimento derivados ou espaços *avôs* .

O espaço de desenvolvimento derivado também roteará de forma inteligente as solicitações entre seus próprios aplicativos e os aplicativos compartilhados de seu pai. O roteamento funciona ao tentar rotear a solicitação para um aplicativo no espaço de desenvolvimento derivado e retornar ao aplicativo compartilhado do espaço de desenvolvimento pai. O roteamento fará o fallback para o aplicativo compartilhado no espaço do avô se o aplicativo não estiver no espaço pai.

Por exemplo:
* O *padrão* de espaço de desenvolvimento tem os aplicativos *servicea* e *serviceB*.
* O *azureuser* de espaço de desenvolvimento é derivado do *padrão*.
* Uma versão atualizada do *servicea* é implantada no *azureuser*.

Ao usar *azureuser*, todas as solicitações para *servicea* serão roteadas para a versão atualizada em *azureuser*. Uma solicitação para *serviceB* primeiro tentará ser roteada para a versão *azureuser* do *serviceB*. Como ele não existe, ele será roteado para a versão *padrão* do *serviceB*. Se a versão *azureuser* do *servicea* for removida, todas as solicitações para *servicea* voltarão a usar a versão *padrão* do *servicea*.

## <a name="next-steps"></a>Próximas etapas

Para ver um exemplo de como Azure Dev Spaces usa o roteamento para fornecer iteração e desenvolvimento rápidos, consulte [como a depuração remota de seu código com o Azure dev Spaces funciona][how-it-works-remote-debugging].


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md