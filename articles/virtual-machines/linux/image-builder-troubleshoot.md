---
title: Solucionar problemas do Serviço de Construtor de Imagens do Azure
description: Solucione problemas e erros comuns que ocorrem ao usar o Serviço do Construtor de Imagens de VM do Azure
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666212"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Solucionar problemas do Serviço de Construtor de Imagens do Azure

Este artigo ajuda você a solucionar problemas e resolver erros comuns que pode encontrar ao usar o Serviço do Construtor de Imagens do Azure.

Podem ocorrer falhas do AIB em duas áreas:
- Envio do modelo de imagem
- Build da imagem

## <a name="troubleshoot-image-template-submission-errors"></a>Solucionar problemas no envio do modelo de imagem

Erros de envio do modelo de imagem são retornados somente no momento do envio. Não há um log de erros com os erros de envio do modelo de imagem. Se tiver ocorrido um erro durante o envio, retorne-o verificando o status do modelo, especificamente examinando o `ProvisioningState` e `ProvisioningErrorMessage`/`provisioningError`.

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> Para o PowerShell, você precisará instalar os [Módulos do PowerShell do Construtor de Imagens do Azure](../windows/image-builder-powershell.md#prerequisites).

As seções a seguir incluem diretrizes de resolução de problemas para erros comuns que ocorrem no envio do modelo de imagem.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>No momento, não há suporte para atualização de modelos de imagem

#### <a name="error"></a>Erro

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Causa

O modelo já existe.

#### <a name="solution"></a>Solução

Se você envia um modelo de configuração de imagem e o envio falha, um artefato de modelo com falha ainda existe. Exclua o modelo com falha.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>A operação do recurso foi concluída com o estado de provisionamento de terminal 'Failed'

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

Na maioria dos casos, o erro de falha na implantação do recurso ocorre devido à falta de permissões.

#### <a name="solution"></a>Solução

Dependendo do cenário, o Construtor de Imagens do Azure pode precisar de permissões para:
- Grupo de recursos da Galeria de Imagens Compartilhadas ou imagem de origem
- Recurso da Galeria de Imagens Compartilhadas ou imagem de distribuição
- A conta de armazenamento, o contêiner ou o blob que o personalizador de arquivos está acessando. 

Para obter mais informações sobre como configurar permissões, confira [Configurar permissões de serviço do Construtor de Imagens do Azure usando a CLI do Azure](image-builder-permissions-cli.md) ou [Configurar permissões de serviço do Construtor de Imagens do Azure usando o PowerShell](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Erro ao obter imagem gerenciada

#### <a name="error"></a>Erro

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Causa

Falta de permissões.

#### <a name="solution"></a>Solução

Dependendo do cenário, o Construtor de Imagens do Azure pode precisar de permissões para:
* Grupo de recursos da Galeria de Imagens Compartilhadas ou imagem de origem
* Recurso da Galeria de Imagens Compartilhadas ou imagem de distribuição
* A conta de armazenamento, o contêiner ou o blob que o personalizador de arquivos está acessando. 

Para obter mais informações sobre como configurar permissões, confira [Configurar permissões de serviço do Construtor de Imagens do Azure usando a CLI do Azure](image-builder-permissions-cli.md) ou [Configurar permissões de serviço do Construtor de Imagens do Azure usando o PowerShell](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>Falha na etapa de build da versão da imagem

#### <a name="error"></a>Erro
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Causa

O Construtor de Imagens do Azure não pode localizar a imagem de origem.

#### <a name="solution"></a>Solução

Verifique se a imagem de origem está correta e existe na localização do Serviço do Construtor de Imagens do Azure.

### <a name="downloading-external-file-to-local-file"></a>Baixar arquivo externo para arquivo local

#### <a name="error"></a>Erro

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Causa

O nome ou a localização do arquivo está incorreto ou a localização não é acessível.

#### <a name="solution"></a>Solução

Verifique se o arquivo está acessível. Verifique se o nome e a localização estão corretos.

## <a name="troubleshoot-build-failures"></a>Solucionar problemas de build

Para falhas de build da imagem, você pode obter o erro no `lastrunstatus` e examinar os detalhes em customizaton.log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Log de personalização

Quando o build da imagem está em execução, logs são criados e armazenados em uma conta de armazenamento. O Construtor de Imagens do Azure cria a conta de armazenamento no grupo de recursos temporário quando você cria um artefato de modelo de imagem.

O nome da conta de armazenamento usa o seguinte padrão: **IT_\<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Por exemplo, *IT_aibmdi_helloImageTemplateLinux01*.

Para ver o customization.log na conta de armazenamento no grupo de recursos, selecione **Conta de Armazenamento** > **Blobs** > `packerlogs`.  Depois, selecione **diretório > customization.log**.


### <a name="understanding-the-customization-log"></a>Noções básicas sobre o log de personalização

O log é detalhado. Ele aborda o build da imagem, incluindo problemas com a distribuição da imagem, como a replicação da Galeria de Imagens Compartilhadas. Esses erros aparecem na mensagem de erro do status do modelo de imagem.

O customization.log inclui as seguintes fases:

1. Implantar a VM de build e as dependências usando modelos do ARM na fase de preparo do grupo de recursos IT_. Essa fase inclui vários POSTs para o provedor de recursos do Construtor de Imagens do Azure:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Fase de status das implantações. Esta fase inclui o status da implantação de cada recurso:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Fase de conexão à VM de build.

    No Windows, o Serviço do Construtor de Imagens do Azure se conecta usando o WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    No Linux, o Serviço do Construtor de Imagens do Azure se conecta usando SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Fase de execução das personalizações. Quando as personalizações são executadas, você pode identificá-las examinando customization.log. Pesquise por *(telemetria)* .
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
5. Fase de desprovisionamento. O Construtor de Imagens do Azure adiciona um personalizador oculto. Essa etapa de desprovisionamento é responsável por preparar a VM para o desprovisionamento. Ela executa o Sysprep do Windows (usando c:\DeprovisioningScript.ps1) ou o desprovisionamento de waagent do Linux (usando /tmp/DeprovisioningScript.sh). 

    Por exemplo:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Fase de limpeza. Após a conclusão do build, os recursos do Construtor de Imagens do Azure são excluídos.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Dicas para solução de problemas de personalização de script/embutida
- Testar o código antes de fornecê-lo ao Construtor de Imagens
- Verificar se os Firewalls e o Azure Policy permitem a conectividade com recursos remotos.
- Gerar comentários para o console, por exemplo, usar `Write-Host` ou `echo`, o que permitirá pesquisar o customization.log.

## <a name="troubleshoot-common-build-errors"></a>Solução de problemas comuns de build

### <a name="packer-build-command-failure"></a>Falha no comando de build do Packer

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

Examine o log para localizar falhas nos personalizadores. Pesquise por *(telemetria)* . 

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

### <a name="timeout-exceeded"></a>Tempo limite ultrapassado

#### <a name="error"></a>Erro

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Causa

O build ultrapassou o tempo limite. Esse erro é visto no 'lastrunstatus'.

#### <a name="solution"></a>Solução

1. Examine o customization.log. Identifique o último personalizador executado. Pesquise por `(telemetry)` começando pelo final do log. 

2. Verifique as personalizações do script. Elas podem não estar suprimindo a interação do usuário para comandos, como as opções `quiet`. Por exemplo, `apt-get install -y` faz com que a execução do script aguarde a interação do usuário.

3. Se estiver usando o personalizador `File` para baixar artefatos com mais de 20 MB, confira a seção de soluções alternativas.

4. Examine os erros e as dependências no script que podem fazê-lo aguardar.

5. Se acreditar que as personalizações precisam de mais tempo, aumente [buildTimeoutInMinutes](image-builder-json.md). O padrão é de quatro horas.

6. Se tiver ações com uso intensivo de recursos, como baixar gigabytes de arquivos, considere o tamanho da VM de build subjacente. O serviço usa uma VM Standard_D1_v2. Ela tem 1 vCPU e 3,5 GB de memória. Se você estiver baixando 50 GB, isso provavelmente esgotará os recursos da VM e causará falhas de comunicação entre o Serviço do Construtor de Imagens do Azure e a VM de build. Repita o build com uma VM com mais memória definindo o [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Muito tempo para o download de um arquivo

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

O personalizador de arquivos está baixando um arquivo grande.

#### <a name="solution"></a>Solução

O personalizador de arquivos é adequado apenas para downloads de arquivos pequenos, com menos de 20 MB. Para downloads de arquivos maiores, use um script ou um comando embutido. Por exemplo, no Linux, você pode usar o `wget` ou o `curl`. No Windows, você pode usar `Invoke-WebRequest`.

### <a name="error-waiting-on-shared-image-gallery"></a>Erro ao aguardar a Galeria de Imagens Compartilhadas

#### <a name="error"></a>Erro

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Causa

O Construtor de Imagens atingiu o tempo limite aguardando a imagem ser adicionada e replicada para a SIG (Galeria de Imagens Compartilhadas). Se a imagem está sendo injetada na SIG, podemos presumir que o build da imagem foi bem-sucedido. No entanto, o processo geral falhou, pois o Construtor de Imagens estava aguardando a Galeria de Imagens Compartilhadas para concluir a replicação. Embora o build tenha falhado, a replicação continua. Você pode obter as propriedades da versão da imagem verificando o *runOutput* de distribuição.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Solução

Aumente o **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Eventos de informação de poucos recursos do Windows

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

Esgotamento de recursos. Normalmente, esse problema é visto quando o Windows Update é executado usando o tamanho de VM de build padrão D1_V2.

#### <a name="solution"></a>Solução

Aumente o tamanho da VM de build.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Builds concluídos, mas nenhum artefato criado

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

Tempo limite atingido pela espera pela criação dos recursos do Azure necessários.

#### <a name="solution"></a>Solução

Execute o build mais uma vez para tentar novamente.

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

Falta de permissões.

#### <a name="solution"></a>Solução

Verifique novamente se o Construtor de Imagens do Azure tem todas as permissões necessárias. 

Para obter mais informações sobre como configurar permissões, confira [Configurar permissões de serviço do Construtor de Imagens do Azure usando a CLI do Azure](image-builder-permissions-cli.md) ou [Configurar permissões de serviço do Construtor de Imagens do Azure usando o PowerShell](image-builder-permissions-powershell.md)

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

A causa pode ser um problema relacionado ao tempo devido ao tamanho da VM D1_V2. Se as personalizações forem limitadas e forem executadas em menos de três segundos, serão executados comandos de sysprep pelo Construtor de Imagens do Azure para desprovisionar. Quando o Construtor de Imagens do Azure desprovisiona, o comando de sysprep verifica o *WindowsAzureGuestAgent*, que pode não estar totalmente instalado, causando o problema com o tempo. 

#### <a name="solution"></a>Solução

Aumente o tamanho da VM. Você também pode adicionar uma personalização de suspensão do PowerShell de 60 segundos para evitar o problema com o tempo.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Cancelamento do construtor após o cancelamento do contexto

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
O serviço do Construtor de Imagens usa a porta 22 (Linux) ou 5986 (Windows) para se conectar à VM de build. Isso ocorre quando o serviço é desconectado da VM de build durante um build de imagem. Os motivos da desconexão podem variar, mas habilitar ou configurar firewalls no script pode bloquear as portas acima.

#### <a name="solution"></a>Solução
Examine nos scripts se há alterações/habilitação do firewall ou alterações no SSH ou no WinRM e verifique se as alterações permitem a conectividade constante entre o serviço e a VM de build nas portas acima. Para obter mais informações sobre a rede do Construtor de Imagens, confira os [requisitos](./image-builder-networking.md).

## <a name="devops-task"></a>Tarefa do DevOps 

### <a name="troubleshooting-the-task"></a>Solucionar problemas da tarefa
A tarefa falhará apenas se ocorrer um erro durante a personalização. Quando isso acontecer, a tarefa relatará a falha e deixará o grupo de recursos de preparo, com os logs, para que você possa identificar o problema. 

Para localizar o log, você precisa conhecer o nome do modelo, entrar no pipeline > build com falha > analisar a tarefa de DevOps do AIB. Você verá o log e um nome de modelo:
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
Acesse a conta de armazenamento > blobs > contêineres > logs.

### <a name="troubleshooting-successful-builds"></a>Solução de problemas em builds bem-sucedidos
Pode haver alguns casos em que você precise investigar builds bem-sucedidos e queira examinar o log. Conforme mencionado, se o build de imagem for bem-sucedido, o grupo de recursos de preparo que contém os logs será excluído como parte da limpeza. No entanto, você pode introduzir uma suspensão após o comando embutido e obter os logs enquanto o build estiver em pausa. Para isso, siga estas etapas:
 
1. Atualize o comando embutido e adicione: Write-Host / Echo “Sleep” – isso permitirá pesquisar no log
2. Adicione uma suspensão de pelo menos dez minutos. Você pode usar o comando [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) ou `Sleep` no Linux.
3. Use o método acima para identificar a localização do log e continue baixando/verificando o log até que ele chegue à suspensão.


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

Se o build não foi cancelado por um usuário, ele foi cancelado pelo Agente do Usuário do Azure DevOps. Provavelmente, o tempo limite de uma hora foi atingido devido aos recursos do Azure DevOps. Se estiver usando um projeto e um agente privados, você terá 60 minutos de tempo de build. Se o build ultrapassar o tempo limite, o DevOps cancelará a tarefa em execução.

Para obter mais informações sobre as limitações e os recursos do Azure DevOps, confira [agentes hospedados pela Microsoft](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)
 
#### <a name="solution"></a>Solução

Você pode hospedar seus agentes do DevOps ou procurar reduzir o tempo do build. Por exemplo, se estiver distribuindo para a Galeria de Imagens Compartilhadas, replique para uma região. Se quiser, replique de maneira assíncrona. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Logon do Windows lento: 'Aguarde o Instalador de Módulos do Windows'

#### <a name="error"></a>Erro
Depois de criar uma imagem do Windows 10 com o Construtor de Imagens e criar uma VM baseada na imagem, você usa o RDP e precisa aguardar minutos no primeiro logon, vendo uma tela azul com a mensagem:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Solução
Primeiro, no build da imagem, verifique se não há reinicializações pendentes necessárias adicionando um personalizador de reinicialização do Windows como a última personalização. Depois, verifique se toda a instalação do software foi concluída. Por fim, adicione a opção [/mode:vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) ao sysprep padrão usado pelo AIB. Confira abaixo, 'VMs criadas com base em imagens do AIB não são criadas com êxito' > 'Substituindo os comandos'  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>VMs criadas com base em imagens do AIB não são criadas com êxito

Por padrão, o Construtor de Imagens do Azure executa o código de *desprovisionamento* no final de cada fase de personalização da imagem para *generalizar* a imagem. A generalização é um processo em que a imagem é configurada para ser reutilizada para criar várias VMs, e você pode passar configurações da VM, como o nome de host, o nome de usuário etc. Para o Windows, o Construtor de Imagens do Azure executa o *Sysprep* e, para Linux, ele executa `waagent -deprovision`. 

Para o Windows, o Construtor de Imagens do Azure usa um comando Sysprep genérico. No entanto, isso pode não ser adequado para todas as generalizações bem-sucedidas do Windows. O Construtor de Imagens do Azure permite que você personalize o comando Sysprep. Observe que o Construtor de Imagens do Azure é uma ferramenta de automação de imagem. Ele é responsável pela execução bem-sucedida do comando Sysprep. No entanto, você pode precisar de comandos Sysprep diferentes para tornar sua imagem reutilizável. Para o Linux, o Construtor de Imagens do Azure usa um comando `waagent -deprovision+user` genérico. Para obter mais informações, confira [Documentação do Agente de Linux do Microsoft Azure](https://github.com/Azure/WALinuxAgent#command-line-options).

Se você está migrando uma personalização existente e está usando comandos Sysprep/waagent diferentes, pode experimentar os comandos genéricos do Construtor de Imagens. Se a criação da VM falhar, use os comandos Sysprep/waagent anteriores.

> [!NOTE]
> Se o AIB criar uma imagem personalizada do Windows com êxito e você criar uma VM baseada dela e a VM não for criada com êxito (por exemplo, se o comando de criação da VM não for concluído o atingir o tempo limite), você precisará examinar a documentação do Sysprep do Windows Server. Você também pode gerar uma solicitação de suporte para a equipe de suporte do atendimento ao cliente do Sysprep do Windows Server, que pode solucionar problemas e orientar quanto ao comando Sysprep correto.

### <a name="command-locations-and-filenames"></a>Nomes de arquivo e locais dos comandos

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

Para substituir os comandos, use os provisionadores de script de shell ou o PowerShell para criar os arquivos de comando com o nome exato do arquivo e coloque-os nos diretórios listados anteriormente. O Construtor de Imagens do Azure lê esses comandos e a saída é gravada no *customization.log*.

## <a name="getting-support"></a>Como Obter Suporte
Se você consultou as diretrizes e ainda não consegue solucionar seu problema, abra caso de suporte. Ao fazer isso, selecione o tópico de suporte e o produto corretos. Fazer isso envolverá a equipe de suporte do Construtor de Imagens de VM do Azure.

Selecionando o produto do caso:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira a [Visão geral do Construtor de Imagens do Azure](../image-builder-overview.md).
