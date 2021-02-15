---
title: Tutorial – Criar e gerenciar dados exportados do Gerenciamento de Custos do Azure
description: Este artigo mostra como você pode criar e gerenciar dados exportados do Gerenciamento de Custos do Azure para que você possa usá-los em sistemas externos.
author: bandersmsft
ms.author: banders
ms.date: 12/7/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: e3c1fa071cd23b871f754e89d6f17eb2cc44b394
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400345"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerenciar dados exportados

Se leu o tutorial de análise de custo, você está familiarizado com o download manual dos dados do Gerenciamento de Custos. No entanto, você pode criar uma tarefa recorrente que exporta automaticamente seus dados de Gerenciamento de Custos para o Armazenamento do Azure com uma frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contém todas as informações que são coletadas pelo Gerenciamento de Custos. Em seguida, você pode usar os dados exportados no Armazenamento do Azure com sistemas externos e combiná-los com seus próprios dados personalizados. Você também pode usar os dados exportados em um sistema externo, tal como um painel ou outro sistema financeiro.

Assista ao vídeo [Como agendar exportações para o armazenamento com o Gerenciamento de Custos do Azure](https://www.youtube.com/watch?v=rWa_xI1aRzo) sobre como criar uma exportação agendada dos seus dados de custo do Azure no Armazenamento do Azure. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Os exemplos neste tutorial orientam você ao exportar os dados do Gerenciamento de Custos e, em seguida, verificar se eles foram exportados com êxito.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo clientes do [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/) e do [Contrato de Cliente da Microsoft](get-started-partners.md). Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Há suporte para as seguintes permissões ou escopos do Azure por assinatura para exportações de dados por usuário e por grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir exportações agendadas de uma assinatura.
- Colaborador – pode criar, modificar ou excluir suas próprias exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outras pessoas.
- Leitor – pode agendar exportações às quais tem permissão.

Para contas de Armazenamento do Azure:
- São necessárias permissões de gravação para alterar a conta de armazenamento configurada, independentemente das permissões de exportação.
- Sua conta de Armazenamento do Azure precisa ser configurada para o Armazenamento de Blobs ou de Arquivos.

Se você tiver uma nova assinatura, não poderá usar imediatamente os recursos do Gerenciamento de Custos. Poderá levar até 48 horas para você poder usar todos os recursos do Gerenciamento de Custos.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar ou exibir uma exportação de dados ou para agendar uma exportação, abra o escopo desejado no portal do Azure e selecione **Análise de custo** no menu. Por exemplo, navegue até **Inscrições**, selecione uma assinatura na lista e, em seguida, selecione **Análise de custo** no menu. Na parte superior da página Análise de custo, selecione **Configurações** e **Exportações**.

> [!NOTE]
> - Além de assinaturas, você pode criar exportações em grupos de recursos, grupos de gerenciamento, departamentos e registros. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).
>- Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta do Armazenamento do Azure que esteja vinculada à conta de armazenamento do parceiro. No entanto, você precisa ter uma assinatura ativa em seu locatário do CSP.

1. Selecione **Adicionar** e digite um nome para a exportação.
1. Para a **Métrica**, faça uma seleção:
    - **Custo real (Uso e Compras)** : selecione essa opção para exportar o uso e as compras padrão
    - **Custo amortizado (Uso e Compras)** : selecione essa opção para exportar os custos amortizados de compras como as reservas do Azure
1. Em **Tipo de exportação**, faça uma seleção:
    - **Exportação diária de custos do mês atual**: fornece um novo arquivo de exportação diariamente para seus custos do mês atual. Os últimos dados são agregados com base nas exportações diárias anteriores.
    - **Exportação semanal de custo dos últimos sete dias**: cria uma exportação semanal dos custos dos últimos sete dias a partir da data de início selecionada da exportação.
    - **Exportação mensal de custos do mês passado**: fornece uma exportação dos custos do mês passado em comparação com o mês atual em que a exportação foi criada. Mais adiante, o agendamento executa uma exportação no quinto dia de cada mês, com os custos dos meses anteriores.
    - **Exportação avulsa**: permite que você escolha um intervalo de datas para que os dados históricos sejam exportados para o Armazenamento de Blobs do Azure. Você pode exportar, no máximo, 90 dias de custos históricos a partir do dia escolhido. Essa exportação é executada imediatamente e fica disponível na sua conta de armazenamento em até duas horas.
        Dependendo do tipo de exportação, escolha uma data de início ou uma data **De** e **Para**.
