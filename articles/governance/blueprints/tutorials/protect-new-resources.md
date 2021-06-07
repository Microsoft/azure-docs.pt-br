---
title: 'Tutorial: Proteger novos recursos com bloqueios'
description: Neste tutorial, você usa as opções Somente Leitura e Não Excluir dos bloqueios de recursos do Azure Blueprints para proteger recursos recém-implantados.
ms.date: 03/08/2021
ms.topic: tutorial
ms.openlocfilehash: 87da0f5a1fff2feb103b32533c8d314fb7690f80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485734"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Tutorial: Proteger recursos novos com bloqueios de recursos do Azure Blueprints

Com os [bloqueios de recursos](../concepts/resource-locking.md) do Azure Blueprints, é possível proteger recursos recém-implantados contra interferências, até mesmo por uma conta com a função _Proprietário_. É possível adicionar essa proteção nas definições de blueprint dos recursos criados por um artefato de modelo do ARM (modelo do Azure Resource Manager). O bloqueio de recursos do Blueprint é definido durante a atribuição do Blueprint.

Neste tutorial, você concluirá estas etapas:

> [!div class="checklist"]
> - Criar uma definição de blueprint
> - Marcar a definição de blueprint como **Publicada**
> - Atribuir a definição do blueprint a uma assinatura existente (**definir bloqueios de recursos**)
> - Inspecionar o novo grupo de recursos
> - Cancelar a atribuição do blueprint para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint-definition"></a>Criar uma definição de blueprint

Primeiro, crie a definição de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione **Criar** em **Criar um blueprint**.

1. Localize o exemplo de blueprint **Blueprint em Branco** na parte superior da página. Selecione **Iniciar com blueprint em branco**.

1. Insira essas informações na guia **Noções básicas**:

   - **Nome do blueprint**: forneça um nome para a sua cópia do exemplo de blueprint. Para este tutorial, usaremos o nome **locked-storageaccount**.
   - **Descrição do blueprint**: adicione uma descrição da definição do blueprint. Use **Para testar o bloqueio em recursos de blueprint implantados**.
   - **Localização da definição**: selecione o botão de reticências (...) e, em seguida, selecione o grupo de gerenciamento ou a assinatura em que deseja salvar sua definição de blueprint.

1. Selecione a guia **Artefatos** na parte superior da página ou selecione **Avançar: Artefatos** na parte inferior da página.

1. Adicione um grupo de recursos no nível da assinatura:
   1. Selecione a linha **Adicionar artefato** na **Assinatura**.
   1. Selecione **Grupo de Recursos** em **Tipo de artefato**.
   1. Defina o **Nome de exibição do artefato** como **RGtoLock**.
   1. Deixe os campos **Nome do grupo de recursos** e **Local** em branco, mas verifique se a caixa de seleção está marcada em cada propriedade para torná-los **parâmetros dinâmicos**.
   1. Selecione **Adicionar** para adicionar o artefato ao blueprint.

1. Adicionar modelo sob o grupo de recursos:
   1. Selecione a linha **Adicionar artefato** na entrada **RGtoLock**.
   1. Selecione **Modelo do Azure Resource Manager** em **Tipo de artefato**, defina **Nome de exibição do artefato** como **StorageAccount** e deixe **Descrição** em branco.
   1. Na guia **Modelo**, cole o modelo do ARM a seguir na caixa do editor. Após colar o modelo, selecione **Adicionar** para adicionar o artefato ao blueprint.

      > [!NOTE]
      > Esta etapa define os recursos a serem implantados que são bloqueados pelo bloqueio de recursos do Blueprint, mas não inclui os bloqueios de recursos do Blueprint. Os bloqueios de recursos do Blueprint são definidos como um parâmetro da atribuição do Blueprint.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Na parte inferior da página, selecione **Salvar Rascunho**.

Esta etapa cria a definição do blueprint de exemplo na assinatura ou no grupo de gerenciamento selecionado.

