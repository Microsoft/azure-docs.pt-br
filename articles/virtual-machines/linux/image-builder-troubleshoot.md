---
title: Solucionar problemas do serviço Construtor de imagens do Azure
description: Solucionar problemas e erros comuns ao usar o serviço do construtor de imagem de VM do Azure
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666212"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Solucionar problemas do serviço Construtor de imagens do Azure

Este artigo ajuda você a solucionar problemas e resolver problemas comuns que você pode encontrar ao usar o serviço do construtor de imagem do Azure.

Falhas de AIB podem ocorrer em duas áreas:
- Envio de modelo de imagem
- Compilação de imagem

## <a name="troubleshoot-image-template-submission-errors"></a>Solucionar erros de envio de modelo de imagem

Os erros de envio do modelo de imagem são retornados somente no envio. Não há um log de erros para erros de envio de modelo de imagem. Se houve um erro durante o envio, você pode retornar o erro verificando o status do modelo, examinando especificamente o `ProvisioningState` e o `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> Para o PowerShell, você precisará instalar os [módulos do PowerShell do Azure Image Builder](../windows/image-builder-powershell.md#prerequisites).

As seções a seguir incluem diretrizes de resolução de problemas para erros de envio de modelo de imagem comuns.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Não há suporte para atualização/atualização de modelos de imagem no momento

#### <a name="error"></a>Erro

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Causa

O modelo já existe.

#### <a name="solution"></a>Solução

Se você enviar um modelo de configuração de imagem e o envio falhar, um artefato de modelo com falha ainda existirá. Exclua o modelo com falha.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>A operação de recurso foi concluída com o estado de provisionamento de terminal ' Failed '

#### <a name="error"></a>Erro

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Causa

Na maioria dos casos, o erro de falha na implantação do recurso ocorre devido a falta de permissões.

#### <a name="solution"></a>Solução

Dependendo do seu cenário, o construtor de imagem do Azure pode precisar de permissões para:
- Imagem de origem ou grupo de recursos da Galeria de imagens compartilhadas
- Imagem de distribuição ou recurso da Galeria de imagens compartilhadas
- A conta de armazenamento, o contêiner ou o blob que o personalizador de arquivo está acessando. 

Para obter mais informações sobre como configurar permissões, consulte [configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure](image-builder-permissions-cli.md) ou [configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Erro ao obter imagem gerenciada

#### <a name="error"></a>Erro

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Causa

Permissões ausentes.

#### <a name="solution"></a>Solução

Dependendo do seu cenário, o construtor de imagem do Azure pode precisar de permissões para:
* Imagem de origem ou grupo de recursos da Galeria de imagens compartilhadas
* Imagem de distribuição ou recurso da Galeria de imagens compartilhadas
* A conta de armazenamento, o contêiner ou o blob que o personalizador de arquivo está acessando. 

Para obter mais informações sobre como configurar permissões, consulte [configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure](image-builder-permissions-cli.md) ou [configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>Falha na etapa de compilação da versão da imagem

#### <a name="error"></a>Erro
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Causa

O construtor de imagens do Azure não pode localizar a imagem de origem.

#### <a name="solution"></a>Solução

Verifique se a imagem de origem está correta e se existe no local do serviço do construtor de imagens do Azure.

### <a name="downloading-external-file-to-local-file"></a>Baixando o arquivo externo para o arquivo local

#### <a name="error"></a>Erro

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Causa

O nome ou o local do arquivo está incorreto ou o local não está acessível.

#### <a name="solution"></a>Solução

Verifique se o arquivo está acessível. Verifique se o nome e o local estão corretos.

## <a name="troubleshoot-build-failures"></a>Solucionar problemas de falhas de compilação

Para falhas de compilação de imagem, você pode obter o erro no `lastrunstatus` e, em seguida, examinar os detalhes em customization. log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Log de personalização

Quando a compilação da imagem está em execução, os logs são criados e armazenados em uma conta de armazenamento. O construtor de imagens do Azure cria a conta de armazenamento no grupo de recursos temporários quando você cria um artefato de modelo de imagem.

O nome da conta de armazenamento usa o seguinte padrão: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Por exemplo, *IT_aibmdi_helloImageTemplateLinux01*.

Você pode exibir a personalização. log na conta de armazenamento no grupo de recursos, selecionando  >  **BLOBs** da conta de armazenamento  >  `packerlogs` .  Em seguida, selecione **diretório > personalização. log**.


### <a name="understanding-the-customization-log"></a>Noções básicas sobre o log de personalização

O log é detalhado. Ele aborda a compilação da imagem, incluindo quaisquer problemas com a distribuição da imagem, como a replicação da Galeria de imagens compartilhadas. Esses erros são exibidos na mensagem de erro do status do modelo de imagem.

O customization. log inclui os seguintes estágios:

1. Implante a VM de compilação e as dependências usando modelos ARM no estágio de grupo de recursos de preparo IT_. Este estágio inclui várias postagens no provedor de recursos do construtor de imagens do Azure:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Status do estágio de implantações. Este estágio inclui o status de cada implantação de recurso:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Conecte-se ao estágio Build VM.

    Se o Windows, o serviço do construtor de imagem do Azure se conecta usando o WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Se o Linux, o serviço do construtor de imagens do Azure se conectará usando SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Executar o estágio de personalizações. Quando as personalizações são executadas, você pode identificá-las examinando o customization. log. Procure *(telemetria)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Estágio de desprovisionamento. O construtor de imagens do Azure adiciona um personalizador oculto. Essa etapa de desprovisionamento é responsável por preparar a VM para desprovisionamento. Ele executa o Windows Sysprep (usando c:\DeprovisioningScript.ps1) ou o desprovisionamento do Linux waagent (usando/tmp/DeprovisioningScript.sh). 

    Por exemplo: 
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Limpar estágio. Depois que a compilação for concluída, os recursos do Azure Image Builder serão excluídos.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Dicas para solução de problemas de personalização de script/embutido
- Testar o código antes de fornecê-lo ao construtor de imagens
- Certifique-se de que os firewalls e Azure Policy permitem a conectividade com recursos remotos.
- Comentários de saída para o console do, como `Write-Host` usar `echo` o ou o, permitirá que você pesquise o customization. log.

## <a name="troubleshoot-common-build-errors"></a>Solucionar erros comuns de compilação

### <a name="packer-build-command-failure"></a>Falha no comando de Build do packr

#### <a name="error"></a>Erro

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Causa

Falha na personalização.

#### <a name="solution"></a>Solução

Examine o log para localizar as falhas dos personalizadores. Procure *(telemetria)*. 

Por exemplo: 
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Tempo limite excedido

#### <a name="error"></a>Erro

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Causa

A compilação excedeu o tempo limite da compilação. Esse erro é visto no ' LastRunStatus '.

#### <a name="solution"></a>Solução

1. Examine o customization. log. Identifique o último personalizador a ser executado. Pesquise `(telemetry)` iniciando na parte inferior do log. 

2. Verifique as personalizações de script. As personalizações podem não estar suprimindo a interação do usuário para comandos, como `quiet` opções. Por exemplo, `apt-get install -y` resulta na execução do script aguardando a interação do usuário.

3. Se você estiver usando o `File` personalizador para baixar artefatos maiores que 20 MB, consulte a seção soluções alternativas.

4. Examine os erros e as dependências no script que podem fazer com que o script aguarde.

5. Se você espera que as personalizações precisem de mais tempo, aumente [buildTimeoutInMinutes](image-builder-json.md). O padrão é quatro horas.

6. Se você tiver ações com uso intensivo de recursos, como baixar gigabytes de arquivos, considere o tamanho da VM de compilação subjacente. O serviço usa uma VM Standard_D1_v2. A VM tem, 1 vCPU e 3,5 GB de memória. Se você estiver baixando 50 GB, isso provavelmente esgotará os recursos da VM e causará falhas de comunicação entre o serviço do construtor de imagem do Azure e a VM de compilação. Repita a compilação com uma VM de memória maior, definindo o [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Tempo de download de arquivo longo

#### <a name="error"></a>Erro
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Causa 

O personalizador de arquivo está baixando um arquivo grande.

#### <a name="solution"></a>Solução

O personalizador de arquivo é adequado apenas para downloads de arquivos pequenos com menos de 20 MB. Para downloads de arquivos maiores, use um script ou comando embutido. Por exemplo, no Linux, você pode usar `wget` ou `curl` . No Windows, você pode usar o `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Erro ao aguardar na Galeria de imagens compartilhadas

