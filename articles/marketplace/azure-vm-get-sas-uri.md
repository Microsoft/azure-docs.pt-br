---
title: Gerar um URI de SAS para uma imagem de VM-Azure Marketplace
description: Gere um URI de SAS (assinatura de acesso compartilhado) para um VHD (discos rígidos virtuais) no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 976d1dd4044649c9ef5307431c744ff3c2068bca
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233558"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Como gerar um URI de SAS para uma imagem de VM

> [!NOTE]
> Você não precisa de um URI de SAS para publicar sua VM. Você pode simplesmente compartilhar uma imagem no parter Center. Consulte [criar uma máquina virtual usando uma base aprovada](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-approved-base) ou [criar uma máquina virtual usando suas próprias instruções de imagem](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-own-image) .

A geração de URIs SAS para seus VHDs tem estes requisitos:

- Eles só dão suporte a VHDs não gerenciados.
- Somente as permissões de lista e leitura são necessárias. Não forneça acesso de gravação ou exclusão.
- A duração de acesso (data de expiração) deve ser no mínimo três semanas a partir de quando o URI de SAS é criado.
- Para se proteger contra alterações da hora de UTC, defina a data de início para um dia antes da data atual. Por exemplo, se a data atual for 16 de junho de 2020, selecione 6/15/2020.

## <a name="extract-vhd-from-a-vm"></a>Extrair o VHD de uma VM

> [!NOTE]
> Você pode ignorar esta etapa se já tiver um VHD carregado em uma conta de armazenamento.

Para extrair o VHD de sua VM, você precisa tirar um instantâneo do disco da VM e extrair o VHD do instantâneo.

Comece tirando um instantâneo do disco da VM:

1. Entre no portal do Azure.
2. Começando no canto superior esquerdo, selecione criar um recurso, em seguida, pesquise e selecione instantâneo.
3. Na folha instantâneo, selecione criar.
4. Insira um Nome para o instantâneo.
5. Selecione um grupo de recursos existente ou insira o nome de um novo.
6. Para Disco de origem, selecione o disco gerenciado para instantâneo.
7. Escolha o Tipo de conta a ser usado para armazenar o instantâneo. Use HD Standard, a menos que você precise dele armazenado em um SSD de alto desempenho.
8. Selecione Criar.

### <a name="extract-the-vhd"></a>Extrair o VHD

Use o script a seguir para exportar o instantâneo para um VHD em sua conta de armazenamento.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos a seguir para gerar o URI de SAS para um instantâneo e copia o VHD subjacente para uma conta de armazenamento usando o URI de SAS. Cada comando da tabela é vinculado à documentação específica do comando.


|Comando  |Observações  |
|---------|---------|
| az disk grant-access    |     Gera o SAS somente leitura usado para copiar o arquivo VHD subjacente para uma conta de armazenamento ou o baixá-lo localmente    |
|  az storage blob copy start   |    Copia um blob de forma assíncrona de uma conta de armazenamento para outra. Use AZ Storage blob show para verificar o status do novo BLOB.     |
|

## <a name="generate-the-sas-address"></a>Gerar o endereço de SAS

Há duas ferramentas comuns usadas para criar um endereço SAS (URL):

1. **Gerenciador de armazenamento do Azure** – disponível no portal do Azure.
2. **CLI do Azure** – recomendado para sistemas operacionais não Windows e ambientes de integração automatizados ou contínuos.

### <a name="using-tool-1-azure-storage-explorer"></a>Usando a ferramenta 1: Gerenciador de Armazenamento do Azure

1. Vá para sua **conta de armazenamento**.
1. Abra **Gerenciador de armazenamento**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Janela da conta de armazenamento.":::

