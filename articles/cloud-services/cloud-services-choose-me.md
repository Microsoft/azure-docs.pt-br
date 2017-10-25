---
title: "Opções de computação do Azure - Serviços de Nuvem | Microsoft Docs"
description: "Saiba mais sobre opções de hospedagem de computação do Azure e como elas funcionam: Serviço de Aplicativo, Serviços de Nuvem e Máquinas Virtuais"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e8053b74e0e4d721523f49bcbb9e33b08bb7a1dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="should-i-choose-cloud-services-or-something-else"></a>Devo escolher os serviços de nuvem ou algo mais?
Os Serviços de Nuvem do Azure são a escolha certa para você? A Azure fornece diferentes modelos de hospedagem para executar aplicativos. Cada um deles fornece um conjunto de serviços diferente, portanto, qual deles você escolhe depende exatamente do que você está tentando fazer.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>Fale-me sobre os serviços de nuvem
Os Serviços de Nuvem são um exemplo de [PaaS (Plataforma como Serviço)](https://azure.microsoft.com/overview/what-is-paas/). Como o [Serviço de Aplicativo](../app-service/app-service-web-overview.md), essa tecnologia foi desenvolvida para dar suporte aos aplicativos escalonáveis, confiáveis e baratos de operar. Assim como o Serviço de Aplicativo é hospedado em VMs, os Serviços de Nuvem também são. No entanto, você tem mais controle sobre as VMs. Você pode instalar seu próprio software nas VMs do Serviço de Nuvem e controlá-los remotamente.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

Mais controle também significa menos facilidade de uso. A menos que você precise de opções de controle adicional, geralmente é mais rápido e fácil colocar um aplicativo Web em funcionamento em Aplicativos Web no Serviço de Aplicativo, em comparação com os Serviços de Nuvem.

Há dois tipos de funções de Serviço de Nuvem. A única diferença entre os dois é o modo como sua função é hospedada na máquina virtual.

* **Função Web**  
Implanta e hospeda o aplicativo automaticamente por IIS.

* **Função de trabalho**  
Não usa IIS e executa o aplicativo de modo autônomo.

Por exemplo, um aplicativo simples pode usar apenas uma única função web, atendendo a um site. Um aplicativo mais complexo pode usar uma função web para lidar com solicitações de entrada de usuários, em seguida, passar essas solicitações a uma função de trabalho para processamento. (Essa comunicação pode usar o [Barramento de Serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) ou as [Filas do Azure](../storage/common/storage-introduction.md)).

Como a figura anterior sugere, todas as VMs em um único aplicativo executado no mesmo serviço de nuvem. Os usuários acessam o aplicativo por meio de um único endereço IP público, com a carga de solicitações balanceada automaticamente entre as VMs do aplicativo. A plataforma [dimensiona e implanta](cloud-services-how-to-scale.md) as VMs em um aplicativo de Serviços de Nuvem de uma maneira que evita um único ponto de falha de hardware.

Ainda que os aplicativos sejam executados em máquinas virtuais, é importante entender que os Serviços de Nuvem fornecem PaaS, e não IaaS. Aqui está uma forma de pensar: com a IaaS, como as Máquinas Virtuais do Azure, primeiramente você cria e configura o ambiente em que seu aplicativo é executado e, em seguida, implanta o aplicativo nesse ambiente. Você é responsável por gerenciar grande parte desse mundo, realizando tarefas como a implantação de novas versões com aplicações de patches do sistema operacional em cada VM. Em contrapartida, na PaaS, é como se o ambiente já existisse. Tudo o que você precisa fazer é implantar o aplicativo. O gerenciamento da plataforma em que ele é executado, incluindo implantar novas versões do sistema operacional, é manipulado para você.

## <a name="scaling-and-management"></a>Escala e gerenciamento
Com os Serviços de Nuvem, você não cria máquinas virtuais. Em vez disso, fornece um arquivo de configuração que informa ao Azure quantas delas você deseja, como **três instâncias da função Web** e **duas instâncias da função de trabalho**, e a plataforma as cria para você.  Você ainda escolhe o [tamanho](cloud-services-sizes-specs.md) que as VMs devem ter, mas não as cria explicitamente. Se o aplicativo precisa manipular uma carga de trabalho maior, é possível solicitar mais VMs e o Azure cria essas instâncias. Se a carga diminuir, essas instâncias poderão ser desligadas e parar de pagar por elas.

Um aplicativo dos Serviços de Nuvem geralmente é disponibilizado aos usuários por um processo de duas etapas. Primeiro, o desenvolvedor [carrega o aplicativo](cloud-services-how-to-create-deploy.md) na área de preparo da plataforma. Quando o desenvolvedor estiver pronto para ativar o aplicativo, ele usará o Portal do Azure para trocar o preparo pela produção. Essa [troca entre o preparo e a produção](cloud-services-nodejs-stage-application.md) pode ser feita sem tempo de inatividade, o que permite que um aplicativo em execução seja atualizado para uma nova versão sem perturbar seus usuários.

## <a name="monitoring"></a>Monitoramento
Os Serviços de Nuvem também fornecem monitoramento. Assim como as Máquinas Virtuais do Azure, ele detecta um servidor físico com falha e reinicia as VMs que estavam em execução nesse servidor em um novo computador. Mas os Serviços de Nuvem também detectam as VMs e os aplicativos com falha, não apenas as falhas de hardware. Diferentemente das Máquinas Virtuais, os Serviços têm um representante dentro de cada função Web e de trabalho, de modo que podem iniciar novas instâncias de aplicativo e VMs quando uma falha ocorre.

A natureza da PaaS dos Serviços de Nuvem tem outras implicações também. Uma das mais importantes é que os aplicativos com base nessa tecnologia devem ser escritos para serem executados corretamente quando houver falha de alguma instância da função de trabalho ou da Web. Para isso, um aplicativo dos Serviços de Nuvem não deve manter o estado de suas próprias VMs no sistema de arquivos. Diferentemente das VMs criadas com as Máquinas Virtuais do Azure, as gravações feitas nas VMs dos Serviços de Nuvem não são persistentes; não há nada parecido com um disco de dados das Máquinas Virtuais. Um aplicativo dos Serviços de Nuvem deve gravar explicitamente todo o estado no Banco de Dados SQL, em blobs, tabelas ou em algum outro armazenamento externo. Criar aplicativos dessa forma facilita o dimensionamento deles e os torna mais resistente a falhas, que são metas importantes dos Serviços de Nuvem.

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de serviço de nuvem em .NET](cloud-services-dotnet-get-started.md)  
[Criar um aplicativo de serviço de nuvem em Node.js](cloud-services-nodejs-develop-deploy-app.md)  
[Criar um aplicativo de serviço de nuvem em PHP](../cloud-services-php-create-web-role.md)  
[Criar um aplicativo de serviço de nuvem no Python](cloud-services-python-ptvs.md)

