---
title: Gerenciar pacotes do Python 2 no Automação do Azure
description: Este artigo descreve como gerenciar pacotes do Python 2 na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f98b1454ff59eae62bcab7792fd7fd742babfb23
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478221"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes do Python 2 no Automação do Azure

A Automação do Azure permite executar runbooks do Python 2 no Azure e no Hybrid Runbook Worker do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como você gerencia e usa pacotes do Python na Automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua Conta de Automação, selecione **pacotes do Python 2** em **Recursos Compartilhar**. Clique em **+ Adicionar um pacote do Python 2**.

![Adicionar pacote do Python](media/python-packages/add-python-package.png)

Na página **Adicionar pacote do Python 2**, selecione um pacote local para carregar. O pacote pode ser um arquivo `.whl` ou arquivo `.tar.gz`. Quando selecionado, clique em **OK** para carregar o pacote.

![Adicionar pacote do Python](media/python-packages/upload-package.png)

Depois que um pacote tiver sido importado, ele será listado na **pacotes do Python 2** página em sua conta de automação. Se for necessário remover um pacote, selecione o pacote e escolha **Excluir**  na página do pacote.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importe os pacotes com dependências

A automação do Azure não resolve dependências de pacotes do python durante o processo de importação. Há duas maneiras de importar um pacote com todas as suas dependências. Apenas uma das etapas a seguir precisa ser usado para importar os pacotes para sua conta de automação.

### <a name="manually-download"></a>Baixar manualmente

No Windows de 64 bits de máquina com [Python 2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalado, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Depois que os pacotes são baixados, você pode importá-los em sua conta de automação.

### <a name="runbook"></a>Runbook

Importe o runbook python [importação Python 2 pacotes do pypi na conta de automação do Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) da galeria à sua conta de automação. Verifique se as configurações de execução estiver definidas como **Azure** e iniciar o runbook com os parâmetros. O runbook exigir uma conta executar como da conta de automação trabalhar. Para cada parâmetro, lembre-se de que você iniciá-lo com a opção como mostra a lista e a imagem a seguir:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O runbook permite que você especifique qual pacote baixar, por exemplo, `Azure` (o quarto parâmetro) baixará todos os módulos do Azure e todas as suas dependências, que é aproximadamente 105.

Após a conclusão do runbook você pode verificar a **pacotes do Python 2** página sob **recursos compartilhados** em sua conta de automação para verificar se eles pacote foi importado corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Depois de importar um pacote, agora você pode usá-lo em um runbook. O exemplo a seguir usa o [ pacote utilitário de Automação do Azure](https://github.com/azureautomation/azure_automation_utility). Esse pacote facilita o uso do Python com a Automação do Azure. Para usar o pacote, siga as instruções no repositório GitHub e adicione-o ao runbook, usando `from azure_automation_utility import get_automation_runas_credential` por exemplo, para importar a função para recuperar a Conta Executar como.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Desenvolva e teste runbooks offline

Para desenvolver e testar os runbooks do Python 2 offline, você pode usar o módulo de [ativos emulados do python de Automação do Azure](https://github.com/azureautomation/python_emulated_assets) no GitHub. Esse módulo permite referenciar os recursos compartilhados como credenciais, variáveis, conexões e certificados.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os runbooks do Python 2, consulte [Meu primeiro runbook do Python 2](automation-first-runbook-textual-python2.md)
