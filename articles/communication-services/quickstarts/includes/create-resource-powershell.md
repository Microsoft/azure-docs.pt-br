---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644720"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet/).
- Instalar o [Módulo do PowerShell Azure Az](https://docs.microsoft.com/powershell/azure/)

## <a name="create-azure-communication-resource"></a>Criar o recurso de comunicação do Azure

Para criar um recurso dos Serviços de Comunicação do Azure, [entre na CLI do Azure](/cli/azure/authenticate-azure-cli). Faça isso por meio do terminal usando o comando ```Connect-AzAccount``` e fornecendo suas credenciais. Execute o seguinte comando para criar o recurso:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Se quiser selecionar uma assinatura específica, você também poderá especificar o sinalizador ```--subscription``` e fornecer a ID da assinatura.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Configure o recurso dos Serviços de Comunicação com as seguintes opções:

* O grupo de recursos
* O nome do recurso dos Serviços de Comunicação
* A geografia à qual o recurso será associado

Na próxima etapa, você pode atribuir marcas ao recurso. As marcas podem ser usadas para organizar os seus recursos do Azure. Confira a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre marcas.

## <a name="manage-your-communication-services-resource"></a>Gerenciar o seu recurso dos Serviços de Comunicação

Para adicionar marcas ao recurso dos Serviços de Comunicação, execute os comandos a seguir. Você também pode direcionar uma assinatura específica.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Para listar todos os recursos dos Serviços de Comunicação do Azure em uma determinada assinatura, use o seguinte comando:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Para listar todas as informações em um determinado recurso, use o seguinte comando:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
