---
title: Habilitar e gerenciar a exclusão reversível para contêineres (visualização)
titleSuffix: Azure Storage
description: Habilite a exclusão reversível de contêiner (versão prévia) para recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4b25458cc934097b5477c174ae7ecc47762e2929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280293"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Habilitar e gerenciar a exclusão reversível para contêineres (visualização)

A exclusão reversível de contêiner (versão prévia) protege seus dados contra acidentalmente ou excluídos de modo acidental ou excluído. Quando a exclusão reversível de contêiner está habilitada para uma conta de armazenamento, um contêiner e seu conteúdo podem ser recuperados após serem excluídos, dentro de um período de retenção especificado por você.

Se houver uma possibilidade de que seus dados possam ser acidentalmente modificados ou excluídos por um aplicativo ou outro usuário da conta de armazenamento, a Microsoft recomenda ativar a exclusão reversível de contêiner. Este artigo mostra como habilitar a exclusão reversível para contêineres. Para obter mais detalhes sobre exclusão reversível de contêiner, incluindo como registrar-se para a versão prévia, consulte [exclusão reversível para contêineres (versão prévia)](soft-delete-container-overview.md).

Para proteção de dados de ponta a ponta, a Microsoft recomenda que você também habilite a exclusão reversível para BLOBs e controle de versão de BLOB. Para saber como habilitar também a exclusão reversível para BLOBs, consulte [habilitar e gerenciar a exclusão reversível para BLOBs](soft-delete-blob-enable.md). Para saber como habilitar o controle de versão de BLOB, consulte [controle de versão de blob](versioning-overview.md).

## <a name="enable-container-soft-delete"></a>Habilitar exclusão reversível de contêiner

Você pode habilitar ou desabilitar a exclusão reversível de contêiner para a conta de armazenamento a qualquer momento usando o portal do Azure ou um modelo de Azure Resource Manager.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para habilitar a exclusão reversível de contêiner para sua conta de armazenamento usando portal do Azure, siga estas etapas:

1. No [Portal do Azure](https://portal.azure.com/), navegue até sua conta de armazenamento.
1. Localize as configurações de **proteção de dados** em **serviço blob**.
1. Defina a propriedade **exclusão reversível do contêiner** como *habilitada*.
1. Em **políticas de retenção**, especifique por quanto tempo os contêineres de exclusão reversível são retidos pelo armazenamento do Azure.
1. Salve suas alterações.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Captura de tela mostrando como habilitar a exclusão reversível de contêiner no portal do Azure":::

# <a name="template"></a>[Modelo](#tab/template)

Para habilitar a exclusão reversível de contêiner com um modelo de Azure Resource Manager, crie um modelo que defina a propriedade **containerDeleteRetentionPolicy** . As etapas a seguir descrevem como criar um modelo no portal do Azure.

1. Na portal do Azure, escolha **criar um recurso**.
1. Em **Pesquisar no Marketplace**, digite **implantação de modelo**e pressione **Enter**.
1. Escolha **implantação de modelo**, escolha **criar**e, em seguida, escolha **criar seu próprio modelo no editor**.
1. No editor de modelo, Cole o JSON a seguir. Substitua o  `<account-name>`   espaço reservado pelo nome da sua conta de armazenamento.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Especifique o período de retenção. O valor padrão é 7.
1. Salve o modelo.
1. Especifique o grupo de recursos da conta e escolha o botão **revisar + criar**   para implantar o modelo e habilitar a exclusão reversível do contêiner.

## <a name="view-soft-deleted-containers"></a>Exibir contêineres excluídos de modo reversível

Quando a exclusão reversível está habilitada, você pode exibir contêineres excluídos de modo reversível no portal do Azure. Os contêineres excluídos por software são visíveis durante o período de retenção especificado. Depois que o período de retenção expirar, um contêiner excluído por software será excluído permanentemente e não ficará mais visível.

Para exibir contêineres excluídos de modo reversível no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure e exiba a lista de seus contêineres.
1. Alterne a opção Mostrar contêineres excluídos para incluir contêineres excluídos na lista.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Captura de tela mostrando como habilitar a exclusão reversível de contêiner no portal do Azure":::

## <a name="restore-a-soft-deleted-container"></a>Restaurar um contêiner excluído por software

Você pode restaurar um contêiner excluído de maneira reversível e seu conteúdo dentro do período de retenção. Para restaurar um contêiner excluído de maneira reversível no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure e exiba a lista de seus contêineres.
1. Exiba o menu de contexto do contêiner que você deseja restaurar e escolha **Cancelar exclusão** no menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Captura de tela mostrando como habilitar a exclusão reversível de contêiner no portal do Azure":::

## <a name="next-steps"></a>Próximas etapas

- [Exclusão reversível para contêineres (visualização)](soft-delete-container-overview.md)
- [Exclusão reversível para blobs](soft-delete-blob-overview.md)
- [Controle de versão de BLOB](versioning-overview.md)
