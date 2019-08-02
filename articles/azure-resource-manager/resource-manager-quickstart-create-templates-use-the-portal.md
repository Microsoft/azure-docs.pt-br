---
title: Criar e implantar um modelo do Azure Resource Manager usando o portal do Azure | Microsoft Docs
description: Saiba como criar seu primeiro modelo do Azure Resource Manager usando o portal do Azure e como implantá-lo.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: d5deedb12afb1b766255ad0a9f247594ed70acaa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069293"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Início Rápido: Criar e implantar modelos do Azure Resource Manager usando o portal do Azure

Saiba como gerar um modelo do Resource Manager usando o portal do Azure e o processo de edição e implantação do modelo por meio do portal. Os modelos do Resource Manager são arquivos JSON que definem os recursos necessários para implantar sua solução. Para entender os conceitos associados à implantação e ao gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).

![diagrama do portal de início rápido do modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de concluir o tutorial, você implantará uma conta do Armazenamento do Azure. O mesmo processo pode ser usado para implantar outros recursos do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="generate-a-template-using-the-portal"></a>Gerar um modelo usando o portal do Azure

A criação de um modelo do Resource Manager do zero não é uma tarefa fácil, especialmente se você não está familiarizado com a implantação do Azure nem com o formato JSON. Usando o portal do Azure, você pode configurar um recurso, por exemplo, uma conta do Armazenamento do Azure. Antes de implantar o recurso, você pode exportar a configuração para um modelo do Resource Manager. Você pode salvar o modelo e reutilizá-lo no futuro.

Muitos desenvolvedores de modelos experientes usam esse método para gerar modelos quando tentam implantar recursos do Azure com os quais não estão familiarizados. Para obter mais informações sobre como exportar modelos usando o portal, consulte [Exportar grupos de recursos para modelos](./manage-resource-groups-portal.md#export-resource-groups-to-templates). A outra forma de localizar um modelo de trabalho é a partir dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento – blob, arquivo, tabela, fila**.

    ![Criar uma conta de armazenamento do Azure usando o portal do Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Insira as seguintes informações:

    |NOME|Valor|
    |----|----|
    |**Grupo de recursos**|Selecione **Criar** e especifique um nome de grupo de recursos de sua escolha. Na captura de tela, o nome do grupo de recursos é *mystorage1016rg*. Um grupo de recursos é um contêiner para os recursos do Azure. O grupo de recursos facilita o gerenciamento de recursos do Azure. |
    |**Nome**|Dê um nome exclusivo à conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo em todo o Azure e conter apenas letras minúsculas e números. O nome deve ter entre 3 e 24 caracteres. Se você receber uma mensagem de erro informando "O nome da conta de armazenamento 'mystorage1016' já foi preenchido", tente usar **&lt;seu nome>storage&lt;Data de hoje em MMDD>** , por exemplo **joaodolestorage1016**. Para obter mais informações, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions).|

    Você pode usar valores padrão para as outras propriedades.

    ![Criar uma configuração de conta de armazenamento do Azure usando o portal do Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Alguns dos modelos exportados exigem algumas edições antes da implantação.

4. Selecione **Revisar + criar** na parte inferior da tela. Não selecione **Criar** na próxima etapa.
5. Selecione **Baixar um modelo para automação** na parte inferior da tela. O portal mostra o modelo gerado:

    ![Gerar um modelo no portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    O painel principal mostra o modelo. É um arquivo JSON com seis elementos de nível superior – `schema`, `contentVersion`, `parameters`, `variables`, `resources` e `output`. Para obter mais informações, consulte [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md)

    Existem seis parâmetros definidos. Um deles é chamado **storageAccountName**. A segunda parte realçada na captura de tela anterior mostra como referenciar esse parâmetro no modelo. Na próxima seção, você deve editar o modelo para usar um nome gerado para a conta de armazenamento.

    No modelo, um recurso do Azure é definido. O tipo é `Microsoft.Storage/storageAccounts`. Examine como o recurso é definido e a estrutura de definição.
6. Selecione **Baixar** na parte superior da tela.
7. Abra o arquivo zip baixado e salve **template.json** no computador. Na próxima seção, você pode usar uma ferramenta de implantação de modelo para editar o modelo.
8. Selecione a guia **Parâmetro** para ver os valores fornecidos para os parâmetros. Anote esses valores, você precisará deles na próxima seção quando implantar o modelo.

    ![Gerar um modelo no portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Usando o arquivo de modelo e o arquivo de parâmetros é possível criar um recurso, neste tutorial, uma conta de armazenamento do Azure.

## <a name="edit-and-deploy-the-template"></a>Editar e implantar o modelo

O portal do Azure pode ser usado para executar uma edição básica do modelo. Neste início rápido, você usa uma ferramenta do portal chamada *Implantação de modelo*. A *Implantação de Modelo* é usada neste tutorial; portanto, você pode concluir todo o tutorial usando uma só interface – o portal do Azure. Para editar um modelo mais complexo, considere o uso do [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), que fornece funcionalidades mais avançadas de edição.

> [!IMPORTANT]
> A Implantação de modelo fornece uma interface para testar modelos simples. Não é recomendável usar esse recurso na produção. Em vez disso, armazene seus modelos em uma conta de armazenamento do Azure ou um repositório de código-fonte, como GitHub.

O Azure exige que cada serviço do Azure tenha um nome exclusivo. A implantação poderá falhar se você inserir um nome de conta de armazenamento que já existe. Para evitar esse problema, você modificará o modelo, de modo que ele use uma chamada de função de modelo `uniquestring()` para gerar um nome exclusivo de conta de armazenamento.

1. No Portal do Azure, selecione **Criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **ENTER**.
3. Selecione **Implantação de modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Selecione **Criar**.
5. Selecione **Criar seu próprio modelo no editor**.
6. Selecione **Carregar arquivo** e, em seguida, siga as instruções para carregar o template.json que você baixou na última seção.
7. Faça as três alterações a seguir no modelo:

    ![Modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Remova o parâmetro **storageAccountName**, conforme mostrado na captura de tela anterior.
   - Adicione uma variável chamada **storageAccountName**, conforme mostrado na captura de tela anterior:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       Duas funções de modelo são usadas aqui: `concat()` e `uniqueString()`.
   - Atualize o elemento de nome do recurso **Storage/storageaccounts** para usar a variável definida recentemente em vez do parâmetro:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     O modelo final deverá ficar assim:

     ```json
     {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "location": {
               "type": "string"
           },
           "accountType": {
               "type": "string"
           },
           "kind": {
               "type": "string"
           },
           "accessTier": {
               "type": "string"
           },
           "supportsHttpsTrafficOnly": {
               "type": "bool"
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       },
       "resources": [
           {
               "name": "[variables('storageAccountName')]",
               "type": "Microsoft.Storage/storageAccounts",
               "apiVersion": "2018-07-01",
               "location": "[parameters('location')]",
               "properties": {
                   "accessTier": "[parameters('accessTier')]",
                   "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
               },
               "dependsOn": [],
               "sku": {
                   "name": "[parameters('accountType')]"
               },
               "kind": "[parameters('kind')]"
           }
       ],
       "outputs": {}
     }
     ```
8. Clique em **Salvar**.
9. Insira os valores a seguir:

    |NOME|Valor|
    |----|----|
    |**Grupo de recursos**|Selecione o nome do grupo de recursos criado na última seção. |
    |**Localidade**|Selecione um local para a conta de armazenamento. Por exemplo, **Centro dos EUA**. |
    |**Tipo de conta**|Insira **Standard_LRS** para este início rápido. |
    |**Tipo**|Insira **StorageV2** para este início rápido. |
    |**Camada de acesso**|Insira **Quente** para este início rápido. |
    |**Somente tráfego HTTPS habilitado**| Selecione **true** para este início rápido. |
    |**Concordo com os termos e condições acima**|(selecionar)|

    Veja uma captura de tela de uma implantação de exemplo:

    ![Implantação de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Selecione **Comprar**.
11. Selecione o ícone de sino (notificações) na parte superior da tela para ver o status da implantação. Você deverá ver a mensagem **Implantação em andamento**. Aguarde até a conclusão da implantação.

    ![Notificação de implantação de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Selecione **Ir para o grupo de recursos** no painel de notificação. Você verá uma tela semelhante a:

    ![Grupo de recursos de implantação de modelos do Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Veja que a implantação foi bem-sucedida, e há apenas uma conta de armazenamento no grupo de recursos. O nome da conta de armazenamento é uma cadeia de caracteres exclusiva gerada pelo modelo. Para saber mais sobre como usar as contas de armazenamento do Azure, confira [Início rápido: carregar, baixar e listar blobs usando o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.  Você deverá ver a conta de armazenamento no grupo de recursos.
4. Selecione **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a gerar um modelo no portal do Azure e a implantar o modelo usando o portal. O modelo usado neste Início Rápido é um modelo simples com um recurso do Azure. Quando o modelo é complexo, é mais fácil usar o Visual Studio Code ou o Visual Studio para desenvolvê-lo. O próximo início rápido também mostra como implantar modelos usando o Azure PowerShell e a Interface da Linha de Comando (CLI) do Azure.

> [!div class="nextstepaction"]
> [Criar modelos usando o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
