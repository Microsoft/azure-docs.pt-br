---
title: Usar Importação/Exportação do Microsoft Azure para transferir dados para Blobs do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação e exportação no portal do Azure para transferir dados de e para Blobs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 77a1c02c1ec59778521104e57f3bf3de8e52fa44
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177348"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usar o serviço de importação/exportação do Microsoft Azure para importar dados do Armazenamento de Blobs

Este artigo fornece instruções passo a passo sobre como usar o serviço de Importação/Exportação do Microsoft Azure para importar com segurança grandes quantidades de dados do Armazenamento de Blobs. Para importar dados para Blobs do Azure, o serviço exige o envio de unidades de disco criptografadas contendo dados para um datacenter do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados ao Armazenamento de Blobs, revise cuidadosamente e preencha a seguinte lista de pré-requisitos para esse serviço.
Você deve:

* Ter uma assinatura ativa do Azure que pode ser usada para o serviço de Importação/Exportação.
* Ter pelo menos uma conta do Armazenamento do Azure com um contêiner de armazenamento. Consulte a lista de [Contas de armazenamento e tipos de armazenamento com suporte para o serviço de Importação/Exportação](storage-import-export-requirements.md).
  * Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](../storage/common/storage-account-create.md).
  * Para obter informações sobre o contêiner de armazenamento, acesse [Criar um contêiner de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Ter o número adequado de discos de [Tipos com suporte](storage-import-export-requirements.md#supported-disks).
* Ter um sistema Windows executando uma [Versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).
* Habilite o BitLocker no sistema Windows. Consulte [Como habilitar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Baixe o WAImportExport versão 1 mais recente](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows. A versão mais recente da ferramenta tem atualizações de segurança para permitir um protetor externo para a chave do BitLocker e o recurso modo de desbloqueio atualizado.

  * Descompacte para a pasta padrão `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
* Ter uma conta FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com` .
  * A conta deve ser válida, deve ter saldo e ter recursos de devolução.
  * Gerar um número de controle para o trabalho de exportação.
  * Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de controle.
  * Se você não tiver uma conta de transportadora, vá para:
    * [Criar uma conta do FedEx](https://www.fedex.com/en-us/create-account.html)ou
    * [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Etapa 1: preparar as unidades

Essa etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas como número de série da unidade, chave de criptografia e detalhes da conta de armazenamento.

Execute as etapas a seguir para preparar as unidades.

1. Conecte as unidades de disco ao sistema Windows via conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma letra da unidade ao volume. Não use pontos de montagem.
3. Habilite a criptografia BitLocker no volume NTFS. Se estiver usando um Windows Server System, use as instruções em [Como habilitar o BitLocker no Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Copie os dados para o volume criptografado. Use arrastar e soltar, Robocopy ou qualquer outra ferramenta de cópia. Um arquivo de diário (*. JRN*) é criado na mesma pasta em que você executa a ferramenta.

   Se a unidade estiver bloqueada e você precisar desbloquear a unidade, as etapas para desbloquear podem ser diferentes dependendo do caso de uso.

   * Se você tiver adicionado dados a uma unidade previamente criptografada (a ferramenta WAImportExport não foi usada para criptografia), use a chave do BitLocker (uma senha numérica que você especificar) no pop-up para desbloquear a unidade.

   * Se você tiver adicionado dados a uma unidade que foi criptografada pela ferramenta WAImportExport, use o seguinte comando para desbloquear a unidade:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Abra um PowerShell ou uma janela de linha de comando com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o comando a seguir:

    `cd C:\WaImportExportV1`
6. Para obter a chave do BitLocker da unidade, execute o comando a seguir:

    `manage-bde -protectors -get <DriveLetter>:`
7. Para preparar o disco, execute o comando a seguir. **Dependendo do tamanho dos dados, a preparação do disco pode levar várias horas para dias.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Um arquivo de diário é criado na mesma pasta em que você executou a ferramenta. Dois outros arquivos também são criados - um arquivo *.xml* (pasta onde você executa a ferramenta) e um arquivo *drive-manifest.xml* (pasta onde os dados residem).

    Os parâmetros utilizados são descritos na tabela a seguir:

    |Opção  |Descrição  |
    |---------|---------|
    |/j:     |O nome do arquivo de diário, com a extensão .jrn. Um arquivo de diário é gerado por unidade. É recomendável utilizar o número de série do disco como o nome do arquivo de diário.         |
    |/id:     |A ID da sessão. Use um número de sessão exclusivo para cada instância do comando.      |
    |/t:     |A letra da unidade do disco a ser enviado. Por exemplo, unidade `D`.         |
    |/bk:     |A chave do BitLocker para a unidade. O código de acesso da saída de `manage-bde -protectors -get D:`      |
    |/srcdir:     |A letra da unidade do disco a ser enviado seguida por `:\`. Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contêiner de destino no Armazenamento do Microsoft Azure.         |
    |/BlobType     |Esta opção especifica o tipo de BLOBs para os quais você deseja importar os dados. Para BLOBs de blocos, o tipo de blob é `BlockBlob` e para BLOBs de páginas, é `PageBlob` .         |
    |/skipwrite:     | Especifica que não há novos dados necessários para serem copiados e os dados existentes no disco devem ser preparados.          |
    |/enablecontentmd5:     |A opção quando habilitado, garante que o MD5 é computado e definido como `Content-md5` propriedade em cada blob. Use esta opção somente se desejar usar o `Content-md5` campo depois que os dados forem carregados no Azure. <br> Essa opção não afeta a verificação de integridade de dados (que ocorre por padrão). A configuração aumenta o tempo necessário para carregar dados na nuvem.          |
8. Repita a etapa anterior para cada disco que precisa ser enviado. Um arquivo de diário com o nome fornecido é criado para cada execução da linha de comando.

    > [!IMPORTANT]
    > * Juntamente com o arquivo de diário, um arquivo `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também é criado na mesma pasta em que a ferramenta reside. O arquivo. xml é usado no lugar do arquivo de diário ao criar um trabalho se o arquivo de diário for muito grande.
   > * O tamanho máximo do arquivo de diário que o portal permite é de 2 MB. Se o arquivo de diário exceder esse limite, um erro será retornado.

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

   1. Carregue os arquivos de diário que você criou durante a [Etapa 1: preparar as unidades](#step-1-prepare-the-drives) anterior. Se utilizou `waimportexport.exe version1`, envie um arquivo para cada unidade que você preparou. Se o tamanho do arquivo de diário exceder 2 MB, você poderá usar o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também criado com o arquivo de diário.
   1. Selecione a região do Azure de destino para o pedido.
   1. Selecione a conta de armazenamento para a importação.
      
      O local final da corrida é preenchido automaticamente com base na região da conta de armazenamento selecionada.
   1. Se você não quiser salvar um log detalhado, limpe o **log detalhado salvar na opção de contêiner de BLOB ' waimportexport '** .

   ![Criar trabalho de importação - Etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Selecione **Avançar: envio >** para continuar.

6. No **envio**:

   1. Selecione a operadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações Azure Data Box em `adbops@microsoft.com`  com as informações sobre a transportadora que você planeja usar.
   1. Insira um número válido de conta de operadora que você criou com essa operadora. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação. Caso não tenha um número de conta, crie uma conta da operadora [FedEx](https://www.fedex.com/us/oadr/) ou [DHL](https://www.dhl.com/).
   1.  Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/município e país/região.

       > [!TIP]
       > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

   ![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Selecione **Examinar + criar** para prosseguir.

7. No resumo do pedido:

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
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

## <a name="step-3-optional-configure-customer-managed-key"></a>Etapa 3 (opcional): configurar a chave gerenciada pelo cliente

Ignore esta etapa e vá para a próxima etapa se você quiser usar a chave gerenciada da Microsoft para proteger suas chaves do BitLocker para as unidades. Para configurar sua própria chave para proteger a chave do BitLocker, siga as instruções em [Configurar chaves gerenciadas pelo cliente com Azure Key Vault para importação/exportação do Azure no portal do Azure](storage-import-export-encryption-key-portal.md).

## <a name="step-4-ship-the-drives"></a>Etapa 4: enviar as unidades

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Etapa 5: atualizar o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Etapa 6: verificar o carregamento de dados no Azure

Acompanhe o trabalho até a conclusão. Quando o trabalho estiver concluído, verifique se os dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi realizado com êxito.

## <a name="next-steps"></a>Próximas etapas

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Verificar os requisitos de Importação/Exportação](storage-import-export-requirements.md)
