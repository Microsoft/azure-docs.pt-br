---
title: "Operações de implantação com o Azure Resource Manager | Microsoft Docs"
description: "Descreve como exibir as operações de implantação do Azure Resource Manager com o portal, o PowerShell, a CLI do Azure e a API REST."
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: tomfitz
ms.openlocfilehash: fb6b3b357fd1f66184e480115a9c863ba31ac193
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Exibir operações de implantação com o Azure Resource Manager


Você pode exibir as operações para uma implantação por meio do portal do Azure. Você pode estar mais interessado em ver as operações quando recebeu um erro durante a implantação para que este artigo foque em exibir as operações que falharam. O portal fornece uma interface que permite encontrar facilmente os erros e determinar as possíveis correções.

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="portal"></a>Portal
Para ver as operações de implantação, use as etapas a seguir:

1. Para o grupo de recursos envolvido na implantação, observe o status da última implantação. Selecione esse status para obter mais detalhes.
   
    ![status da implantação](./media/resource-manager-deployment-operations/deployment-status.png)
2. Você vê o histórico recente de implantação. Selecione a implantação que falhou.
   
    ![status da implantação](./media/resource-manager-deployment-operations/select-deployment.png)
3. Selecione o link para ver uma descrição do motivo da falha na implantação. Na imagem abaixo, o registro DNS não é exclusivo.  
   
    ![exibir implantação com falha](./media/resource-manager-deployment-operations/view-error.png)
   
    Essa mensagem de erro deve ser suficiente para você começar a solucionar o problema. No entanto, se você precisar de mais detalhes sobre quais tarefas foram concluídas, poderá exibir as operações, como mostrado nas etapas a seguir.
4. Você pode exibir todas as operações de implantação na folha **Implantação**. Selecione qualquer operação para ver mais detalhes.
   
    ![exibir operações](./media/resource-manager-deployment-operations/view-operations.png)
   
    Neste caso, verá que a conta de armazenamento, a rede virtual e o conjunto de disponibilidades foram criados com êxito. O endereço IP público falhou e outros recursos não foram tentados.
5. Você pode exibir os eventos para a implantação selecionando **Eventos**.
   
    ![exibir eventos](./media/resource-manager-deployment-operations/view-events.png)
6. Você vê todos os eventos da implantação e seleciona qualquer um para obter mais detalhes. Observe também a IDs de correlação. Esse valor pode ser útil ao trabalhar com o suporte técnico para solucionar um problema de implantação.
   
    ![consultar eventos](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Para obter o status geral de uma implantação, use e comando **Get-AzureRmResourceGroupDeployment** . 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Ou você pode filtrar os resultados para mostrar somente as implantações que falharam.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Cada implantação inclui várias operações. Cada operação representa uma etapa no processo de implantação. Para descobrir o que deu errado com uma implantação, geralmente você precisa ver os detalhes sobre as operações de implantação. É possível ver o status das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Que retorna várias operações com cada uma no seguinte formato:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Para obter mais detalhes sobre as operações com falha, recupere as propriedades das operações com o estado **Falha** .

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Retorna todas as operações com falha com cada uma no seguinte formato:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Observe serviceRequestId e trackingId para a operação. serviceRequestId pode ser útil ao trabalhar com o suporte técnico para solucionar um problema de implantação. Você usará trackingId na próxima etapa para focar em uma determinada operação.
4. Para obter a mensagem de status de uma determinada operação com falha, use o seguinte comando:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Que retorna:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Cada operação de implantação no Azure inclui conteúdo da solicitação e resposta. O conteúdo da solicitação é aquilo que é enviado para o Azure durante a implantação (por exemplo, criar uma VM, disco do sistema operacional e outros recursos). O conteúdo da resposta é aquilo que o Azure enviou de volta com base em sua solicitação de implantação. Durante a implantação, você pode usar o parâmetro **DeploymentDebugLogLevel** para especificar que a solicitação e/ou resposta seja mantida no log. 

  Você obtém informações do log e salva-as localmente usando os seguintes comandos do PowerShell:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>CLI do Azure

1. Obtenha o status geral de uma implantação com o comando **azure group deployment show** .

  ```azurecli
  azure group deployment show --resource-group ExampleGroup --name ExampleDeployment --json
  ```
  
  Um dos valores retornados é **correlationId**. Esse valor é usado para acompanhar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico na solução de um problema de implantação.

  ```azurecli
  "properties": {
    "provisioningState": "Failed",
    "correlationId": "4002062a-a506-4b5e-aaba-4147036b771a",
  ```

2. Para ver as operações de uma implantação, use:

  ```azurecli
  azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json
  ```

## <a name="rest"></a>REST

1. Obtenha informações sobre uma implantação com a operação [Obter informações sobre uma implantação de modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    Na resposta, observe em particular os elementos **provisioningState**, **correlationId** e **error**. **correlationId** é usado para acompanhar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico na solução de um problema de implantação.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Obtenha informações sobre operações de implantação com a operação [Listar todas as operações de implantação de modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    A resposta inclui informações de solicitação e/ou resposta com base no que foi especificado na propriedade **debugSetting** durante a implantação.

  ```json
  {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
  }
  ```


## <a name="next-steps"></a>Próximas etapas
* Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como usar os logs de atividades para monitorar outros tipos de ação, confira [View activity logs to manage Azure resources](resource-group-audit.md) (Exibir logs de atividades para gerenciar recursos do Azure).
* Para validar sua implantação antes de executá-la, consulte [Implantar um grupo de recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md).