1. Especifique a assinatura para a sua conta de armazenamento do Azure e selecione um grupo de recursos ou crie um.
1. Selecione o nome da conta de armazenamento ou crie uma.
1. Selecione a localização (região do Azure).
1. Especifique o contêiner de armazenamento e o caminho do diretório para o qual deseja enviar o arquivo de exportação.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Exemplo de nova exportação" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Examine os detalhes da exportação e selecione **Criar**.

A nova exportação será exibida na lista de exportações. Por padrão, as novas exportações estão habilitadas. Se quiser desabilitar ou excluir uma exportação agendada, selecione qualquer item na lista e, em seguida, selecione **Desabilitar** ou **Excluir**.

Inicialmente, podem ser necessárias de 12 a 24 horas antes que a exportação seja executada. No entanto, pode levar até mais tempo para que os dados sejam mostrados nos arquivos exportados.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Comece preparando seu ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Depois de se conectar, para ver as exportações atuais, use o comando [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list):

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Além das assinaturas, você pode criar exportações para grupos de recursos e grupos de gerenciamento. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).
   >* Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta do Armazenamento do Azure que esteja vinculada à conta de armazenamento do parceiro. No entanto, você precisa ter uma assinatura ativa em seu locatário do CSP.

1. Crie um grupo de recursos ou use um existente. Para criar um grupo de recursos, use o comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Crie uma conta de armazenamento para receber as exportações ou use uma conta de armazenamento existente. Para criar uma conta de armazenamento, use o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Execute o comando [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create) para criar a exportação:

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   Para o parâmetro **--type**, escolha `ActualCost`, `AmortizedCost` ou `Usage`.

   Este exemplo usa o `MonthToDate`. A exportação cria um arquivo de exportação diariamente para os custos do mês atual. Os últimos dados são agregados das exportações diárias anteriores neste mês.

1. Para ver os detalhes da operação de exportação, use o comando [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show):

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Atualize uma exportação usando o comando [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update):

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Este exemplo altera o diretório de saída.

>[!NOTE]
>Inicialmente, podem ser necessárias de 12 a 24 horas antes que a exportação seja executada. No entanto, pode levar mais tempo para que os dados sejam mostrados nos arquivos exportados.

Exclua uma exportação usando o comando [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete):

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Comece preparando seu ambiente para o Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Enquanto o módulo **Az.CostManagement** do PowerShell estiver em versão prévia, você precisará instalá-lo separadamente usando o cmdlet `Install-Module`. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. Depois de se conectar, para ver as exportações atuais, use o cmdlet [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport):

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Além das assinaturas, você pode criar exportações para grupos de recursos e grupos de gerenciamento. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).
   >* Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta do Armazenamento do Azure que esteja vinculada à conta de armazenamento do parceiro. No entanto, você precisa ter uma assinatura ativa em seu locatário do CSP.

1. Crie um grupo de recursos ou use um existente. Para criar um grupo de recursos, use o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Crie uma conta de armazenamento para receber as exportações ou use uma conta de armazenamento existente. Para criar uma conta de armazenamento, use o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount):

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Execute o cmdlet [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) para criar a exportação:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   No parâmetro **DefinitionType**, escolha `ActualCost`, `AmortizedCost` ou `Usage`.

   Este exemplo usa o `MonthToDate`. A exportação cria um arquivo de exportação diariamente para os custos do mês atual. Os últimos dados são agregados das exportações diárias anteriores neste mês.

1. Para ver os detalhes da operação de exportação, use o cmdlet `Get-AzCostManagementExport`:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. Atualize uma exportação usando o cmdlet [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport):

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   Este exemplo altera o diretório de saída.

>[!NOTE]
>Inicialmente, podem ser necessárias de 12 a 24 horas antes que a exportação seja executada. No entanto, pode levar mais tempo para que os dados sejam mostrados nos arquivos exportados.

Exclua uma exportação usando o cmdlet [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport):

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Exportar agenda