3. No **contêiner**, clique com o botão direito do mouse no arquivo VHD e selecione **obter assinatura de acesso de compartilhamento**.
4. Na caixa de diálogo **assinatura de acesso compartilhado** , preencha os seguintes campos:

    1. Hora de início – Data de início de permissão para acesso ao VHD. Forneça uma data que é um dia antes da data atual.
    2. Hora de expiração – Data de expiração de permissão para acesso ao VHD. Forneça uma data de pelo menos três semanas após a data atual.
    3. Permissões – Selecione as permissões de leitura e listagem.
    4. Nível de contêiner – Marque a caixa de seleção Gerar URI de assinatura de acesso compartilhado no nível de contêiner.

    ![Caixa de diálogo assinatura de acesso compartilhado.](media/vm/create-sas-uri-storage-explorer.png)

5. Para criar o URI de SAS associado a este VHD, selecione **Criar**.
6. Copie o URI e salve-o em um arquivo de texto em um local seguro. Esse URI de SAS é gerado para o acesso de nível de contêiner. Para torná-lo específico, edite o arquivo de texto para adicionar o nome do VHD.
7. Insira seu nome de VHD após a cadeia de caracteres vhds no URI de SAS (incluir uma barra /). O URI de SAS final deve ficar assim:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Repita essas etapas para cada VHD nos planos que você vai publicar.

### <a name="using-tool-2-azure-cli"></a>Usando a ferramenta 2: CLI do Azure

1. Baixe e instale o [Microsoft Azure CL](/cli/azure/install-azure-cli)I. As versões estão disponíveis Windows. macOS e várias distribuições do Linux.
2. Crie um arquivo PowerShell (extensão de arquivo .ps1), copie o código a seguir e salve-o localmente.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Edite o arquivo para usar os seguintes valores de parâmetro. Forneça datas no formato UTC DateTime, como 2020-04-01T00:00:00Z.

    - nome da conta – seu nome de conta de armazenamento do Azure.
    - Account-Key – sua chave de conta de armazenamento do Azure.
    - Data de início – data de início da permissão para acesso VHD. Forneça uma data um dia antes da data atual.
    - Data de expiração – data de expiração da permissão para acesso VHD. Forneça uma data de pelo menos três semanas após a data atual.

    Aqui está um exemplo de valores de parâmetro apropriados (no momento da redação deste artigo):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Salve as alterações.
2. Usando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma Cadeia de caracteres de conexão SAS para acesso do nível de contêiner:

    - Execute o script a partir do console. No Windows, clique com o botão direito do mouse no script e selecione **Executar como administrador**.
    - Execute o script em um editor de script do PowerShell, como [ISE do Windows PowerShell](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Esta tela mostra a criação de uma cadeia de conexão de SAS dentro neste editor:

    [![criação de uma cadeia de conexão SAS no editor do PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Copie a cadeia de caracteres de conexão de SAS e salve-o em um arquivo de texto em um local seguro. Edite essa cadeia de caracteres para adicionar as informações de localização do VHD para criar o URI de SAS final.
7. No portal do Azure, navegue até o armazenamento de Blobs que inclui o VHD associado ao novo URI.
8. Copie a URL do ponto de extremidade do serviço blob:

    ![Copiando a URL do ponto de extremidade do serviço BLOB.](media/vm/create-sas-uri-blob-endpoint.png)

9. Edite o arquivo de texto com a cadeia de caracteres de conexão SAS da etapa 6. Crie o URI de SAS completo usando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Verifique o URI SAS

Verifique o URI de SAS antes de publicá-lo no Partner Center para evitar problemas relacionados ao envio de postagem de URI SAS da solicitação. Esse processo é opcional, mas recomendado.

- O URI inclui o nome de arquivo da imagem VHD, incluindo a extensão do nome do arquivo `.vhd` .
- `Sp=rl` aparece próximo ao meio do URI. Essa cadeia de caracteres mostra que o acesso de leitura e lista está especificado.
- Quando `sr=c` aparece, isso significa que o acesso de nível de contêiner é especificado.
- Copie e cole o URI em um navegador para fazer um teste de download do blob (você pode cancelar a operação antes que o download seja concluído).

## <a name="next-steps"></a>Próximas etapas

- Se você encontrar problemas, consulte [mensagens de falha SAS da VM](azure-vm-sas-failure-messages.md).
- [Entrar no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Criar uma oferta de máquina virtual no Azure Marketplace](azure-vm-create.md)
