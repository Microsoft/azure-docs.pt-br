---
title: "Solucionar problemas de implantação do serviço de nuvem | Microsoft Docs"
description: "Existem alguns problemas comuns que você experimentar ao implantar um serviço de nuvem no Azure. Este artigo fornece soluções para alguns deles."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 7/26/2017
ms.author: v-six
ms.openlocfilehash: 29529978a2f6a1454c7a97b5f5baac214a90d63b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Solucionar problemas de implantação do serviço de nuvem
Quando você implanta um pacote de aplicativos do serviço de nuvem no Azure, é possível obter informações sobre a implantação no painel **Propriedades** no portal do Azure. Você pode usar os detalhes nesse painel para ajudá-lo a solucionar problemas com o serviço de nuvem, e pode fornecer essas informações ao suporte do Azure ao abrir uma nova solicitação de suporte.

Você pode encontrar o painel **Propriedades** da seguinte maneira:

* No portal do Azure, clique na implantação de seu serviço de nuvem, clique em **Todas as configurações** e em **Propriedades**.
* No portal clássico do Azure, clique na implantação de seu serviço de nuvem e clique em **PAINEL**, localizado no canto inferior direito da página (em **visão rápida**). Lembre-se de que não há um rótulo de "Propriedades" nesse painel.

> [!NOTE]
> Você pode copiar o conteúdo do painel **Propriedades** para a área de transferência clicando no ícone no canto superior direito do painel.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: não consigo acessar meu site, mas minha implantação foi iniciada e todas as instâncias de função estão prontas
O link da URL do site exibido no portal não inclui a porta. A porta padrão para sites é 80. Se o seu aplicativo for configurado para ser executado em uma porta diferente, você deverá adicionar o número de porta correto à URL ao acessar o site.

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** do Portal do Azure, verifique as portas para as instâncias de função (em **Pontos de Extremidade de Entrada**).
3. Se a porta não for 80, adicione o valor de porta correto à URL ao acessar o aplicativo. Para especificar uma porta não padrão, digite a URL, seguida de dois-pontos (:) e do número da porta sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: minhas instâncias de funções foram recicladas sem que eu tenha realizado nenhuma ação
A recuperação de serviço ocorre automaticamente quando o Azure detecta nós com problemas e, assim, move as instâncias de função para novos nós. Quando isso ocorrer, você poderá ver suas instâncias de função serem recicladas automaticamente. Para descobrir se a recuperação de serviço ocorreu:

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** do Portal do Azure, examine as informações e determine se a recuperação de serviço ocorreu durante o tempo em que você observou a reciclagem das funções.

As funções também serão recicladas aproximadamente uma vez por mês durante as atualizações do SO host e do SO convidado.  
Para saber mais, confira a postagem no blog [A instância de função é reinicializada devido às atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: não consigo fazer uma permuta de VIP. Recebi uma mensagem de erro
Uma permuta de VIP não é permitida se uma atualização de implantação estiver em andamento. As atualizações de implantação podem ocorrer automaticamente quando:

* Um novo sistema operacional convidado está disponível e você está configurado para receber atualizações automáticas.
* A recuperação de serviço ocorre.

Para descobrir se uma atualização automática está impedindo que você faça uma permuta de VIP:

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** do Portal do Azure, observe o valor de **Status**. Se ele estiver **Pronto**, verifique a **Última operação** para ver se ocorreu uma operação recentemente que possa impedir a permuta de VIP.
3. Repita as etapas 1 e 2 para a implantação de produção.
4. Se uma atualização automática estiver em andamento, aguarde sua conclusão antes de tentar fazer a permuta de VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: uma instância de função está alternando entre os estados Iniciado, Inicializando, Ocupado e Parado
Essa condição pode indicar um problema com o código, pacote ou arquivo de configuração do aplicativo. Nesse caso, você deverá ver o status sendo alterado em intervalos de alguns minutos e o Portal do Azure poderá indicar algo como **Reciclando**, **Ocupado** ou **Inicializando**. Isso indica que há algo errado com o aplicativo que está impedindo a execução da instância de função.

Para saber mais sobre como solucionar o problema, confira a postagem de blog [Dados de Diagnóstico de Computação do Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [Problemas comuns que fazem as funções serem recicladas](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: meu aplicativo parou de funcionar
1. No portal do Azure, clique na instância de função.
2. No painel **Propriedades** do Portal do Azure, considere as seguintes condições para resolver o problema:
   * Se a instância de função tiver sido interrompida recentemente (é possível verificar o valor de **Anular contagem**), a implantação pode estar sendo atualizada. Aguarde para ver se a instância de função retoma o funcionamento por conta própria.
   * Se a instância de função estiver **Ocupada**, verifique o código do aplicativo para ver se o evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) foi manipulado. Talvez seja necessário adicionar ou corrigir algum código que manipula esse evento.
   * Examine os dados de diagnóstico e os cenários de solução de problemas na postagem no blog [Dados de diagnóstico de computação de PaaS do Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Se você reciclar o serviço de nuvem, redefina as propriedades da implantação apagando de maneira efetiva as informações do problema original.
>
>

## <a name="next-steps"></a>Próximas etapas
Confira mais [artigos sobre solução de problemas](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-allocation-failures) para serviços de nuvem.

Para saber como solucionar problemas das funções do serviço de nuvem usando os dados de diagnóstico do computador Azure PaaS, confira a [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
