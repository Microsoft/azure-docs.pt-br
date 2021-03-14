---
title: Monitorar a integridade das instâncias do serviço de aplicativo
description: Saiba como monitorar a integridade das instâncias do serviço de aplicativo usando a verificação de integridade.
keywords: serviço de aplicativo do Azure, aplicativo Web, verificação de integridade, tráfego de rota, instâncias íntegras, caminho, monitoramento,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 7d6f9564328f81b71c62a4243c5f4cc209a29d8f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714469"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Monitorar instâncias do serviço de aplicativo usando a verificação de integridade

![Falha na verificação de integridade][2]

Este artigo usa a verificação de integridade no portal do Azure para monitorar instâncias do serviço de aplicativo. A verificação de integridade aumenta a disponibilidade do seu aplicativo removendo instâncias não íntegras. O [plano do serviço de aplicativo](./overview-hosting-plans.md) deve ser dimensionado para duas ou mais instâncias para usar a verificação de integridade. O caminho de verificação de integridade deve verificar os componentes críticos do seu aplicativo. Por exemplo, se seu aplicativo depende de um banco de dados e um sistema de mensagens, o ponto de extremidade de verificação de integridade deve se conectar a esses componentes. Se o aplicativo não puder se conectar a um componente crítico, o caminho deverá retornar um código de resposta de nível 500 para indicar que o aplicativo não está íntegro.

## <a name="what-app-service-does-with-health-checks"></a>O que o serviço de aplicativo faz com verificações de integridade

- Quando um caminho é fornecido em seu aplicativo, a verificação de integridade executa ping nesse caminho em todas as instâncias do aplicativo do serviço de aplicativo em intervalos de 1 minuto.
- Se uma instância não responder com um código de status entre 200-299 (inclusivo) após duas ou mais solicitações, ou não responder ao ping, o sistema determinará que ele não está íntegro e o removerá.
- Após a remoção, a verificação de integridade continuará a executar o ping na instância não íntegra. Se ele continuar a responder sem êxito, o serviço de aplicativo reiniciará a VM subjacente em um esforço para retornar a instância a um estado íntegro.
- Se uma instância permanecer não íntegra durante uma hora, ela será substituída por uma nova instância.
- Além disso, ao escalar verticalmente ou horizontalmente, o serviço de aplicativo executa ping no caminho de verificação de integridade para garantir que novas instâncias estejam prontas.

> [!NOTE]
> A verificação de integridade não segue redirecionamentos de 302. No máximo uma instância será substituída por hora, com um máximo de três instâncias por dia por plano do serviço de aplicativo.
>

## <a name="enable-health-check"></a>Habilitar verificação de integridade

![Navegação de verificação de integridade no portal do Azure][3]

- Para habilitar a verificação de integridade, navegue até a portal do Azure e selecione seu aplicativo do serviço de aplicativo.
- Em **monitoramento**, selecione **verificação de integridade**.
- Selecione **habilitar** e forneça um caminho de URL válido em seu aplicativo, como `/health` ou `/api/health` .
- Clique em **Save** (Salvar).

> [!CAUTION]
> Alterações de configuração da verificação de integridade reinicie seu aplicativo. Para minimizar o impacto nos aplicativos de produção, é recomendável [Configurar slots de preparo](deploy-staging-slots.md) e alternar para produção.
>

### <a name="configuration"></a>Configuração

Além de configurar as opções de verificação de integridade, você também pode definir as seguintes [configurações de aplicativo](configure-common.md):

| Nome da configuração do aplicativo | Valores permitidos | Descrição |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | O número máximo de falhas de ping. Por exemplo, quando definido como `2` , suas instâncias serão removidas após `2` pings com falha. Além disso, quando você estiver aumentando ou reduzindo, o serviço de aplicativo executará ping no caminho de verificação de integridade para garantir que novas instâncias estejam prontas. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0–100 | Para evitar a intensização de instâncias íntegras, não mais do que metade das instâncias serão excluídas. Por exemplo, se um plano do serviço de aplicativo for dimensionado para quatro instâncias e três não estiverem íntegras, no máximo duas serão excluídas. As outras duas instâncias (uma íntegra e uma não íntegra) continuarão a receber solicitações. No pior cenário em que todas as instâncias não estão íntegras, nenhuma será excluída. Para substituir esse comportamento, defina a configuração de aplicativo como um valor entre `0` e `100` . Um valor mais alto significa que mais instâncias não íntegras serão removidas (o padrão é 50). |

#### <a name="authentication-and-security"></a>Autenticação e segurança

A verificação de integridade se integra aos recursos de autenticação e autorização do serviço de aplicativo. Nenhuma configuração adicional será necessária se esses recursos de segurança estiverem habilitados. No entanto, se você estiver usando seu próprio sistema de autenticação, o caminho de verificação de integridade deverá permitir o acesso anônimo. Se o site for somente HTTP **s** habilitado, a solicitação de verificação de integridade será enviada via http **s**.

Grandes equipes de desenvolvimento empresarial geralmente precisam aderir aos requisitos de segurança para APIs expostas. Para proteger o ponto de extremidade de verificação de integridade, você deve primeiro usar recursos como [restrições de IP](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [certificados de cliente](app-service-ip-restrictions.md#set-an-ip-address-based-rule)ou uma rede virtual para restringir o acesso ao aplicativo. Você pode proteger o ponto de extremidade de verificação de integridade exigindo as `User-Agent` correspondências de solicitação de entrada `ReadyForRequest/1.0` . A User-Agent não pode ser falsificada, pois a solicitação já estava protegida por recursos de segurança anteriores.

## <a name="monitoring"></a>Monitoramento

Depois de fornecer o caminho de verificação de integridade do aplicativo, você pode monitorar a integridade do seu site usando Azure Monitor. Na folha **verificação de integridade** no portal, clique nas **métricas** na barra de ferramentas superior. Isso abrirá uma nova folha em que você poderá ver o status de integridade histórico do site e criar uma nova regra de alerta. Para obter mais informações sobre como monitorar seus sites, [consulte o guia em Azure monitor](web-sites-monitor.md).

## <a name="next-steps"></a>Próximas etapas
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png