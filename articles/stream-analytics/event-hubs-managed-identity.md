---
title: Usar identidades gerenciadas para acessar o Hub de eventos de um trabalho de Azure Stream Analytics (versão prévia)
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho de Azure Stream Analytics para entrada e saída dos hubs de eventos do Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018744"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Usar identidades gerenciadas para acessar o Hub de eventos de um trabalho de Azure Stream Analytics (versão prévia)

O Azure Stream Analytics dá suporte à autenticação de identidade gerenciada para entrada e saída dos hubs de eventos do Azure. Identidades gerenciadas eliminam as limitações de métodos de autenticação baseados no usuário, como a necessidade de autenticar devido a alterações de senha ou expirações de token de usuário que ocorrem a cada 90 dias. Quando você remove a necessidade de autenticar manualmente, suas implantações do Stream Analytics podem ser totalmente automatizadas.  

Uma identidade gerenciada é um aplicativo gerenciado registrado no Azure Active Directory que representa um determinado trabalho do Stream Analytics. O aplicativo gerenciado é usado para autenticar para um recurso de destino, incluindo hubs de eventos que estão atrás de um firewall ou rede virtual (VNet). Para obter mais informações sobre como ignorar firewalls, consulte [permitir acesso aos namespaces dos hubs de eventos do Azure por meio de pontos de extremidade privados](../event-hubs/private-link-service.md#trusted-microsoft-services).

Este artigo mostra como habilitar a identidade gerenciada para uma entrada ou saída de hubs de eventos de um trabalho de Stream Analytics por meio do portal do Azure.Antes de habilitar a identidade gerenciada, você deve primeiro ter um Stream Analytics trabalho e recurso do hub de eventos.

### <a name="limitation"></a>Limitação
Durante a visualização, a entrada de amostragem dos hubs de eventos em portal do Azure não funcionará ao usar o modo de autenticação de identidade gerenciada.

## <a name="create-a-managedidentity"></a>Criar uma identidade gerenciada  

Primeiro, você cria uma identidade gerenciada para seu trabalho do Azure Stream Analytics.  

1. Na portal do Azure, abra seu trabalho de Azure Stream Analytics.  

1. No menu de navegação à esquerda, selecione **identidade gerenciada**   localizada em *Configurar*. Em seguida, marque a caixa ao lado de **usar identidade gerenciada atribuída pelo sistema**   e selecione **salvar**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Identidade gerenciada atribuída ao sistema":::  

1. Uma entidade de serviço para a identidade do trabalho de Stream Analytics é criada em Azure Active Directory. O ciclo de vida da identidade recém-criada é gerenciado pelo Azure. Quando o trabalho do Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure.  

   Ao salvar a configuração, a OID (ID do objeto) da entidade de serviço é listada como a ID da Entidade de Segurança conforme mostrado abaixo:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="ID da entidade de segurança":::

   A entidade de serviço tem o mesmo nome que o trabalho do Stream Analytics. Por exemplo, se o nome do seu trabalho for  `MyASAJob` , o nome da entidade de serviço também será  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Conceder as permissões de trabalho de Stream Analytics para acessar o Hub de eventos

Para que o trabalho de Stream Analytics acesse seu hub de eventos usando a identidade gerenciada, a entidade de serviço que você criou deve ter permissões especiais para o Hub de eventos.

1. Vá para **controle de acesso (iam)** em seu hub de eventos.

1. Selecione **+ Adicionar** e **Adicionar atribuição de função**.

1. Na página **Adicionar atribuição de função** , insira as seguintes opções:

   |Parâmetro|Valor|
   |---------|-----|
   |Função|Proprietário de Dados de Hubs de Eventos do Azure|
   |Atribuir acesso a|Usuário, grupo ou entidade de serviço|
   |Selecionar|Insira o nome do seu trabalho de Stream Analytics|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Adicionar atribuição de função":::

1. Selecione **salvar** e aguarde um minuto para que as alterações sejam propagadas.

Você também pode conceder essa função no nível de namespace do hub de eventos, o que propagará naturalmente as permissões para todos os hubs de eventos criados nele. Ou seja, todos os hubs de eventos em um namespace podem ser usados como um recurso de autenticação de identidade gerenciada em seu trabalho de Stream Analytics.

## <a name="create-anevent-hub-input-or-output"></a>Criar uma entrada ou saída do hub de eventos  

Agora que sua identidade gerenciada está configurada, você está pronto para adicionar o recurso de Hub de eventos como uma entrada ou saída ao seu trabalho de Stream Analytics.  

### <a name="add-the-event-hub-as-an-input"></a>Adicionar o Hub de eventos como uma entrada 

1. Vá para o trabalho de Stream Analytics e navegue até a página de **entradas** em **topologia do trabalho**.

1. Selecione **Adicionar entrada de fluxo > Hub de eventos**. Na janela Propriedades de entrada, pesquise e selecione seu hub de eventos e selecione **identidade gerenciada** no menu suspenso *modo de autenticação* .

1. Preencha o restante das propriedades e selecione **salvar**.

### <a name="add-the-event-hub-as-an-output"></a>Adicionar o Hub de eventos como uma saída

1. Vá para o trabalho de Stream Analytics e navegue até a página de **saídas** em **topologia do trabalho**.

1. Selecione **adicionar > Hub de eventos**. Na janela Propriedades de saída, pesquise e selecione o Hub de eventos e selecione **identidade gerenciada** no menu suspenso *modo de autenticação* .

1. Preencha o restante das propriedades e selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

* [Saída dos hubs de eventos de Azure Stream Analytics](event-hubs-output.md)
* [Transmitir dados dos Hubs de Eventos](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
