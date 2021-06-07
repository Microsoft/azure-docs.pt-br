---
title: Azure Monitor pastas de trabalho trazem seu próprio armazenamento
description: Saiba como proteger sua pasta de trabalho salvando o conteúdo da pasta de trabalho no armazenamento
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100606450"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Traga seu próprio armazenamento para salvar pastas de trabalho

Há ocasiões em que você pode ter uma consulta ou alguma lógica de negócios que você deseja proteger. As pastas de trabalho do fornecem uma opção para proteger as pastas de trabalho salvando o conteúdo das pastas de trabalho no armazenamento. A conta de armazenamento pode ser criptografada com chaves gerenciadas pela Microsoft ou você pode gerenciar a criptografia fornecendo suas próprias chaves. [Consulte a documentação do Azure em Criptografia do Serviço de Armazenamento.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Salvando pasta de trabalho com identidades gerenciadas

1. Antes de salvar a pasta de trabalho em seu armazenamento, você precisará criar uma identidade gerenciada (todos os serviços > identidades gerenciadas) e fornecer `Storage Blob Data Contributor` acesso à sua conta de armazenamento. [Consulte a documentação do Azure sobre identidades gerenciadas.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Captura de tela mostrando a adição de uma atribuição de função](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Crie uma nova pasta de trabalho.
3. Selecione o botão **salvar** para salvar a pasta de trabalho.
4. Há uma opção para `Save content to an Azure Storage Account` , marque a caixa de seleção para salvar em uma conta de armazenamento do Azure.

    ![Captura de tela mostrando uma caixa de diálogo salva](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Selecione a conta de armazenamento desejada e o contêiner. A lista de contas de armazenamento é da assinatura selecionada acima.

    ![Captura de tela mostrando uma caixa de diálogo Salvar com a opção de armazenamento](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Em seguida, selecione **alterar** para selecionar uma identidade gerenciada criada anteriormente.

    [![Captura de tela mostrando a caixa de diálogo Alterar identidade](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Depois de selecionar as opções de armazenamento, pressione **salvar** para salvar a pasta de trabalho.

## <a name="limitations"></a>Limitações

- Ao salvar no armazenamento personalizado, não é possível fixar partes individuais da pasta de trabalho em um painel, pois os Pins individuais contêm informações protegidas no próprio painel. Ao usar o armazenamento personalizado, você só pode fixar links para a pasta de trabalho em painéis.
- Depois que uma pasta de trabalho tiver sido salva no armazenamento personalizado, ela sempre será salva no armazenamento personalizado e não poderá ser desativada. Para salvar em outro lugar, você pode usar "salvar como" e optar por não salvar a cópia no armazenamento personalizado.
- As pastas de trabalho no recurso Application Insights são pastas de trabalho "herdadas" e não oferecem suporte ao armazenamento personalizado. As pastas de trabalho mais recentes no recurso Application Insights é o "... Mais "seleção. As pastas de trabalho herdadas não têm opções de assinatura ao salvar.

   ![Captura de tela mostrando pasta de trabalho herdada](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como criar uma visualização de [mapa](workbooks-map-visualizations.md) em pastas de trabalho.
- Saiba como usar [grupos em pastas de trabalho](../visualize/workbooks-groups.md).