#### <a name="error"></a>Erro

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Causa

O Image Builder atingiu o tempo limite aguardando a imagem ser adicionada e replicada para a Galeria de imagens compartilhada (SIG). Se a imagem estiver sendo injetada no SIG, ela poderá ser considerada que a compilação da imagem foi bem-sucedida. No entanto, o processo geral falhou, pois o construtor de imagem estava esperando na Galeria de imagens compartilhadas para concluir a replicação. Embora a compilação tenha falhado, a replicação continua. Você pode obter as propriedades da versão da imagem verificando o *runOutput* de distribuição.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Solução

Aumente o **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Poucos eventos de informações de recursos do Windows

#### <a name="error"></a>Erro

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Causa

Esgotamento de recursos. Esse problema é normalmente visto com Windows Update em execução usando o tamanho de VM de compilação padrão D1_V2.

#### <a name="solution"></a>Solução

Aumente o tamanho da VM de compilação.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Compilações concluídas, mas nenhum artefato foi criado

#### <a name="error"></a>Erro

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Causa

Tempo limite causado pela espera dos recursos do Azure necessários para serem criados.

#### <a name="solution"></a>Solução

Execute novamente a compilação para tentar novamente.

### <a name="resource-not-found"></a>Recurso não encontrado

#### <a name="error"></a>Erro

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Causa

