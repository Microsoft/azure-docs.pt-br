---
title: O que são os Serviços de Nuvem do Azure | Microsoft Docs
description: Saiba mais sobre o que são os Serviços de Nuvem do Azure.
services: cloud-services
author: georgewallace
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 61369d51056607d8176d301afa945c7c77895b12
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359713"
---
# <a name="overview-of-azure-cloud-services"></a>Visão geral dos Serviços de Nuvem do Azure
Os Serviços de Nuvem do Azure são um exemplo de uma [PaaS](https://azure.microsoft.com/overview/what-is-paas/) (Plataforma como Serviço). Como o [Serviço de Aplicativo do Azure](../app-service/overview.md), essa tecnologia foi desenvolvida para dar suporte aos aplicativos escalonáveis, confiáveis e baratos de operar. Da mesma forma que o Serviço de Aplicativo é hospedado em máquinas virtuais (VMs), os Serviços de Nuvem do Azure também são. No entanto, você tem mais controle sobre as VMs. Você pode instalar seu próprio software em VMs que usam os Serviços de Nuvem do Azure acessá-las remotamente.

![Diagrama dos Serviços de Nuvem do Azure](./media/cloud-services-choose-me/diagram.png)

Mais controle também significa menos facilidade de uso. A menos que você precise de opções de controle adicional, geralmente é mais rápido e fácil colocar um aplicativo Web em funcionamento no recurso Aplicativos Web do Serviço de Aplicativo, em comparação com os Serviços de Nuvem do Azure.

Há dois tipos de funções do Serviço de Nuvem do Azure. A única diferença entre os dois é o modo como sua função é hospedada nas VMs:

* **Função Web**: Implanta e hospeda o aplicativo automaticamente por IIS.

* **Função de trabalho**: não usa IIS e executa o aplicativo de modo autônomo.

Por exemplo, um aplicativo simples pode usar apenas uma única função web, atendendo a um site. Um aplicativo mais complexo pode usar uma função Web para lidar com solicitações de entrada de usuários e, em seguida, passar essas solicitações a uma função de trabalho para processamento. (Essa comunicação pode usar [Barramento de Serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) ou [Armazenamento de Filas do Azure](../storage/common/storage-introduction.md).)

Como a figura anterior sugere, todas as VMs em um único aplicativo executado no mesmo serviço de nuvem. Os usuários acessam o aplicativo por meio de um único endereço IP público, com a carga de solicitações balanceada automaticamente entre as VMs do aplicativo. A plataforma [dimensiona e implanta](cloud-services-how-to-scale-portal.md) as VMs em um aplicativo de Serviços de Nuvem do Azure de uma maneira que evita um único ponto de falha de hardware.

Ainda que os aplicativos sejam executados em VMs, é importante entender que os Serviços de Nuvem do Azure fornecem PaaS, e não IaaS (infraestrutura como serviço). Aqui está uma forma de pensar sobre isso. Com IaaS, como as Máquinas Virtuais do Microsoft Azure, você primeiro cria e configura o ambiente de que seu aplicativo é executado. Em seguida, implanta seu aplicativo nesse ambiente. Você é responsável por gerenciar grande parte desse mundo, realizando tarefas como a implantação de novas versões com aplicações de patches do sistema operacional em cada VM. Em contrapartida, na PaaS, é como se o ambiente já existisse. Tudo o que você precisa fazer é implantar o aplicativo. O gerenciamento da plataforma em que ele é executado, incluindo implantar novas versões do sistema operacional, é manipulado para você.

## <a name="scaling-and-management"></a>Escala e gerenciamento
Com os Serviços de Nuvem do Azure, você não cria máquinas virtuais. Em vez disso, fornece um arquivo de configuração que informa ao Azure quantas delas você deseja, como "três instâncias da função Web" e "duas instâncias da função de trabalho." A plataforma, então, as criará para você. Você ainda escolhe o [tamanho](cloud-services-sizes-specs.md) que as VMs devem ter, mas não as cria explicitamente. Se o aplicativo precisa manipular uma carga de trabalho maior, é possível solicitar mais VMs e o Azure cria essas instâncias. Se a carga diminuir, essas instâncias poderão ser desligadas e parar de pagar por elas.

Um aplicativo dos Serviços de Nuvem do Azure geralmente é disponibilizado aos usuários por um processo de duas etapas. Primeiro, o desenvolvedor [carrega o aplicativo](cloud-services-how-to-create-deploy-portal.md) na área de preparo da plataforma. Quando o desenvolvedor estiver pronto para ativar o aplicativo, ele usará o Portal do Azure para trocar o preparo pela produção. Essa [troca entre o preparo e a produção](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) pode ser feita sem tempo de inatividade, o que permite que um aplicativo em execução seja atualizado para uma nova versão sem perturbar seus usuários.

## <a name="monitoring"></a>Monitorando
Os Serviços de Nuvem do Azure também fornecem monitoramento. Assim como as Máquinas Virtuais, ele detecta um servidor físico com falha e reinicia as VMs que estavam em execução nesse servidor em um novo computador. Mas os Serviços de Nuvem do Azure também detectam as VMs e os aplicativos com falha, não apenas as falhas de hardware. Diferentemente das Máquinas Virtuais, os Serviços têm um representante dentro de cada função Web e de trabalho, de modo que podem iniciar novas instâncias de aplicativo e VMs quando uma falha ocorre.

A natureza da PaaS dos Serviços de Nuvem do Azure tem outras implicações também. Uma das mais importantes é que os aplicativos com base nessa tecnologia devem ser escritos para serem executados corretamente quando houver falha de alguma instância da função de trabalho ou da Web. Para isso, um aplicativo dos Serviços de Nuvem do Azure não deve manter o estado de suas próprias VMs no sistema de arquivos. Ao contrário das VMs criadas com Máquinas Virtuais, gravações feitas para VMs de Serviços de Nuvem do Azure não são persistentes. Não há nada como um disco de dados de Máquinas Virtuais. Um aplicativo dos Serviços de Nuvem do Azure deve gravar explicitamente todo o estado no Banco de Dados SQL do Azure, em blobs, tabelas ou em algum outro armazenamento externo. Criar aplicativos dessa forma facilita o dimensionamento deles e os torna mais resistente a falhas, que são metas importantes dos Serviços de Nuvem do Azure.

## <a name="next-steps"></a>Próximas etapas
* [Criar um aplicativo de serviço de nuvem em .NET](cloud-services-dotnet-get-started.md) 
* [Criar um aplicativo de serviço de nuvem em Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Criar um aplicativo de serviço de nuvem em PHP](../cloud-services-php-create-web-role.md) 
* [Criar um aplicativo de serviço de nuvem no Python](cloud-services-python-ptvs.md)



