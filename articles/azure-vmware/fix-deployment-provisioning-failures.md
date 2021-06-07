---
title: Suporte para implantação de solução do Azure VMware ou falha de provisionamento
description: Obtenha informações de sua nuvem privada da solução Azure VMware para arquivar uma solicitação de serviço para uma implantação de solução do Azure VMware ou falha de provisionamento.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542398"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Abrir uma solicitação de suporte para uma implantação de solução do Azure VMware ou falha de provisionamento

Este artigo mostra como abrir uma solicitação de [suporte](https://rc.portal.azure.com/#create/Microsoft.Support) e fornecer informações de chave para uma implantação de solução do Azure VMware ou falha de provisionamento. 

Quando houver uma falha em sua nuvem privada, você precisará abrir uma solicitação de suporte no portal do Azure. Para abrir uma solicitação de suporte, primeiro obtenha algumas informações importantes no portal do Azure:

- ID de Correlação
- ID do circuito do ExpressRoute do Azure
- Mensagens de erro

## <a name="get-the-correlation-id"></a>Obter a ID de correlação
 
Quando você cria uma nuvem privada ou qualquer recurso no Azure, uma ID de correlação para o recurso é gerada automaticamente para o recurso. Inclua a ID de correlação de nuvem privada em sua solicitação de suporte para abrir e resolver mais rapidamente a solicitação.

No portal do Azure, você pode obter a ID de correlação de um recurso de duas maneiras:

* Painel de **visão geral**
* Logs de implantação
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Obter a ID de correlação da visão geral do recurso

Veja um exemplo dos detalhes da operação de uma implantação de nuvem privada com falha, com a ID de correlação selecionada:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Captura de tela que mostra uma implantação de nuvem privada com falha com a ID de correlação selecionada.":::

Para acessar os resultados da implantação em um painel de **visão geral** da nuvem privada:

1. Na portal do Azure, selecione sua nuvem privada.

1. No menu à esquerda, selecione **visão geral**.

Depois que uma implantação é iniciada, os resultados da implantação são mostrados no painel **visão geral** da nuvem privada.

Copie e salve a ID de correlação de implantação de nuvem privada para incluir na solicitação de serviço.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Obter a ID de correlação do log de implantação

Você pode obter a ID de correlação para uma implantação com falha pesquisando o log de atividades de implantação no portal do Azure.

Para acessar o log de implantação:

1. Na portal do Azure, selecione sua nuvem privada e, em seguida, selecione o ícone notificações.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Captura de tela que mostra o ícone de notificações no portal do Azure.":::

1. No painel **notificações** , selecione **mais eventos no log de atividades**:

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Captura de tela que mostra mais eventos no link do log de atividades selecionado no painel notificações.":::

1. Para localizar a implantação com falha e sua ID de correlação, procure o nome do recurso ou outras informações que você usou para criar o recurso. 

    O exemplo a seguir mostra os resultados da pesquisa para um recurso de nuvem privada chamado pc03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Captura de tela que mostra os resultados da pesquisa para um recurso de nuvem privada de exemplo e o painel criar ou atualizar um nuvem particular.":::
 
1. Nos resultados da pesquisa no painel **log de atividades** , selecione o nome da operação da implantação com falha.

1. No painel **criar ou atualizar um nuvem particular** , selecione a guia **JSON** e procure `correlationId` no log que é mostrado. Copie o `correlationId` valor para incluí-lo em sua solicitação de suporte. 
 
## <a name="copy-error-messages"></a>Copiar mensagens de erro

Para ajudar a resolver o problema de implantação, inclua as mensagens de erro mostradas no portal do Azure. Selecione uma mensagem de aviso para ver um resumo dos erros:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Captura de tela que mostra os detalhes do erro na guia Resumo do painel erros, com o ícone de cópia selecionado.":::

Para copiar a mensagem de erro, selecione o ícone de cópia. Salve a mensagem copiada para incluir em sua solicitação de suporte.
 
## <a name="get-the-expressroute-id-uri"></a>Obter a ID do ExpressRoute (URI)
 
Talvez você esteja tentando dimensionar ou emparelhar uma nuvem privada existente com o circuito de ExpressRoute de nuvem privada e falhar. Nesse cenário, você precisa da ID do ExpressRoute para incluir em sua solicitação de suporte.

Para copiar a ID do ExpressRoute:

1. Na portal do Azure, selecione sua nuvem privada.
1. No menu à esquerda, em **gerenciar**, selecione **conectividade**. 
1. No painel direito, selecione a guia **ExpressRoute** .
1. Selecione o ícone de cópia para a **ID do ExpressRoute** e salve o valor a ser usado em sua solicitação de suporte.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Copie a ID do ExpressRoute para a área de transferência."::: 
 
## <a name="pre-validation-failures"></a>Falhas de pré-validação

Se a verificação de pré-validação da nuvem privada falhou (antes da implantação), uma ID de correlação não será gerada. Nesse cenário, você pode fornecer as seguintes informações em sua solicitação de suporte:

- Mensagens de erro e falha. Essas mensagens podem ser úteis em muitas falhas, por exemplo, para problemas relacionados à cota. É importante copiar essas mensagens e incluí-las na solicitação de suporte, conforme descrito neste artigo.
- Informações usadas para criar a nuvem privada da solução Azure VMware, incluindo:
  - Location
  - Resource group
  - Nome do recurso

## <a name="create-your-support-request"></a>Criar sua solicitação de suporte

Para obter informações gerais sobre como criar uma solicitação de suporte, consulte [como criar uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Para criar uma solicitação de suporte para uma implantação de solução do Azure VMware ou falha de provisionamento:

1. Na portal do Azure, selecione o ícone de **ajuda** e, em seguida, selecione **nova solicitação de suporte**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Captura de tela do painel nova solicitação de suporte no portal do Azure.":::

1. Insira ou selecione as informações necessárias:

   1. Na guia **Informações Básicas**:

      1. Para **tipo de problema**, selecione **problemas de configuração e** configuração.

      1. Para **subtipo de problema**, selecione **provisionar uma nuvem privada**.

   1. Na guia **detalhes** :

      1. Insira ou selecione as informações necessárias.

      1. Cole sua ID de correlação ou a ID do ExpressRoute onde essas informações são solicitadas. Se você não vir uma caixa de texto específica para esses valores, Cole-os na caixa de texto **fornecer detalhes sobre o problema** .

    1. Cole os detalhes do erro, incluindo as mensagens de erro ou de falha que você copiou, na caixa de texto **fornecer detalhes sobre o problema** .

1. Examine suas entradas e, em seguida, selecione **criar** para criar sua solicitação de suporte.
