---
title: Mover Azure Analysis Services para uma região diferente | Microsoft Docs
description: Descreve como mover um recurso de Azure Analysis Services para uma região diferente.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497921"
---
# <a name="move-analysis-services-to-a-different-region"></a>Mover Analysis Services para uma região diferente

Este artigo descreve como mover um recurso do Analysis Services Server para uma região diferente do Azure. Você pode mover o servidor para outra região por vários motivos, por exemplo, para aproveitar uma região do Azure mais próxima aos usuários, para usar os planos de serviço com suporte somente em regiões específicas ou para atender aos requisitos internos de políticas e governança. 

Neste e nos artigos vinculados associados, você aprenderá a:

> [!div class="checklist"]
> 
> * Fazer backup de um banco de dados modelo de servidor de origem no [armazenamento de BLOBs](../storage/blobs/storage-blobs-introduction.md).
> * Exportar um modelo de [recurso](../azure-resource-manager/templates/overview.md)do servidor de origem.
> * Obtenha uma [assinatura de acesso compartilhado de armazenamento (SAS)](../storage/common/storage-sas-overview.md).
> * Modifique o modelo de recurso.
> * Implante o modelo para criar um novo servidor de destino.
> * Restaure um banco de dados modelo para o novo servidor de destino.
> * Verifique o novo servidor de destino e o banco de dados.
> * Exclua o servidor de origem.

Este artigo descreve como usar um modelo de recurso para migrar um único servidor de Analysis Services com uma **configuração básica** para uma região *e* um grupo de recursos diferentes na mesma assinatura. O uso de um modelo retém as propriedades de servidor configuradas, garantindo que o servidor de destino esteja configurado com as mesmas propriedades, exceto região e grupo de recursos, como o servidor de origem. Este artigo não descreve a movimentação de recursos associados que podem fazer parte do mesmo grupo de recursos, como fonte de dados, armazenamento e recursos de gateway. 

Antes de mover um servidor para uma região diferente, é recomendável que você crie um plano detalhado. Considere recursos adicionais, como gateways e armazenamento que também podem precisar ser movidos. Com qualquer plano, é importante concluir uma ou mais operações de movimentação de avaliação usando servidores de teste antes de mover um servidor de produção.

> [!IMPORTANT]
> Os aplicativos cliente e as cadeias de conexão se conectam a Analysis Services usando o nome completo do servidor, que é um URI que inclui a região em que o servidor está. Por exemplo, `asazure://westcentralus.asazure.windows.net/advworks01`. Ao mover um servidor para uma região diferente, você estará efetivamente criando um novo recurso de servidor em uma região diferente, que terá uma região diferente no URI do nome do servidor. Os aplicativos cliente e as cadeias de conexão usados em scripts devem se conectar ao novo servidor usando o novo URI do nome do servidor. O uso de um [alias de nome de servidor](analysis-services-server-alias.md) pode reduzir o número de locais que o URI de nome do servidor precisa ser alterado, mas deve ser implementado antes de uma movimentação de região.

> [!IMPORTANT]
> As regiões do Azure usam intervalos de endereços IP diferentes. Se você tiver exceções de firewall configuradas para a região em que seu servidor e/ou conta de armazenamento está, pode ser necessário configurar um intervalo de endereços IP diferente. Para saber mais, consulte [perguntas frequentes sobre Analysis Services conectividade de rede](analysis-services-network-faq.md).

> [!NOTE]
> Este artigo descreve como restaurar um backup de banco de dados para um servidor de destino de um contêiner de armazenamento na região do servidor de origem. Em alguns casos, a restauração de backups de uma região diferente pode ter um desempenho insatisfatório, especialmente para bancos de dados grandes. Para obter o melhor desempenho durante a restauração do banco de dados, migre ou crie um novo contêiner de armazenamento na região do servidor de destino. Copie os arquivos de backup. ABF do contêiner de armazenamento da região de origem para o contêiner de armazenamento da região de destino antes de restaurar o banco de dados para o servidor de destino. Embora fora do escopo deste artigo, em alguns casos, particularmente com bancos de dados muito grandes, o script de um banco de dados do seu servidor de origem, a recriação e o processamento no servidor de destino para o carregamento de bancos de dado podem ser mais econômicos do que o uso de backup/restauração.