Permissões ausentes.

#### <a name="solution"></a>Solução

Verifique novamente se o construtor de imagem do Azure tem todas as permissões necessárias. 

Para obter mais informações sobre como configurar permissões, consulte [configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure](image-builder-permissions-cli.md) ou [configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Tempo de Sysprep

#### <a name="error"></a>Erro

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Causa

A causa pode ser um problema de tempo devido ao tamanho da VM D1_V2. Se as personalizações forem limitadas e executadas em menos de três segundos, os comandos do Sysprep serão executados pelo construtor de imagens do Azure para desprovisionar. Quando o construtor de imagem do Azure desprovisiona, o comando Sysprep verifica o *WindowsAzureGuestAgent*, que pode não estar totalmente instalado, causando o problema de tempo. 

#### <a name="solution"></a>Solução

Aumente o tamanho da VM. Ou então, você pode adicionar uma personalização de suspensão do PowerShell de 60 segundos para evitar o problema de tempo.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Cancelando o Construtor após o contexto de cancelamento do contexto

#### <a name="error"></a>Erro
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Causa
O serviço do construtor de imagem usa a porta 22 (Linux) ou 5986 (Windows) para se conectar à VM de compilação, isso ocorre quando o serviço está desconectado da VM de compilação durante uma compilação de imagem. Os motivos para a desconexão podem variar, mas habilitar ou configurar firewalls no script pode bloquear as portas acima.

#### <a name="solution"></a>Solução
Examine os scripts de alterações/habilitação do firewall ou alterações no SSH ou no WinRM e verifique se as alterações permitem a conectividade constante entre o serviço e a VM de compilação nas portas acima. Para obter mais informações sobre a rede do Image Builder, consulte os [requisitos](./image-builder-networking.md).

## <a name="devops-task"></a>Tarefa do DevOps 

### <a name="troubleshooting-the-task"></a>Solucionando problemas da tarefa
A tarefa só falhará se ocorrer um erro durante a personalização, quando isso acontecer, a tarefa relatará falha e deixará o grupo de recursos de preparo, com os logs, para que você possa identificar o problema. 

Para localizar o log, você precisa saber o nome do modelo, entrar no pipeline > compilação com falha > analisar a tarefa AIB DevOps, em seguida, verá o log e um nome de modelo:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Vá para o portal, procure o nome do modelo no grupo de recursos e procure o grupo de recursos com IT_ *.
Acesse a conta de armazenamento > BLOBs > contêineres > logs.

### <a name="troubleshooting-successful-builds"></a>Solução de problemas de builds bem-sucedidos
Talvez haja alguns casos em que você precise investigar compilações bem-sucedidas e desejar examinar o log. Conforme mencionado, se a compilação da imagem for bem-sucedida, o grupo de recursos de preparo que contém os logs será excluído como parte da limpeza. No entanto, o que você pode fazer é introduzir uma suspensão após o comando embutido e, em seguida, obter os logs à medida que a compilação é pausada. Para fazer isso, siga estas etapas:
 
1. Atualize o comando embutido e adicione: Write-Host/Echo "Sleep" – isso permitirá que você pesquise no log
2. Adicione uma suspensão para pelo menos 10mins, você pode usar o comando [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)ou `Sleep` Linux.
3. Use o método acima para identificar o local do log e, em seguida, continue baixando/verificando o log até que ele chegue ao estado de suspensão.


### <a name="operation-was-canceled"></a>A operação foi cancelada

#### <a name="error"></a>Erro

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Causa

Se a compilação não foi cancelada por um usuário, ela foi cancelada pelo agente do usuário DevOps do Azure. Provavelmente, o tempo limite de uma hora ocorreu devido a recursos de DevOps do Azure. Se você estiver usando um projeto e um agente privados, obterá 60 minutos de tempo de compilação. Se a compilação exceder o tempo limite, o DevOps cancelará a tarefa em execução.

Para obter mais informações sobre as limitações e recursos de DevOps do Azure, consulte [agentes hospedados pela Microsoft](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)
 
#### <a name="solution"></a>Solução

Você pode hospedar seus próprios agentes do DevOps ou procurar reduzir o tempo de sua compilação. Por exemplo, se você estiver distribuindo para a Galeria de imagens compartilhadas, replique para uma região. Se você quiser replicar de forma assíncrona. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Logon lento do Windows: ' aguarde o instalador dos módulos do Windows '

#### <a name="error"></a>Erro
Depois de criar uma imagem do Windows 10 com o Image Builder, crie uma VM a partir da imagem, você o RDP e tenha que aguardar minutos no primeiro logon visualizando uma tela azul com a mensagem:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Solução
No início da compilação de imagem, verifique se não há reinicializações pendentes necessárias adicionando um personalizador de reinicialização do Windows como a última personalização e se toda a instalação do software foi concluída. Por fim, adicione a opção [/Mode: VM](/windows-hardware/manufacture/desktop/sysprep-command-line-options) ao Sysprep padrão que o AIB usa, veja abaixo, ' VMs criadas de AIB images não criam com êxito ' > ' substituindo os comandos '  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>As VMs criadas a partir de imagens AIB não são criadas com êxito

Por padrão, o construtor de imagens do Azure executa o código *de desprovisionamento* no final de cada fase de personalização de imagem para *generalizar* a imagem. Generalizar é um processo em que a imagem é configurada para ser reutilizada para criar várias VMs e você pode passar configurações de VM, como nome de host, nome de usuário, etc. Para o Windows, o construtor de imagem do Azure executa o *Sysprep* e para execuções do construtor de imagem do Azure do Linux `waagent -deprovision` . 

Para o Windows, o construtor de imagem do Azure usa um comando Sysprep genérico. No entanto, isso pode não ser adequado para todas as generalizações do Windows bem-sucedidas. O construtor de imagens do Azure permite que você personalize o comando Sysprep. Observe que o construtor de imagem do Azure é uma ferramenta de automação de imagem. É responsável pela execução bem-sucedida do comando Sysprep. Mas você pode precisar de comandos diferentes do Sysprep para tornar sua imagem reutilizável. Para o Linux, o construtor de imagem do Azure usa um `waagent -deprovision+user` comando genérico. Para obter mais informações, consulte [Microsoft Azure documentação do agente do Linux](https://github.com/Azure/WALinuxAgent#command-line-options).

Se você estiver migrando uma personalização existente e estiver usando diferentes comandos Sysprep/waagent, poderá experimentar os comandos genéricos do Image Builder. Se a criação da VM falhar, use os comandos Sysprep/waagent anteriores.

> [!NOTE]
> Se AIB criar uma imagem personalizada do Windows com êxito e você criar uma VM a partir dela, então, localize a VM não será criada com êxito (por exemplo, o comando de criação de VM não é concluído/tempo limite), você precisará examinar a documentação do Sysprep do Windows Server. Ou, você pode gerar uma solicitação de suporte com a equipe de suporte do Windows Server Sysprep Customer Services, que pode solucionar problemas e avisar sobre o comando Sysprep correto.

### <a name="command-locations-and-filenames"></a>Locais de comando e nomes de FileName

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Comando Sysprep: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Comando de desprovisionamento: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Como substituir os comandos

Para substituir os comandos, use os provisionadores do script PowerShell ou Shell para criar os arquivos de comando com o nome exato do arquivo e coloque-os nos diretórios listados anteriormente. O construtor de imagens do Azure lê esses comandos e a saída é gravada no *customization. log*.

## <a name="getting-support"></a>Como Obter Suporte
Se você tiver mencionado as diretrizes e ainda não conseguir solucionar o problema, poderá abrir um caso de suporte. Ao fazer isso, selecione o tópico de suporte e produto correto, fazendo isso envolverá a equipe de suporte do construtor de imagem de VM do Azure.

Selecionando o produto do caso:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [visão geral do construtor de imagens do Azure](../image-builder-overview.md).
