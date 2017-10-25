---
title: "Entender os erros de implantação do Azure | Microsoft Docs"
description: "Descreve como saber mais sobre os erros de implantação do Azure."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "erro de implantação, implantação do azure, implante no azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-deployment-errors"></a>Entender os erros de implantação do Azure
Este tópico descreve os erros de implantação e como você pode descobrir mais informações sobre um erro. Para obter resoluções de erros comuns de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Dois tipos de erros
Há dois tipos de erros que você pode receber:

* erros de validação
* erros de implantação

A imagem a seguir mostra o log de atividades de uma assinatura. Ela representa duas implantações. Em uma implantação, o modelo não foi aprovado na validação (**Validar**) e não continuou. Na outra implantação, o modelo foi aprovado na validação, mas falhou ao criar os recursos (**Implantações de Gravação**). 

![mostrar código de erro](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Erros de validação ocorrem em cenários que podem ser determinados antes da implantação. Eles incluem erros de sintaxe no modelo ou a tentativa de implantar recursos que excederão suas cotas de assinatura. Os erros de implantação surgem de condições que ocorrem durante o processo de implantação. Eles incluem a tentativa de acessar um recurso que está sendo implantado em paralelo.

Ambos os tipos de erro retornam um código de erro que você pode usar para solucionar os problemas de implantação. Os dois tipos de erro aparecem no [log de atividades](resource-group-audit.md). No entanto, os erros de validação não aparecem no seu histórico de implantação porque a implantação nunca foi iniciada.

## <a name="determine-error-code"></a>Determinar o código de erro

Saiba mais sobre um erro examinando a mensagem e o código de erro. O artigo [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) lista as resoluções por código de erro. Este tópico mostra como usar o portal do Azure para descobrir o código de erro.

### <a name="validation-errors"></a>Erros de validação

Durante a implantação por meio do portal, você vê um erro de validação depois de enviar os valores.

![mostrar erro de validação no portal](./media/resource-manager-troubleshoot-tips/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem a seguir, você vê um erro **InvalidTemplateDeployment** e uma mensagem que indica uma implantação bloqueada por política.

![mostrar detalhes da validação](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Erros de implantação

Quando a operação for aprovada na validação, mas falhar durante a implantação, você verá o erro nas notificações. Selecione a notificação.

![erro de notificação](./media/resource-manager-troubleshoot-tips/notification.png)

Você verá mais detalhes sobre a implantação. Selecione a opção para obter mais informações sobre o erro.

![falha na implantação](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Você verá a mensagem de erro e os códigos de erro. Observe que há dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes necessários para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes necessários. 

![detalhes do erro](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>Habilitar o log de depuração
Às vezes, você precisa obter mais informações sobre a solicitação e a resposta para descobrir o que deu errado. Usando o PowerShell ou a CLI do Azure, você pode solicitar que informações adicionais sejam registradas durante a implantação.

- PowerShell

   No PowerShell, defina o parâmetro **DeploymentDebugLogLevel** como All, ResponseContent ou RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examine o conteúdo da solicitação com o seguinte cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Ou o conteúdo da resposta com:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Essas informações podem ajudá-lo a determinar se um valor no modelo está sendo definido incorretamente.

- CLI do Azure

   Examine as operações de implantação, com o comando a seguir:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Modelo aninhado

   Para registrar informações de depuração de um modelo aninhado, use o elemento **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>Criar um modelo de solução de problemas
Em alguns casos, a maneira mais fácil de solucionar problemas do seu modelo é testar partes dele. Você pode criar um modelo simplificado que permite a você se concentrar na parte que acredita estar causando o erro. Por exemplo, suponha que você esteja recebendo um erro ao fazer referência a um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que retorne a parte que pode estar causando o problema. Ele pode ajudá-lo a determinar se você está sendo aprovado nos parâmetros certos, usando funções de modelo corretamente e obtendo os recursos desejados.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Ou, vamos supor que você esteja enfrentando erros de implantação, os quais você acredita terem relação com as dependências definidas incorretamente. Teste seu modelo dividindo-o em modelos simplificados. Primeiro, crie um modelo que implanta um único recurso (como um SQL Server). Quando você tiver certeza de que esse recurso foi definido corretamente, adicione um recurso depende dele (como um Banco de Dados SQL). Quando esses dois recursos estiverem definidos corretamente, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implantação de teste, exclua o grupo de recursos para garantir o teste adequado das dependências. 

## <a name="check-deployment-sequence"></a>Verificar a sequência de implantação

Muitos erros de implantação ocorrem quando os recursos são implantados em uma sequência inesperada. Esses erros surgem quando as dependências não são definidas corretamente. Quando você não tiver uma dependência necessária, um recurso tenta usar um valor para outro recurso, mas o outro ainda não existir. Você obterá um erro informando que um recurso não foi encontrado. Você pode encontrar esse tipo de erro intermitente porque o tempo de implantação para cada recurso pode variar. Por exemplo, sua primeira tentativa de implantar seus recursos é bem-sucedida pois aleatoriamente conclui um recurso necessário no tempo. No entanto, a segunda tentativa falha porque o recurso necessário não foi concluída no tempo. 

Mas, para evitar definindo dependências que não são necessários. Quando você tiver dependências desnecessárias, você prolongar a duração da implantação, impedindo que os recursos que não são dependentes entre si seja implantado em paralelo. Além disso, você pode criar dependências circulares que bloqueiam a implantação. A função [reference](resource-group-template-functions-resource.md#reference) cria uma dependência implícita no recurso referenciado, quando esse recurso é implantado no mesmo modelo. Portanto, você pode ter dependências mais que as dependências especificada no **dependsOn** propriedade. O [resourceId](resource-group-template-functions-resource.md#resourceid) função não cria uma dependência implícita ou validar que o recurso existe.

Quando você encontrar problemas de dependência, você precisa obter informações sobre a ordem de implantação de recursos. Para exibir a ordem das operações de implantação:

1. Selecione o histórico de implantação do grupo de recursos.

   ![selecionar o histórico de implantação](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Selecione uma implantação no histórico e selecione **Eventos**.

   ![selecionar os eventos de implantação](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Examine a sequência de eventos de cada recurso. Preste atenção ao status de cada operação. Por exemplo, a imagem a seguir mostra três contas de armazenamento implantadas em paralelo. Observe que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   A imagem a seguir mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento e a terceira conta de armazenamento depende da segunda conta de armazenamento. Portanto, a primeira conta de armazenamento é iniciada, aceita e concluída antes que a próxima seja iniciada.

   ![implantação sequencial](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Até mesmo em cenários mais complicados, você pode usar a mesma técnica para descobrir quando a implantação é iniciada e concluída para cada recurso. Examine os eventos de implantação para ver se a sequência é diferente do esperado. Nesse caso, reavalie as dependências desse recurso.

O Resource Manager identifica dependências circulares durante a validação do modelo. Ele retorna uma mensagem de erro afirmando especificamente uma dependência circular existe. Para resolver uma dependência circular:

1. No modelo, localize o recurso identificado na dependência circular. 
2. Para esse recurso, examine o **dependsOn** propriedade e quaisquer usos da **referência** função para ver quais recursos ele depende. 
3. Examine os recursos para ver quais recursos eles dependem. Siga as dependências até você perceber um recurso depende do recurso original.
5. Para os recursos envolvidos na dependência circular, examine cuidadosamente todas as funções de **dependsOn** propriedade para identificar quaisquer dependências que não são necessários. Remova essas dependências. Se você não tiver certeza de que uma dependência é necessária, tente removê-lo. 
6. Reimplante o modelo.

Removendo valores do **dependsOn** propriedade pode causar erros quando você implanta o modelo. Se você encontrar um erro, adicione a dependência de volta para o modelo. 

Se essa abordagem não resolver a dependência circular, considere mover parte de sua lógica de implantação para recursos filho (como extensões ou definições de configuração). Configure os recursos filho para implantar após os recursos envolvidos na dependência circular. Por exemplo, suponha que você estiver implantando duas máquinas virtuais, mas você deve definir propriedades em cada um deles que se referem a outro. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende vm1 e vm2. A extensão define valores na vm1 que ele obtém da vm2.
4. Extensão da vm2 depende vm1 e vm2. A extensão define valores de vm2 obtido do vm1.

A mesma abordagem funciona para aplicativos de serviço de aplicativo. Considere a mudança de valores de configuração em um recurso filho de recurso de aplicativo. Você pode implantar dois aplicativos web na seguinte ordem:

1. webapp1
2. webapp2
3. a configuração para webapp1 depende de webapp1 e webapp2. Ele contém configurações do aplicativo com os valores do webapp2.
4. a configuração para webapp2 depende de webapp1 e webapp2. Ele contém configurações do aplicativo com os valores do webapp1.


## <a name="next-steps"></a>Próximas etapas
* Para obter resoluções de erros comuns de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](resource-group-audit.md).
* Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).