Depois que a notificação do portal **Êxito ao salvar a definição de blueprint** for exibida, passe para a próxima etapa.

## <a name="publish-the-blueprint-definition"></a>Publique a definição do blueprint

Agora, sua definição de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser publicada antes de ser atribuída e implantada.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a definição de blueprint **locked-storageaccount** e selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. No novo painel à direita, insira **1.0** como a **Versão**. Essa propriedade será útil se você fizer uma alteração mais tarde. Insira **Notas de alteração**, como **Primeira versão publicada para bloquear recursos implantados de blueprint**. Em seguida, selecione **Publicar** na parte inferior da página.

Esta etapa possibilita atribuir o blueprint a uma assinatura. Depois que a definição de blueprint for publicada, ainda será possível fazer alterações. Se você fizer alterações, será necessário publicar a definição com um novo valor de versão para acompanhar as diferenças entre as versões da mesma definição de blueprint.

Depois que a notificação do portal **A publicação da definição do blueprint foi bem-sucedida** for exibida, passe para a próxima etapa.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição de blueprint

Depois que a definição de blueprint for publicada, será possível atribuí-la a uma assinatura dentro do grupo de gerenciamento em que ela foi salva. Nesta etapa, você fornecerá parâmetros para tornar cada implantação da definição de blueprint única.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a definição de blueprint **locked-storageaccount** e selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - **Noções básicas**

     - **Assinaturas**: selecione uma ou mais assinaturas que estão no grupo de gerenciamento em que você salvou sua definição de blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada assinatura, usando os parâmetros inseridos.
     - **Nome da atribuição**: o nome é preenchido previamente com base no nome da definição de blueprint. Queremos que essa atribuição represente o bloqueio de novo grupo de recursos, portanto, altere o nome da atribuição para **ssignment-locked-storageaccount-TestingBPLocks**.
     - **Localização**: selecione uma região na qual a identidade gerenciada será criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Para este tutorial, selecione **Leste dos EUA 2**.
     - **Versão de definição de blueprint**: selecione a versão publicada **1.0** da definição de blueprint.

   - **Atribuição de bloqueio**

     Selecione o modo de bloqueio de blueprint **Somente Leitura**. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

     > [!NOTE]
     > Esta etapa configura o bloqueio de recursos do Blueprint nos recursos implantados recentemente.

   - **Identidade gerenciada**

     Use a opção padrão: **Atribuída pelo sistema**. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parâmetros do artefato**

     Os parâmetros definidos nesta seção se aplicam ao artefato no qual eles foram definidos. Esses são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para cada artefato, defina o valor de parâmetro para o que é visto na coluna **Valor**.

     |Nome do artefato|Tipo de artefato|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos RGtoLock|Resource group|Nome|TestingBPLocks|Define o nome do novo grupo de recursos ao qual os bloqueios de blueprint serão aplicados.|
     |Grupo de recursos RGtoLock|Resource group|Location|Oeste dos EUA 2|Define a localização do novo grupo de recursos ao qual os bloqueios de blueprint serão aplicados.|
     |StorageAccount|Modelo do Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|O SKU de armazenamento. O valor padrão é _Standard_LRS_.|

1. Depois que você tiver inserido todos os parâmetros, selecione **Atribuir** na parte inferior da página.

Esta etapa implanta os recursos definidos e configura a **Atribuição de Bloqueio** selecionada. Pode levar até 30 minutos para aplicar bloqueios de blueprint.

Depois que a notificação do portal **A atribuição da definição do blueprint foi bem-sucedida** for exibida, passe para a próxima etapa.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar recursos implantados pela atribuição

A atribuição cria o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implantados pelo artefato de modelo do ARM. O novo grupo de recursos e o estado de bloqueio selecionado são mostrados na página de detalhes de atribuição.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Blueprints atribuídos** à esquerda. Use os filtros para localizar a atribuição de blueprint **assignment-locked-storageaccount-TestingBPLocks** e, em seguida, selecione-a.

   Nessa página, podemos ver que a atribuição foi bem-sucedida e que os recursos foram implantados com o respectivo estado de bloqueio do blueprint. Se a atribuição for atualizada, a lista suspensa **Operação de atribuição** mostrará detalhes sobre a implantação de cada versão de definição. É possível selecionar o grupo de recursos para abrir a página de propriedades.

