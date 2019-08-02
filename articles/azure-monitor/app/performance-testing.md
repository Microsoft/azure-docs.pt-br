---
title: Desempenho e teste de carga com o Azure Application Insights | Microsoft Docs
description: Configurar testes de carga com o Azure Application Insights e desempenho
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305204"
---
# <a name="performance-testing"></a>Testes de desempenho

> [!NOTE]
> O serviço de teste de carga baseado em nuvem foi preterida. Para obter mais informações sobre a substituição, a disponibilidade do serviço e serviços alternativos podem ser encontradas [aqui](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights permite que você gerar testes de carga para seus sites. Como o [testes de disponibilidade](monitor-web-app-availability.md), você pode enviar qualquer solicitações básicas ou [solicitações de várias etapas](availability-multistep.md) do Azure em todo o mundo, os agentes de teste. Testes de desempenho permitem que você simule até 20.000 usuários simultâneos para até 60 minutos.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de desempenho, você precisa primeiro criar um recurso do Application Insights. Se você já tiver criado um recurso de prosseguir para a próxima seção.

No portal do Azure, selecione **criar um recurso** > **ferramentas de desenvolvedor** > **Application Insights** e criar um Application Insights recurso.

## <a name="configure-performance-testing"></a>Configurar testes de desempenho

Se essa for a primeira vez em que a criação de select do teste de desempenho **organização definido** e escolher uma organização de DevOps do Azure para ser a origem para os testes de desempenho.

Sob **configurar**, acesse **teste de desempenho** e clique em **New** para criar um teste.

![Preencha pelo menos o URL do seu site](./media/performance-testing/new-performance-test.png)

Para criar um teste de desempenho básico, selecione um tipo de teste do **teste Manual** e preencha as configurações desejadas para seu teste.

|Configuração| Valor máximo
|----------|------------|
| Carga de usuário | 20.000 |
| Duração (minutos)  | 60 |  

Depois que o teste é criado, clique em **executar teste**.

Quando o teste for concluído, você verá resultados semelhantes aos resultados abaixo:

![Resultados de teste](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurar o teste da web do Visual Studio

Projetos de teste do Application Insights funcionalidades de teste de desempenho avançado são criadas sobre a carga e desempenho do Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Próximas etapas

* [Testes da web de várias etapas](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)