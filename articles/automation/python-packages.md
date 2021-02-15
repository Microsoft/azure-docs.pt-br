---
title: Gerenciar pacotes do Python 2 no Automação do Azure
description: Este artigo informa como gerenciar pacotes do Python 2 na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/17/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: fd830afd5628591019902ca583f9cbc8e2a7ecad
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683401"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes do Python 2 no Automação do Azure

A Automação do Azure permite executar runbooks do Python 2 no Azure e no Hybrid Runbook Worker do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como gerenciar e usar pacotes do Python na Automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua conta de Automação, selecione **Pacotes do Python 2** em **Recursos Compartilhados**. Clique em **+ Adicionar um pacote do Python 2**.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="A captura de tela da página de pacotes do Python 2 mostra pacotes python 2 no menu à esquerda e adiciona um pacote Python 2 realçado.":::

Na página Adicionar Pacote Python 2, selecione um pacote local para carregar. O pacote pode ser um arquivo **.whl** ou **.tar.gz**. Quando o pacote for selecionado, clique em **OK** para carregá-lo.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Captura de tela mostra a página Adicionar pacote Python 2 com um arquivo tar. gz carregado selecionado.":::

Depois que um pacote tiver sido importado, ele será listado na página de pacotes do Python 2 na sua conta de Automação. Se você precisar remover um pacote, selecione o pacote e clique em **Excluir**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Captura de tela mostra a página de pacotes do Python 2 depois que um pacote é importado.":::

## <a name="import-packages-with-dependencies"></a>Importar pacotes com dependências

A Automação do Azure não resolve dependências de pacotes Python durante o processo de importação. Há duas maneiras de importar um pacote com todas as dependências. Somente uma das etapas a seguir precisa ser adotada a fim de importar os pacotes para sua conta de Automação.

### <a name="manually-download"></a>Baixar manualmente

Em um computador com Windows de 64 bits com [Python 2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalados, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Depois que os pacotes forem baixados, você poderá importá-los para sua conta de automação.

### <a name="runbook"></a>Runbook

 Para obter um runbook, [importe pacotes python 2 do PyPI para a conta de automação do Azure](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account) da organização GitHub da automação do Azure para sua conta de automação. Verifique se as configurações de execução estão definidas como **Azure** e inicie o runbook com os parâmetros. O runbook requer uma conta Executar como para que a conta de Automação funcione. Para cada parâmetro, certifique-se de iniciá-lo com a opção conforme mostrado na seguinte lista e imagem:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Captura de tela mostra a página Visão geral de import_py2package_from_pypi com o painel Iniciar runbook no lado direito.":::

O runbook permite que você especifique qual pacote baixar. Por exemplo, o uso do parâmetro `Azure` baixa todos os módulos do Azure e todas as dependências (cerca de 105).

Depois que o runbook for concluído, você poderá verificar os **Pacotes Python 2** em **Recursos Compartilhados** na sua conta de Automação para verificar se o pacote foi importado corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Com um pacote importado, você pode usá-lo em um runbook. O exemplo a seguir usa o [ pacote utilitário de Automação do Azure](https://github.com/azureautomation/azure_automation_utility). Esse pacote facilita o uso do Python com a Automação do Azure. Para usar o pacote, siga as instruções no repositório GitHub e adicione-o ao runbook. Por exemplo, você pode usar `from azure_automation_utility import get_automation_runas_credential` a fim de importar a função para recuperar a conta Executar como.

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

Para desenvolver e testar os runbooks do Python 2 offline, você pode usar o módulo [Ativos emulados do Python da Automação do Azure](https://github.com/azureautomation/python_emulated_assets) no GitHub. Esse módulo permite referenciar os recursos compartilhados como credenciais, variáveis, conexões e certificados.

## <a name="next-steps"></a>Próximas etapas

Para preparar um runbook do Python, confira [Criar um runbook do Python](learn/automation-tutorial-runbook-textual-python2.md).