1. Selecione o grupo de recursos **TestingBPLocks**.

1. Selecione a página **Controle de acesso (IAM)** à esquerda. Em seguida, selecione a guia **Atribuições de função**.

   Aqui, podemos ver que a atribuição de blueprint _assignment-locked-storageaccount-TestingBPLocks_ tem a função _Proprietário_. Ela tem essa função, porque a função foi usada para implantar e bloquear o grupo de recursos.

1. Selecione a guia **Negar atribuições**.

   A atribuição de blueprint criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantado para impor o modo de bloqueio **Somente Leitura** do blueprint. A atribuição de negação impede que alguém com os direitos adequados execute ações específicas na guia **Atribuições de função**. A atribuição de negação afeta _Todas as entidades_.

   Para obter informações sobre a exclusão de uma entidade de segurança de uma atribuição de negação, veja [bloqueio de recursos de blueprint](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está impedindo todas as operações com **\* *_ e a configuração _* Ação**, mas permite o acesso de leitura excluindo **\*/read** por meio de **NotActions**.

1. Na trilha do portal do Azure, selecione **TestingBPLocks – Controle de acesso (IAM)** . Depois, selecione a página **Visão geral** à esquerda e, em seguida, o botão **Excluir grupo de recursos**. Insira o nome **TestingBPLocks** para confirmar a exclusão e, em seguida, selecione **Excluir** na parte inferior do painel.

   A notificação do portal **Falha ao excluir grupo de recursos TestingBPLocks** é exibida. O erro informa que, embora sua conta tenha permissão para excluir o grupo de recursos, o acesso é negado pela atribuição de blueprint. Lembre-se de que selecionamos o modo de bloqueio de blueprint **Somente Leitura** durante a atribuição de blueprint. O bloqueio de blueprint impede que uma conta com permissão, até mesmo _Proprietário_, exclua o recurso. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

Estas etapas mostram que nossos recursos implantados agora estão protegidos com bloqueios de blueprint que impedem a exclusão indesejada, até mesmo de uma conta que tem permissão para excluir os recursos.

## <a name="unassign-the-blueprint"></a>Cancelar a atribuição do blueprint

A última etapa é remover a atribuição da definição de blueprint. A remoção da atribuição não remove os artefatos associados.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Blueprints atribuídos** à esquerda. Use os filtros para localizar a atribuição de blueprint **assignment-locked-storageaccount-TestingBPLocks** e, em seguida, selecione-a.

1. Selecione **Cancelar atribuição do blueprint** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e, em seguida, selecione **OK**.

   Quando a atribuição do blueprint é removida, os bloqueios de blueprint também são removidos. Os recursos podem ser excluídos mais uma vez por uma conta com permissões adequadas.

1. Selecione **Grupos de recursos** no menu do Azure e, em seguida, selecione **TestingBPLocks**.

1. Selecione a página **Controle de acesso (IAM)** à esquerda e, em seguida, selecione a guia **Atribuições de função**.

A segurança do grupo de recursos mostra que a atribuição de blueprint não tem mais acesso de _Proprietário_.

Depois que a notificação do portal **A remoção da atribuição do blueprint foi bem-sucedida** for exibida, passe para a próxima etapa.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando concluir este tutorial, exclua estes recursos:

- Grupo de recursos _TestingBPLocks_
- Definição de blueprint _locked-storageaccount_

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a proteger novos recursos implantados com o Azure Blueprints. Para saber mais sobre o Azure Blueprints, prossiga para o artigo de ciclo de vida do blueprint.

> [!div class="nextstepaction"]
> [Saiba mais sobre o ciclo de vida do blueprint](../concepts/lifecycle.md)