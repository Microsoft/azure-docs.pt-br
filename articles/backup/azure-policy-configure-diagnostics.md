---
title: Definir configurações de diagnóstico de cofre em escala
description: Definir Log Analytics configurações de diagnóstico para todos os cofres em um determinado escopo usando Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 55461937381f7551c42714c835d4755ab65f175b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92171521"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Definir configurações de diagnóstico de cofre em escala

A solução de relatórios fornecida pelo backup do Azure aproveita Log Analytics (LA). Para que os dados de qualquer cofre fornecido sejam enviados à LA, uma [configuração de diagnóstico](./backup-azure-diagnostic-events.md) precisa ser criada para esse cofre.

Muitas vezes, adicionar uma configuração de diagnóstico manualmente por cofre pode ser uma tarefa complicada. Além disso, qualquer novo cofre criado também precisa ter as configurações de diagnóstico habilitadas para poder exibir relatórios para este cofre.

Para simplificar a criação de configurações de diagnóstico em escala (com LA como o destino), o backup do Azure fornece um [Azure Policy](../governance/policy/index.yml)interno. Essa política adiciona uma configuração de diagnóstico LA a todos os cofres em uma determinada assinatura ou grupo de recursos. As seções a seguir fornecem instruções sobre como usar essa política.

## <a name="supported-scenarios"></a>Cenários com suporte

* A política pode ser aplicada de uma vez a todos os cofres dos serviços de recuperação em uma determinada assinatura (ou a um grupo de recursos dentro da assinatura). O usuário que atribui a política precisa ter acesso de **proprietário** à assinatura à qual a política é atribuída.

* O espaço de trabalho LA conforme especificado pelo usuário (para o qual os dados de diagnóstico serão enviados) pode estar em uma assinatura diferente dos cofres aos quais a política é atribuída. O usuário precisa ter acesso de **leitor**, **colaborador** ou **proprietário** à assinatura na qual o espaço de trabalho especificado existe.

* O escopo do grupo de gerenciamento não tem suporte no momento.

* Atualmente, a política interna não está disponível em nuvens nacionais.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Atribuindo a política interna a um escopo

Para atribuir a política para cofres no escopo necessário, siga as etapas abaixo:

1. Entre no portal do Azure e navegue até o painel de **política** .
2. Selecione **definições** no menu à esquerda para obter uma lista de todas as políticas internas nos recursos do Azure.
3. Filtre a lista para **Category = backup**. Localize a política denominada **[Preview]: implantar configurações de diagnóstico para o cofre dos serviços de recuperação para log Analytics espaço de trabalho para categorias específicas de recursos**.

    ![Painel de definição de política](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Selecione o nome da política. Você será redirecionado para a definição detalhada dessa política.

    ![Definição de política detalhada](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Selecione o botão **atribuir** na parte superior do painel. Isso redireciona você para o painel **atribuir política** .

6. Em **noções básicas**, selecione os três pontos ao lado do campo **escopo** . Isso abre um painel de contexto à direita no qual você pode selecionar a assinatura na qual a política será aplicada. Opcionalmente, você também pode selecionar um grupo de recursos, para que a política seja aplicada somente para cofres em um determinado grupo de recursos.

    ![Noções básicas de atribuição de política](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Em **parâmetros**, insira as seguintes informações:

    * **Nome do perfil** -o nome que será atribuído às configurações de diagnóstico criadas pela política.
    * **Espaço de trabalho log Analytics** -o espaço de trabalho log Analytics ao qual a configuração de diagnóstico deve ser associada. Os dados de diagnóstico de todos os cofres no escopo da atribuição de política serão enviados para o espaço de trabalho especificado.

    * **Nome da marca de exclusão (opcional) e valor da marca de exclusão (opcional)** -você pode optar por excluir cofres contendo um determinado nome de marca e valor da atribuição de política. Por exemplo, se você **não** quiser que uma configuração de diagnóstico seja adicionada a esses cofres que tenham uma marca ' istest ' definida com o valor ' Yes ', deverá inserir ' istest ' no campo de **nome da marca de exclusão** e ' Yes ' no campo valor da **marca de exclusão** . Se algum (ou ambos) desses dois campos forem deixados vazios, a política será aplicada a todos os cofres relevantes, independentemente das marcas que eles contêm.

    ![Parâmetros de atribuição de política](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Criar uma tarefa de correção** -depois que a política for atribuída a um escopo, todos os novos cofres criados nesse escopo receberão automaticamente as configurações de diagnóstico de la configuradas (dentro de 30 minutos a partir do momento da criação do cofre). Para adicionar uma configuração de diagnóstico aos cofres existentes no escopo, você pode disparar uma tarefa de correção no momento da atribuição da política. Para disparar uma tarefa de correção, marque a caixa de seleção **criar uma tarefa de correção**.

    ![Correção de atribuição de política](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navegue até a guia **revisar + criar** e selecione **criar**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Em quais condições a tarefa de correção será aplicada a um cofre?

A tarefa de correção é aplicada aos cofres que não são compatíveis, de acordo com a definição da política. Um cofre não será compatível se atender a uma das seguintes condições:

* Nenhuma configuração de diagnóstico está presente para o cofre.
* As configurações de diagnóstico estão presentes para o cofre, mas nenhuma das configurações tem **todos** os eventos específicos do recurso habilitados com la como destino e o **recurso específico** selecionado na alternância.

Portanto, mesmo que um usuário tenha um cofre com o evento AzureBackupReport habilitado no modo AzureDiagnostics (que tem suporte nos relatórios de backup), a tarefa de correção ainda se aplicará a esse cofre, pois o modo específico ao recurso é a maneira recomendada de criar configurações de diagnóstico, no [futuro](./backup-azure-diagnostic-events.md#legacy-event).

Além disso, se um usuário tiver um cofre com apenas um subconjunto dos seis eventos específicos do recurso habilitado, a tarefa de correção será aplicada a esse cofre, já que os relatórios de backup funcionarão como esperado somente se todos os seis eventos específicos do recurso estiverem habilitados.

> [!NOTE]
>
> Se um cofre tiver uma configuração de diagnóstico existente com um **subconjunto de categorias específicas de recurso** habilitado, configurado para enviar dados para um espaço de trabalho específico, digamos ' espaço de trabalho X ', a tarefa de correção falhará (somente para esse cofre) se o espaço de trabalho de destino fornecido na atribuição de política for o **mesmo** ' espaço de trabalho x '.
>
>Isso ocorre porque, se os eventos habilitados por duas configurações de diagnóstico diferentes no mesmo recurso **se sobrepõem** em algum formato, as configurações não podem ter o mesmo espaço de trabalho que o destino. Você precisará resolver manualmente essa falha, navegando até o cofre relevante e configurando uma configuração de diagnóstico com um espaço de trabalho de LA diferente como o destino.
>
> Observe que a tarefa de correção **não** falhará se a configuração de diagnóstico existente for apenas AzureBackupReport habilitada com o espaço de trabalho X como o destino, já que, nesse caso, não haverá nenhuma sobreposição entre os eventos habilitados pela configuração existente e os eventos habilitados pela configuração criada pela tarefa de correção.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como usar relatórios de backup](./configure-reports.md)
* [Saiba mais sobre o Azure Policy](../governance/policy/index.yml)
* [Usar Azure Policy para habilitar o backup automaticamente para todas as VMs em um escopo de atribuições](./backup-azure-auto-enable-backup.md)
