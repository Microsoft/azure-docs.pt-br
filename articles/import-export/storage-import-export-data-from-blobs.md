---
title: Usando a importação/exportação do Azure para exportar dados de Blobs do Azure | Microsoft Docs
description: Saiba como criar trabalhos de exportação no Portal do Azure para transferir dados para Blobs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 772be332af1476975d91eb270bec24d6d241a616
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706203"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Usar o serviço de Importação/ Exportação do Azure para exportar dados do Armazenamento de Blobs do Azure

Este artigo fornece instruções passo a passo sobre como usar o serviço de Importação/Exportação do Azure para exportar com segurança grandes quantidades de dados do Armazenamento de Blobs do Azure. O serviço requer que você envie unidades vazias para o datacenter do Azure. O serviço exporta os dados de sua conta de armazenamento para as unidades e, em seguida, envia as unidades de volta.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de exportação para transferir dados para fora do Armazenamento de Blobs do Azure, examine-o com cuidado e complete a seguinte lista de pré-requisitos para esse serviço.
Você deve:

- Ter uma assinatura ativa do Azure que pode ser usada para o serviço de Importação/Exportação.
- Ter pelo menos uma conta de Armazenamento do Microsoft Azure. Consulte a lista de [Contas de armazenamento e tipos de armazenamento com suporte para o serviço de Importação/Exportação](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](../storage/common/storage-account-create.md).
- Ter o número adequado de discos de [Tipos com suporte](storage-import-export-requirements.md#supported-disks).
- Ter uma conta FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com` .
  - A conta deve ser válida, deve ter saldo e ter recursos de devolução.
  - Gerar um número de controle para o trabalho de exportação.
  - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de controle.
  - Se você não tiver uma conta de transportadora, vá para:
    - [Criar uma conta do FedEx](https://www.fedex.com/en-us/create-account.html)ou
    - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Etapa 1: Criar um trabalho de exportação

### <a name="portal"></a>[Portal](#tab/azure-portal)

Execute as etapas a seguir para criar um trabalho de exportação no portal do Azure.

1. Faça logon em <https://portal.azure.com/>.
2. Vá para **Todos os serviços > Armazenamento > Trabalhos de importação/exportação**.

    ![Vá para Trabalhos de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Clique em **criar trabalho de importação/exportação**.

    ![Clique em Trabalho de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Em **Noções básicas**:

    - Selecione **Exportar do Azure**.
    - Digite um nome descritivo para o trabalho de exportação. Use o nome escolhido para acompanhar o andamento dos trabalhos.
        - O nome pode conter apenas letras minúsculas, números, hifens e sublinhados.
        - O nome deve começar com uma letra e não pode conter espaços.
    - Selecione uma assinatura.
    - Insira ou selecione um grupo de recursos.

        ![Noções básicas](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. Em **Detalhes do trabalho**:

    - Selecione a conta de armazenamento onde estão os dados a serem exportados. Use uma conta de armazenamento perto de onde você está localizado.
    - O local final da corrida é preenchido automaticamente com base na região da conta de armazenamento selecionada.
    - Especifique quais dados de blob deseja exportar da sua conta de armazenamento para a(s) unidade(s) em branco.
    - Escolha **Exportar todos** os dados blob na conta de armazenamento.

         ![Exportar tudo](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Você pode especificar quais contêineres e blobs exportar.
        - **Para especificar um blob para exportar**: Use o seletor **Igual a**. Especifique o caminho relativo do blob, começando com o nome do contêiner. Use *$root* para especificar o contêiner raiz.
        - **Para especificar todos os blobs que começam com um prefixo**: Use o seletor **Começa com**. Especifique o prefixo, começando com uma barra '/'. O prefixo pode ser do nome do contêiner, o nome do contêiner completo ou o nome do contêiner completo seguido do prefixo do nome do blob. Você deve fornecer os caminhos de blob no formato válido para evitar erros durante o processamento, como mostrado nesta captura de tela. Para obter mais informações, consulte [Exemplos de caminhos de blob válido](#examples-of-valid-blob-paths).

           ![Exporte os blobs e os contêineres selecionados](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Você pode exportar a partir do arquivo de lista de blob.

        ![Exportar de arquivo da lista de blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)

   > [!NOTE]
   > Se o blob a ser exportado estiver em uso durante a cópia dos dados, o serviço de Importação/Exportação do Azure tira um instantâneo do blob e copia o instantâneo.

6. Em **Informações sobre a remessa de devolução**:

    - Selecione a operadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações Azure Data Box em `adbops@microsoft.com`  com as informações sobre a transportadora que você planeja usar.
    - Insira um número válido de conta de operadora que você criou com essa operadora. A Microsoft usa essa conta para enviar as unidades de volta para você quando o trabalho de exportação estiver concluído.
    - Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

7. Em **Resumo**:

    - Revise os detalhes do trabalho.
    - Anote o nome do trabalho e forneça o endereço para entrega do datacenter do Azure para o envio de discos para Azure.

        > [!NOTE]
        > Sempre envie os discos para o datacenter anotado no portal do Azure. Se os discos forem enviados para o datacenter incorreto, o trabalho não será processado.

    - Clique em **OK** para concluir a criação do trabalho de exportação.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use as etapas a seguir para criar um trabalho de exportação no portal do Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Criar um trabalho

1. Use o comando [AZ Extension Add](/cli/azure/extension#az_extension_add) para adicionar a extensão [AZ Import-Export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Para obter uma lista dos locais dos quais você pode receber discos, use o comando [AZ Import-Export Location List](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Execute o seguinte comando [AZ Import-Export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) para criar um trabalho de exportação que usa sua conta de armazenamento existente:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

   Esse trabalho exporta todos os BLOBs em sua conta de armazenamento. Você pode especificar um blob para exportação substituindo esse valor para **--Export**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Esse valor de parâmetro exporta o blob chamado *logo.bmp* no contêiner raiz.

   Você também tem a opção de selecionar todos os BLOBs em um contêiner usando um prefixo. Substitua esse valor para **--Export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Para obter mais informações, consulte [Exemplos de caminhos de blob válido](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Se o blob a ser exportado estiver em uso durante a cópia dos dados, o serviço de Importação/Exportação do Azure tira um instantâneo do blob e copia o instantâneo.

1. Use o comando [AZ Import-Export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) para ver todos os trabalhos do grupo de recursos myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Para atualizar seu trabalho ou cancelar seu trabalho, execute o comando [AZ Import-Export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Use as etapas a seguir para criar um trabalho de exportação no Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Enquanto o módulo **AZ. ImportExport** do PowerShell está em versão prévia, você deve instalá-lo separadamente usando o `Install-Module` cmdlet. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Criar um trabalho

1. Para obter uma lista dos locais dos quais você pode receber discos, use o cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) :

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Execute o seguinte exemplo de [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) para criar um trabalho de exportação que usa sua conta de armazenamento existente:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

   Esse trabalho exporta todos os BLOBs em sua conta de armazenamento. Você pode especificar um blob para exportação substituindo esse valor para **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Esse valor de parâmetro exporta o blob chamado *logo.bmp* no contêiner raiz.

   Você também tem a opção de selecionar todos os BLOBs em um contêiner usando um prefixo. Substitua esse valor por **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Para obter mais informações, consulte [Exemplos de caminhos de blob válido](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Se o blob a ser exportado estiver em uso durante a cópia dos dados, o serviço de Importação/Exportação do Azure tira um instantâneo do blob e copia o instantâneo.

1. Use o cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) para ver todos os trabalhos do grupo de recursos myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Para atualizar seu trabalho ou cancelar seu trabalho, execute o cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Etapa 2: Enviar as unidades

Se você não souber o número de unidades que você precisa, vá para [Verificar o número de unidades](#check-the-number-of-drives). Se você souber o número de unidades, vá para enviar as unidades.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Etapa 3: Atualizar o trabalho com informações de controle

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Etapa 4: Receber os discos

Quando o painel informa que o trabalho foi concluído, os discos são enviados a você e o número de controle para a remessa está disponível no portal.

1. Depois de receber as unidades com dados exportados, você precisa obter as chaves do BitLocker para desbloquear as unidades. Vá para o trabalho de exportação no portal do Azure. Clique na guia **Importação/Exportação**.
2. Selecione e clique no trabalho de exportação da lista. Vá para **criptografia** e copie as chaves.

   ![Exibir chaves do BitLocker para um trabalho de exportação](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Use as chaves do BitLocker para desbloquear os discos.

A exportação foi concluída.

## <a name="step-5-unlock-the-disks"></a>Etapa 5: desbloquear os discos

Use o seguinte comando para desbloquear a unidade:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Aqui está um exemplo da entrada de exemplo.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Neste momento, você pode excluir o trabalho ou deixá-lo. Os trabalhos são excluídos automaticamente após 90 dias.

## <a name="check-the-number-of-drives"></a>Verificar o número de unidades

Essa etapa *opcional* ajuda você a determinar o número de unidades necessárias para o trabalho de exportação. Execute essa etapa em um sistema Windows executando uma [versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).

1. [Baixe o WAImportExport versão 1](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows.
2. Descompacte para a pasta padrão `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
3. Abra um PowerShell ou janela de linha de comando com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o comando a seguir:

   `cd C:\WaImportExportV1`

4. Para verificar o número de discos necessários para os blobs selecionados, execute o seguinte comando:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Os parâmetros estão descritos na tabela a seguir:

    |Parâmetro de linha de comando|Descrição|
    |--------------------------|-----------------|
    |**logdir**|Opcional. O diretório de log. Os arquivos de log detalhados são gravados nesse diretório. Se nenhum for especificado, o diretório atual será usado como o diretório de log.|
    |**SN**|Obrigatórios. O nome da conta de armazenamento do trabalho de exportação.|
    |**/SK**|Necessário somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento do trabalho de exportação.|
    |**/csas:**|Necessário somente se uma chave de conta de armazenamento não for especificada. A SAS do contêiner para listar os blobs a serem exportados no trabalho de exportação.|
    |**/ExportBlobListFile:**|Obrigatórios. Caminho até o arquivo XML que contém a lista de caminhos de blob ou prefixos de caminhos de blob para os blobs a serem exportados. O formato de arquivo usado no elemento `BlobListBlobPath` da operação [Put Job](/rest/api/storageimportexport/jobs) da API REST do serviço de Importação/Exportação.|
    |**/DriveSize:**|Obrigatórios. O tamanho das unidades a ser usado para um trabalho de exportação, *por exemplo*, 500 GB, 1,5 TB.|

    Consulte um [exemplo do comando PreviewExport](#example-of-previewexport-command).

5. Verifique se você pode ler/gravar nas unidades que serão enviadas para o trabalho de exportação.

### <a name="example-of-previewexport-command"></a>Exemplo de comando PreviewExport

O seguinte exemplo demonstra o comando `PreviewExport`:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

O arquivo de lista de blobs de exportação pode conter nomes e prefixos de blob, conforme mostrado aqui:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

A Ferramenta de Importação/Exportação do Azure lista todos os blobs a serem exportados e calcula como empacotá-los em unidades do tamanho especificado, levando em consideração qualquer sobrecarga necessária. Em seguida, estima o número de unidades necessárias para manter os blobs e as informações de uso da unidade.

Este é um exemplo da saída, com a omissão dos logs informativos:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de blob válidos

A tabela a seguir mostra exemplos de caminhos de blob válidos:

   | Seletor | Caminho do Blob | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todos os blobs na conta de armazenamento |
   | Começa com |/$root/ |Exporta todos os blobs no contêiner raiz |
   | Começa com |/book |Exporta todos os blobs em qualquer contêiner que comece com o prefixo **book** |
   | Começa com |/music/ |Exporta todos os blobs no contêiner **music** |
   | Começa com |/music/love |Exporta todos os blobs no contêiner **music** que comecem com o prefixo **love** |
   | Igual a |$root/logo.bmp |Exporta o blob **logo.bmp** no contêiner raiz |
   | Igual a |videos/story.mp4 |Exporta o blob **story.mp4** no contêiner **videos** |

## <a name="next-steps"></a>Próximas etapas

- [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
- [Verificar os requisitos de Importação/Exportação](storage-import-export-requirements.md)
