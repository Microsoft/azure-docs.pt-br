---
title: Usar Importação/Exportação do Microsoft Azure para transferir dados para Arquivos do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação no Portal do Azure para transferir dados para Arquivos do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: b62c3c4be4fdffd9f509b86d248cd028518ae89a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181934"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usar o serviço de importação/exportação do Microsoft Azure para importar dados para Arquivos do Azure

Este artigo fornece instruções passo a passo sobre como usar o serviço de Importação/Exportação do Microsoft Azure para importar com segurança grandes quantidades de dados de Arquivos do Azure. Para importar dados, o serviço exige o envio de unidades de disco suportadas contendo dados para um datacenter do Azure.

O serviço de Importação/Exportação dá suporte somente à importação de Arquivos do Azure para o Armazenamento do Microsoft Azure. Não há suporte para a exportação de Arquivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados ao Arquivos do Azure, revise cuidadosamente e preencha a seguinte lista de pré-requisitos. Você deve:

- Ter uma assinatura ativa do Azure para usar com o serviço de Importação/Exportação.
- Ter pelo menos uma conta de Armazenamento do Microsoft Azure. Consulte a lista de [Contas de armazenamento e tipos de armazenamento com suporte para o serviço de Importação/Exportação](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](../storage/common/storage-account-create.md).
- Ter o número adequado de discos de [Tipos com suporte](storage-import-export-requirements.md#supported-disks).
- Ter um sistema Windows executando uma [Versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).
- [Baixe o WAImportExport versão 2](https://aka.ms/waiev2) no sistema Windows. Descompacte para a pasta padrão `waimportexport`. Por exemplo, `C:\WaImportExport`.
- Ter uma conta FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com` .
    - A conta deve ser válida, deve ter saldo e ter recursos de devolução.
    - Gerar um número de controle para o trabalho de exportação.
    - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de controle.
    - Se você não tiver uma conta da operadora, vá para:
        - [Criar uma conta do FedEx](https://www.fedex.com/en-us/create-account.html)ou
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>Etapa 1: preparar as unidades

Essa etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas como número de série da unidade, chave de criptografia e detalhes da conta de armazenamento.

Execute as etapas a seguir para preparar as unidades.

1. Conecte as unidades de disco ao sistema Windows via conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma letra da unidade ao volume. Não use pontos de montagem.
3. Modifique o arquivo de *dataset.csv* na pasta raiz onde está a ferramenta. Dependendo de você querer importar um arquivo ou pasta ou ambos, adicione as entradas no arquivo *dataset.csv* semelhante aos exemplos a seguir.

   - **Para importar um arquivo**: no exemplo a seguir, os dados a serem copiados estão na unidade F:. O arquivo *MyFile1.txt* é copiado para a raiz de *MyAzureFileshare1*. Se o *MyAzureFileshare1* não existir, ele será criado na conta de armazenamento do Azure. A estrutura de pastas é mantida.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Para importar uma pasta**: todos os arquivos e pastas em *MyFolder2* são copiados para o compartilhamento de arquivos de forma recursiva. A estrutura de pastas é mantida.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Várias entradas podem ser feitas no mesmo arquivo correspondente a pastas ou arquivos que são importados.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Saiba mais sobre [como preparar o arquivo CSV de conjunto de dados](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Modifique o arquivo de *driveset.csv* na pasta raiz onde está a ferramenta. Adicione as entradas no arquivo *driveset.csv* semelhante aos exemplos a seguir. O arquivo driveset tem a lista de discos e as letras de unidade correspondentes para que a ferramenta possa escolher corretamente a lista de discos a serem preparados.

    Este exemplo presume que dois discos estão vinculados e volumes NTFS básicos G:\ e H:\ foram criados. H:\is não está criptografado enquanto g já está criptografado. A ferramenta formata e criptografa o disco que hospeda apenas H:\ (e não G:\).

   - **Para um disco que não está criptografado**: especifique *Criptografar* para habilitar a criptografia BitLocker no disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Para um disco que já está criptografado**: especifique *AlreadyEncrypted* e forneça a chave do BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Várias entradas podem ser feitas no mesmo arquivo correspondente a várias unidades. Saiba mais sobre [como preparar o arquivo CSV de driveset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Use a opção `PrepImport` para copiar e preparar dados para a unidade de disco. Para a primeira sessão de cópia copiar diretórios e/ou arquivos com uma nova sessão de cópia, execute o seguinte comando:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Um exemplo de importação é mostrado abaixo.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Um arquivo de diário com o nome fornecido com o parâmetro `/j:` é criado para cada execução da linha de comando. Cada unidade que você prepara tem um arquivo de diário que deve ser carregado quando você cria o trabalho de importação. Unidades sem arquivos de diário não são processadas.

    > [!IMPORTANT]
    > - Não modifique os dados em unidades de disco ou o arquivo de diário depois de concluir a preparação do disco.

Para exemplos adicionais, vá para [Exemplos de arquivos de diário](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Etapa 2: criar um trabalho de importação

### <a name="portal"></a>[Portal](#tab/azure-portal)

Execute as etapas a seguir para criar um trabalho de importação no portal do Azure.
1. Faça logon em https://portal.azure.com/.
2. Pesquise **trabalhos de importação/exportação**.

    ![Pesquisar em trabalhos de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecione **+ Novo**.

    ![Selecione novo para criar um novo ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Em **Noções básicas**:

   1. Selecione uma assinatura.
   1. Selecione um grupo de recursos ou selecione **criar novo** e crie um novo.
   1. Digite um nome descritivo para o trabalho de importação. Use o nome para acompanhar o andamento dos trabalhos.
       * O nome pode conter apenas letras minúsculas, números e hifens.
       * O nome deve começar com uma letra e não pode conter espaços.
   1. Selecione **Importar para o Azure**.

    ![Criar o trabalho de importação - Etapa 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   Selecione **Avançar: detalhes do trabalho >** para continuar.

5. Em **Detalhes do trabalho**:

   1. Carregue os arquivos de diário que você criou durante a [Etapa 1: preparar as unidades](#step-1-prepare-the-drives) anterior.
   1. Selecione a região do Azure de destino para o pedido.
   1. Selecione a conta de armazenamento para a importação.

      O local final da corrida é preenchido automaticamente com base na região da conta de armazenamento selecionada.

   1. Se você não quiser salvar um log detalhado, limpe o **log detalhado salvar na opção de contêiner de BLOB ' waimportexport '** .


   ![Criar trabalho de importação - Etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Selecione **Avançar: envio >** para continuar.

4. No **envio**:

    1. Selecione a operadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações de Azure Data Box `adbops@microsoft.com`  com as informações sobre a transportadora que você planeja usar.
    1. Insira um número válido de conta de operadora que você criou com essa operadora. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação.
    1. Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/município e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

    ![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Selecione **Examinar + criar** para prosseguir.

5. No resumo do pedido:

   1. Examine os **termos** e, em seguida, selecione "Eu reconheço que todas as informações fornecidas estão corretas e concorde com os termos e condições". Em seguida, a validação é executada.
   1. Revise as informações do trabalho fornecidas no resumo. Anote o nome do trabalho e o endereço de remessa do datacenter do Azure para enviar os discos de volta ao Azure. Essas informações serão utilizadas posteriormente na etiqueta de remessa.
   1. Selecione **Criar**.

        ![Criar o trabalho de importação - Etapa 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use as etapas a seguir para criar um trabalho de importação no CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Criar um trabalho

1. Use o comando [AZ Extension Add](/cli/azure/extension#az_extension_add) para adicionar a extensão [AZ Import-Export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Você pode usar um grupo de recursos existente ou criar um. Para criar um grupo de recursos, execute o comando [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Você pode usar uma conta de armazenamento existente ou criar uma. Para criar uma conta de armazenamento, execute o comando [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) :

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Para obter uma lista dos locais para os quais você pode enviar discos, use o comando [AZ Import-exportar local List](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Use o comando [AZ Import-exportar local show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) para obter locais para sua região:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Execute o seguinte comando [AZ Import-Export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) para criar um trabalho de importação:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.


1. Use o comando [AZ Import-Export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) para ver todos os trabalhos do grupo de recursos myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Para atualizar seu trabalho ou cancelar seu trabalho, execute o comando [AZ Import-Export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

Use as etapas a seguir para criar um trabalho de importação no Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Enquanto o módulo **AZ. ImportExport** do PowerShell está em versão prévia, você deve instalá-lo separadamente usando o `Install-Module` cmdlet. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Criar um trabalho

1. Você pode usar um grupo de recursos existente ou criar um. Para criar um grupo de recursos, execute o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Você pode usar uma conta de armazenamento existente ou criar uma. Para criar uma conta de armazenamento, execute o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) :

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Para obter uma lista dos locais para os quais você pode enviar discos, use o cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) :

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Use o `Get-AzImportExportLocation` cmdlet com o `Name` parâmetro para obter locais para sua região:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Execute o seguinte exemplo de [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) para criar um trabalho de importação:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
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
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

1. Use o cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) para ver todos os trabalhos do grupo de recursos myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Para atualizar seu trabalho ou cancelar seu trabalho, execute o cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Etapa 3: enviar as unidades para o datacenter do Azure

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Etapa 4: atualizar o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Etapa 5: verificar upload de dados para Azure

Acompanhe o trabalho até a conclusão. Quando o trabalho estiver concluído, verifique se os dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi bem-sucedido.

## <a name="samples-for-journal-files"></a>Exemplos de arquivos de diário

Para **adicionar mais unidades**, é possível criar um novo arquivo de driveset e executar o comando conforme exibido a seguir.

Para sessões de cópia subsequentes em unidades de disco diferentes das especificadas no arquivo *InitialDriveset. csv* , especifique um novo arquivo driveset *. csv* e forneça-o como um valor para o parâmetro `AdditionalDriveSet` . Use o nome **mesmo arquivo de diário** e forneça uma **nova ID de sessão**. O formato do arquivo CSV AdditionalDriveset é igual ao formato InitialDriveSet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Um exemplo de importação é mostrado abaixo.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Para adicionar mais dados ao mesmo driveset, use o comando PrepImport para sessões de cópia subsequentes para copiar arquivos/diretórios adicionais.

Para as sessões de cópia subsequentes para a mesma unidade de disco rígido especificada no arquivo *InitialDriveset.csv*, especifique o mesmo nome do **arquivo de diário** e forneça uma **nova ID de sessão**. Não é preciso fornecer a chave da conta de armazenamento.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Um exemplo de importação é mostrado abaixo.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Próximas etapas

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Verificar os requisitos de Importação/Exportação](storage-import-export-requirements.md)