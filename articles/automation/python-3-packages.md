---
title: Gerenciar pacotes do Python 3 na automação do Azure
description: Este artigo informa como gerenciar pacotes do Python 3 (versão prévia) na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122027"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gerenciar pacotes python 3 (versão prévia) na automação do Azure

A automação do Azure permite que você execute runbooks do Python 3 (versão prévia) no Azure e em Hybrid runbook Workers do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Para importar um único pacote, consulte [importar um pacote](#import-a-package). Para importar um pacote com vários pacotes, consulte [importar um pacote com dependências](#import-a-package-with-dependencies). Este artigo descreve como gerenciar e usar pacotes python 3 (versão prévia) na automação do Azure.

## <a name="import-a-package"></a>Importar um pacote

Em sua conta de automação, selecione **pacotes do Python** em **recursos compartilhados**. Selecione **+ Adicionar um pacote do Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="A captura de tela da página de pacotes do Python 3 mostra pacotes do Python 3 no menu à esquerda e adiciona um pacote do Python 2 realçado.":::

Na página **Adicionar pacote do Python** , selecione **Python 3** para a **versão** e selecione um pacote local para carregar. O pacote pode ser um arquivo **.whl** ou **.tar.gz**. Quando o pacote for selecionado, selecione **OK** para carregá-lo.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Captura de tela mostra a página Adicionar pacote Python 3 com um arquivo tar. gz carregado selecionado.":::

Depois que um pacote tiver sido importado, ele será listado na página pacotes do Python em sua conta de automação, na guia **pacotes python 3 (versão prévia)** . Se você precisar remover um pacote, selecione o pacote e clique em **excluir**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Captura de tela mostra a página de pacotes do Python 3 depois que um pacote é importado.":::

### <a name="import-a-package-with-dependencies"></a>Importar um pacote com dependências

Você pode importar um pacote Python 3 e suas dependências importando o seguinte script Python para um runbook do Python 3 e, em seguida, executando-o.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importando o script para um runbook
Para obter informações sobre como importar o runbook, consulte [importar um runbook do portal do Azure](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Copie o arquivo do GitHub para o armazenamento que o portal pode acessar antes de executar a importação.

A página **importar um runbook** usa como padrão o nome do runbook para corresponder ao nome do script. Se você tiver acesso ao campo, poderá alterar o nome. O **tipo de runbook** pode padrão para **Python 2**. Se tiver, certifique-se de alterá-lo para **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Captura de tela mostra a página de importação de runbook 3 do Python.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Executando o runbook para importar o pacote e as dependências

Depois de criar e publicar o runbook, execute-o para importar o pacote. Consulte [Iniciar um runbook na automação do Azure](start-runbooks.md) para obter detalhes sobre como executar o runbook.

O script ( `import_py3package_from_pypi.py` ) requer os seguintes parâmetros.

| Parâmetro | Descrição |
|---------------|-----------------|
|subscription_id | ID da assinatura da conta de automação |
| resource_group | Nome do grupo de recursos no qual a conta de automação está definida |
| automation_account | Nome da conta de automação |
| module_name | Nome do módulo do qual importar `pypi.org` |

Para obter mais informações sobre como usar parâmetros com runbooks, consulte [trabalhar com parâmetros de runbook](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Com o pacote importado, você pode usá-lo em um runbook. Adicione o código a seguir para listar todos os grupos de recursos em uma assinatura do Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Próximas etapas

Para preparar um runbook do Python, confira [Criar um runbook do Python](learn/automation-tutorial-runbook-textual-python-3.md).
