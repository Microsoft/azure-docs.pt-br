---
title: Gerenciar ambientes de serviço de integração nos aplicativos lógicos do Azure
description: Verificar a integridade da rede e gerenciar aplicativos lógicos, conexões, conectores personalizados e contas de integração no ISE (ambiente do serviço de integração) para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92676111"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gerencie o ambiente do serviço de integração (ISE) nos Aplicativos Lógicos do Azure

Este artigo mostra como executar tarefas de gerenciamento para o [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), por exemplo:

* Gerencie os recursos, como aplicativos lógicos, conexões, contas de integração e conectores no ISE.

* Verifique a integridade da rede do ISE.

* Adicione capacidade, reinicie o ISE ou exclua o ISE, siga as etapas neste tópico. Para adicionar esses artefatos ao ISE, consulte [Adicionar artefatos ao seu ambiente do serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Exibir seu ISE

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa do portal, insira "ambientes do serviço de integração" e, em seguida, selecione **ambientes do serviço de integração**.

   ![Localizar ambientes de serviço de integração](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Na lista de resultados, selecione o ambiente do serviço de integração.

   ![Selecionar o ambiente de serviço de integração](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Continue nas próximas seções para localizar aplicativos lógicos, conexões, conectores ou contas de integração no ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Verificar a integridade da rede

No menu do ISE, em **configurações**, selecione **integridade da rede**. Esse painel mostra o status de integridade para suas sub-redes e dependências de saída em outros serviços.

![Verificar a integridade da rede](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Se a rede do ISE se tornar não íntegra, o Ambiente do Serviço de Aplicativo interno (ASE) que é usado pelo ISE também pode se tornar não íntegro. Se o ASE não estiver íntegro por mais de sete dias, o ASE será suspenso. Para resolver esse Estado, verifique sua configuração de rede virtual. Resolva os problemas encontrados e reinicie o ISE. Caso contrário, após 90 dias, o ASE suspenso será excluído e o ISE se tornará inutilizável. Portanto, certifique-se de manter seu ISE íntegro para permitir o tráfego necessário.
> 
> Para saber mais, consulte esses tópicos:
>
> * [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](../app-service/overview-diagnostics.md)
> * [Log de mensagens para Ambiente do Serviço de Aplicativo do Azure](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gerenciar seus aplicativos lógicos

Você pode exibir e gerenciar os aplicativos lógicos que estão no ISE.

1. No menu do ISE, em **configurações**, selecione **aplicativos lógicos**.

   ![Exibir aplicativos lógicos](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Para remover os aplicativos lógicos que você não precisa mais no ISE, selecione esses aplicativos lógicos e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

> [!NOTE]
> Se você excluir e recriar um aplicativo lógico filho, deverá salvar novamente o aplicativo lógico pai. O aplicativo filho recriado terá metadados diferentes.
> Se você não salvar novamente o aplicativo lógico pai depois de recriar seu filho, suas chamadas para o aplicativo lógico filho falharão com um erro de "não autorizado". Esse comportamento se aplica a aplicativos lógicos pai-filho, por exemplo, aqueles que usam artefatos em contas de integração ou chamam o Azure functions.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gerenciar conexões de API

Você pode exibir e gerenciar as conexões que foram criadas pelos aplicativos lógicos em execução no ISE.

1. No menu do ISE, em **configurações**, selecione **conexões de API**.

   ![Exibir conexões de API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Para remover as conexões que você não precisa mais no ISE, selecione essas conexões e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gerenciar conectores do ISE

Você pode exibir e gerenciar os conectores de API que são implantados no ISE.

1. No menu do ISE, em **configurações**, selecione **conectores gerenciados**.

   ![Exibir conectores gerenciados](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Para remover os conectores que você não deseja disponibilizar no ISE, selecione esses conectores e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gerenciar conectores personalizados

Você pode exibir e gerenciar os conectores personalizados que você implantou no ISE.

1. No menu do ISE, em **configurações**, selecione **conectores personalizados**.

   ![Localizar conectores personalizados](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Para remover conectores personalizados que não são mais necessários no ISE, selecione esses conectores e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gerenciar contas de integração

1. No menu do ISE, em **configurações**, selecione **contas de integração**.

   ![Localizar contas de integração](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Para remover contas de integração do ISE quando não forem mais necessárias, selecione as contas de integração e, em seguida, selecione **excluir**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adicionar capacidade de ISE

A unidade base do ISE Premium tem capacidade fixa, portanto, se você precisar de mais taxa de transferência, poderá adicionar mais unidades de escala, seja durante a criação ou depois. A SKU do desenvolvedor não inclui a capacidade de adicionar unidades de escala.

1. No [portal do Azure](https://portal.azure.com), vá para o ISE.

1. Para examinar o uso e as métricas de desempenho do ISE, no menu do ISE, selecione **visão geral**.

   ![Exibir o uso do ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Em **configurações**, selecione **escalar horizontalmente**. No painel **Configurar** , selecione uma destas opções:

   * [**Escala manual**](#manual-scale): escala com base no número de unidades de processamento que você deseja usar.
   * [**Dimensionamento automático personalizado**](#custom-autoscale): dimensione com base nas métricas de desempenho selecionando vários critérios e especificando as condições de limite para atender aos critérios.

   ![Captura de tela que mostra a página "scale out" com a "escala manual" selecionada.](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Dimensionamento manual

1. Depois de selecionar a **escala manual**, para **capacidade adicional**, selecione o número de unidades de dimensionamento que você deseja usar.

   ![Selecione o tipo de dimensionamento desejado](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Quando terminar, selecione **Salvar**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Dimensionamento automático personalizado

1. Depois de selecionar o **dimensionamento automático personalizado**, para o **nome da configuração de dimensionamento automático**, forneça um nome para sua configuração e, opcionalmente, selecione o grupo de recursos do Azure ao qual a configuração pertence.

   ![Forneça o nome para a configuração de dimensionamento automático e selecione grupo de recursos](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Para a condição **padrão** , selecione **escala com base em uma métrica** ou **escala para uma contagem de instâncias específica**.

   * Se você escolher baseado em instância, insira o número para as unidades de processamento, que é um valor de 0 a 10.

   * Se você escolher baseado em métrica, siga estas etapas:

     1. Na seção **regras** , selecione **Adicionar uma regra**.

     1. No painel **regra de dimensionamento** , configure os critérios e a ação a serem tomadas quando a regra for disparada.

     1. Para **limites de instância**, especifique estes valores:

        * **Mínimo**: o número mínimo de unidades de processamento a serem usadas
        * **Máximo**: o número máximo de unidades de processamento a ser usado
        * **Padrão**: se algum problema ocorrer durante a leitura das métricas de recurso e a capacidade atual estiver abaixo da capacidade padrão, o dimensionamento automático escalará para o número padrão de unidades de processamento. No entanto, se a capacidade atual exceder a capacidade padrão, o dimensionamento automático não será dimensionado horizontalmente.

1. Para adicionar outra condição, selecione **Adicionar condição de escala**.

1. Quando tiver concluído as configurações de dimensionamento automático, salve as alterações.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Reiniciar o ISE

Se você alterar o servidor DNS ou as configurações desse servidor, terá que reiniciar o ISE para que ele possa utilizar essas alterações. Reiniciar um ISE do SKU Premium não resulta em tempo de inatividade devido à redundância e aos componentes que reiniciam um de cada vez durante a reciclagem. No entanto, um ISE de SKU de desenvolvedor experimenta tempo de inatividade porque não existe nenhuma redundância. Para obter mais informações, consulte [SKUs do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. No [portal do Azure](https://portal.azure.com), vá para o ISE.

1. No menu ISE, selecione **visão geral**. Na barra de ferramentas visão geral, **reinicie** o.

   ![Reiniciar o ambiente do serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Excluir o ISE

Antes de excluir um ISE que não é mais necessário ou um grupo de recursos do Azure que contém um ISE, verifique se você não tem políticas ou bloqueios no grupo de recursos do Azure que contém esses recursos ou em sua rede virtual do Azure porque esses itens podem bloquear a exclusão.

Depois de excluir o ISE, talvez seja necessário aguardar até 9 horas antes de tentar excluir sua rede virtual ou sub-redes do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