As exportações agendadas são afetadas pela hora e pelo dia da semana de quando a exportação foi inicialmente criada. Quando você cria uma exportação agendada, a exportação é executada na mesma frequência para cada ocorrência seguinte de exportação. Por exemplo, para um conjunto de exportação de custos do mês atual definido com uma frequência diária, a exportação é executada diariamente. Da mesma forma, para uma exportação semanal, a exportação é executada todas as semanas no mesmo dia em que foi agendada. O tempo de entrega exato da exportação não é garantido, e os dados exportados ficam disponíveis em até quatro horas após a hora da execução.

Cada exportação cria um arquivo e, portanto, as exportações mais antigas não são substituídas.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Criar uma exportação para várias assinaturas

Caso você tenha um Contrato Enterprise, use um grupo de gerenciamento para agregar as informações de custo da assinatura em um só contêiner. Em seguida, você poderá exportar os dados de gerenciamento de custos do grupo de gerenciamento.

Não há suporte para exportações de grupos de gerenciamento de outros tipos de assinaturas.

1. Se você ainda não criou um grupo de gerenciamento, crie um e atribua assinaturas a ele.
1. Na análise de custo, defina o escopo para o grupo de gerenciamento e escolha **Selecionar este grupo de gerenciamento**.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Exemplo mostrando a opção Selecionar este grupo de gerenciamento" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Crie uma exportação no escopo para obter os dados de gerenciamento de custos para as assinaturas do grupo de gerenciamento.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Exemplo mostrando a opção Criar exportação com um escopo de grupo de gerenciamento":::

## <a name="verify-that-data-is-collected"></a>Verificar se os dados foram coletados

Verifique com facilidade se os dados do Gerenciamento de Custos estão sendo coletados e veja o arquivo CSV exportado usando o Gerenciador de Armazenamento do Azure.

Na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione Abrir no Explorador. Se vir uma caixa de confirmação, selecione **Sim** para abrir o arquivo no Gerenciador de Armazenamento do Azure.

![Página de conta de armazenamento mostrando informações de exemplo e link para abrir no Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

No Gerenciador de Armazenamento, navegue até o contêiner que você deseja abrir e selecione a pasta que corresponde ao mês atual. É mostrada uma lista de arquivos CSV. Selecione um deles e selecione **Abrir**.

![Informações de exemplo mostradas no Gerenciador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O arquivo é aberto com o programa ou aplicativo que está configurado para abrir as extensões de arquivo CSV. Aqui está um exemplo no Excel.

![Exemplo de dados CSV exportados mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Baixar um arquivo de dados CSV exportado

Baixe também o arquivo CSV exportado no portal do Azure. As etapas a seguir explicam como encontrá-lo na análise de custo.

1. Na análise de custo, selecione **Configurações** e **Exportações**.
1. Na lista de exportações, escolha a conta de armazenamento para uma exportação.
1. Na sua conta de armazenamento, selecione **Contêineres**.
1. Na lista de contêineres, escolha o contêiner.
1. Navegue pelos diretórios e pelos blobs de armazenamento até a data desejada.
1. Selecione o arquivo CSV e escolha **Baixar**.

[![Exemplo de download de exportação](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Exibir o histórico de execuções da exportação

Veja o histórico de execuções da exportação agendada selecionando uma exportação individual na página de lista de exportações. A página de lista de exportações também fornece acesso rápido para ver o runtime das exportações anteriores e a próxima vez em que a exportação será executada. Veja um exemplo que mostra o histórico de execuções.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="A captura de tela mostra o painel Exportações.":::

Selecione uma exportação para ver o histórico de execuções.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="A captura de tela mostra o histórico de execuções de uma exportação.":::

## <a name="access-exported-data-from-other-systems"></a>Acessar dados exportados de outros sistemas

Uma das finalidades de exportar os dados do Gerenciamento de Custos é acessar os dados de sistemas externos. Você pode usar um sistema de painéis ou outro sistema financeiro. Tais sistemas variam amplamente, portanto, mostrar um exemplo não seria conveniente.  No entanto, você pode obter uma introdução sobre como acessar seus dados nos aplicativos em [Introdução ao Armazenamento do Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

Avance para o próximo tutorial para otimizar e melhorar a eficiência, identificando recursos ociosos e subutilizados.

> [!div class="nextstepaction"]
> [Examinar recomendações de otimização e agir com base nelas](tutorial-acm-opt-recommendations.md)