> [!NOTE]
> Se estiver usando um gateway de dados local para se conectar a fontes de dados, você também deverá mover o recurso de gateway para a região do servidor de destino. Para saber mais, consulte [instalar e configurar um gateway de dados local](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Conta de armazenamento do Azure**: necessária para armazenar um arquivo de backup. ABF.
- **SQL Server Management Studio (SSMS)**: necessário para fazer backup e restaurar bancos de dados de modelo.
- **Azure PowerShell**. Necessário somente se você optar por concluir essa tarefa usando o PowerShell.

## <a name="prepare"></a>Preparar

### <a name="backup-model-databases"></a>Bancos de dados de modelo de backup

Se **as configurações de armazenamento** ainda não estiverem configuradas para o servidor de origem, siga as etapas em [definir configurações de armazenamento](analysis-services-backup.md#configure-storage-settings).

Quando as configurações de armazenamento estiverem definidas, siga as etapas em [backup](analysis-services-backup.md#backup) para criar um banco de dados modelo. ABF backup em seu contêiner de armazenamento. Posteriormente, você restaura o backup. ABF para o novo servidor de destino.


### <a name="export-template"></a>Exportar modelo

O modelo contém as propriedades de configuração do servidor de origem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para exportar um modelo com o portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione o servidor de Analysis Services.

3. Selecione **configurações** de >  >  **modelo de exportação**.

4. Escolha **baixar** na folha **Exportar modelo** .

5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta.

   O arquivo zip contém os arquivos. JSON que compõem o modelo e os parâmetros necessários para implantar um novo servidor.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para exportar um modelo usando o PowerShell:

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura do recurso de servidor que você deseja mover.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporte o modelo do seu servidor de origem. Esses comandos salvam um modelo JSON com o nome de arquivo ResourceGroupName as no diretório atual.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Obter assinatura de acesso compartilhado de armazenamento (SAS)

Ao implantar um servidor de destino de um modelo, um token SAS de delegação de usuário (como um URI) é necessário para especificar o contêiner de armazenamento que contém o backup do banco de dados.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter uma assinatura de acesso compartilhado usando o portal:

1. No portal, selecione a conta de armazenamento usada para fazer backup do banco de dados do servidor.

2. Selecione **Gerenciador de armazenamento** e, em seguida, EXPANDa **contêineres de blob**. 

3. Clique com o botão direito do mouse no contêiner de armazenamento e selecione **obter assinatura de acesso compartilhado**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Obter SAS":::

4. Em **assinatura de acesso compartilhado**, selecione **criar**. Por padrão, a SAS expirará em 24 horas.

5. Copie e salve o **URI**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter uma assinatura de acesso compartilhado usando o PowerShell, siga as etapas em [criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Modificar o modelo

Use um editor de texto para modificar o template.jsno arquivo exportado, alterando as propriedades da região e do contêiner de BLOB. 

Para modificar o modelo:

1. Em um editor de texto, na propriedade **local** , especifique a nova região de destino. Na propriedade **backupBlobContainerUri** , Cole o URI do contêiner de armazenamento com a chave SAS. 

    O exemplo a seguir define a região de destino para o servidor advworks1 `South Central US` e especifica o URI do contêiner de armazenamento com assinatura de acesso compartilhado: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Salve o modelo.

#### <a name="regions"></a>Regiões

Para obter regiões do Azure, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). Para obter regiões usando o PowerShell, execute o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Mover

Para implantar um novo recurso de servidor em uma região diferente, você usará o **template.jsno** arquivo exportado e modificado nas seções anteriores.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal, selecione **criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **Enter**.

3. Selecione **implantação de modelo**.

4. Selecione **Criar**.

5. Selecione **Criar seu próprio modelo no editor**.

6. Selecione **carregar arquivo** e siga as instruções para carregar o **template.jsno** arquivo que você exportou e modificou.

7. Verifique se o editor de modelo mostra as propriedades corretas para o novo servidor de destino.

8. Selecione **Salvar**.

9. Insira ou selecione os valores de propriedade:

    - **Assinatura**: selecione a assinatura do Azure.
    
    - **Grupo de recursos**: selecione **criar novo** e insira um nome de grupo de recursos. Você pode selecionar um grupo de recursos existente desde que ele ainda não contenha um servidor de Analysis Services com o mesmo nome.
    
    - **Local**: selecione a mesma região que você especificou no modelo.

10. Selecione **revisar e criar**.

11. Examine os termos e noções básicas e, em seguida, selecione **criar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use estes comandos para implantar seu modelo:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Obter URI do servidor de destino

Para se conectar ao novo servidor de destino do SSMS para restaurar o banco de dados modelo, você precisa obter o novo URI do servidor de destino.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter o URI do servidor no Portal:

1. No portal, vá para o novo recurso do servidor de destino.

2. Na página **visão geral** , copie o URI do **nome do servidor** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter o URI do servidor usando o PowerShell, use os seguintes comandos:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Copie **ServerFullName** da saída.

---

### <a name="restore-model-database"></a>Restaurar banco de dados modelo

Siga as etapas descritas em [restaurar](analysis-services-backup.md#restore) para restaurar o banco de dados modelo. ABF backup para o novo servidor de destino.

Opcional: depois de restaurar o banco de dados modelo, processe o modelo e as tabelas para atualizar os dados das fontes de dados. Para processar o modelo e a tabela usando o SSMS:

1. No SSMS, clique com o botão direito do mouse no banco de dados modelo > **processar banco de dados**.

2. Expanda **tabelas**, clique com o botão direito do mouse em uma tabela. Em **processar tabela (s)**, selecione todas as tabelas e, em seguida, selecione **OK**.

## <a name="verify"></a>Verificar

1. No portal, vá para o novo servidor de destino.

2. Na página Visão geral, em **modelos no Analysis Services Server**, verifique se os modelos restaurados são exibidos.

3. Use um aplicativo cliente como o Power BI ou o Excel para se conectar ao modelo no novo servidor. Verifique se os objetos de modelo, como tabelas, medidas, hierarquias são exibidos. 

4. Execute qualquer script de automação. Verifique se eles foram executados com êxito.

Opcional: o [Alm Toolkit](http://alm-toolkit.com/) é uma *ferramenta de software* livre para comparar e gerenciar conjuntos de dados Power bi e Analysis Services bancos de dados *de* modelo de tabela. Use o kit de ferramentas para se conectar aos bancos de dados do servidor de origem e de destino e comparar. Se a migração do banco de dados for bem-sucedida, os objetos de modelo terão a mesma definição. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de verificar se os aplicativos cliente podem se conectar ao novo servidor e todos os scripts de automação estiverem sendo executados corretamente, exclua o servidor de origem. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para excluir o servidor de origem do portal:

Na página **visão geral** do servidor de origem, selecione **excluir**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para excluir o servidor de origem usando o PowerShell, use o comando Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Depois de concluir uma movimentação de região, é recomendável que o novo servidor de destino use um contêiner de armazenamento na mesma região para backups, em vez do contêiner de armazenamento na região do servidor de origem.