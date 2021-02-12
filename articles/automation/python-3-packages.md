---
title: Gerenciar pacotes do Python 3 na automação do Azure
description: Este artigo informa como gerenciar pacotes do Python 3 (versão prévia) na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734294"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gerenciar pacotes python 3 (versão prévia) na automação do Azure

A automação do Azure permite que você execute runbooks do Python 3 (versão prévia) no Azure e em Hybrid runbook Workers do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como gerenciar e usar pacotes python 3 (versão prévia) na automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Em sua conta de automação, selecione **pacotes do Python** em **recursos compartilhados**. Selecione **+ Adicionar um pacote do Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="A captura de tela da página de pacotes do Python 3 mostra pacotes do Python 3 no menu à esquerda e adiciona um pacote do Python 2 realçado.":::

Na página **Adicionar pacote do Python** , selecione Python 3 para a **versão** e selecione um pacote local para carregar. O pacote pode ser um arquivo **.whl** ou **.tar.gz**. Quando o pacote for selecionado, selecione **OK** para carregá-lo.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Captura de tela mostra a página Adicionar pacote Python 3 com um arquivo tar. gz carregado selecionado.":::

Depois que um pacote tiver sido importado, ele será listado na página pacotes do Python em sua conta de automação, na guia **pacotes python 3 (versão prévia)** . Se você precisar remover um pacote, selecione o pacote e clique em **excluir**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Captura de tela mostra a página de pacotes do Python 3 depois que um pacote é importado.":::

## <a name="import-packages-with-dependencies"></a>Importar pacotes com dependências

A automação do Azure não resolve dependências de pacotes do Python durante o processo de importação. No entanto, há uma maneira de importar um pacote com todas as suas dependências.

### <a name="manually-download"></a>Baixar manualmente

Em um computador com Windows de 64 bits com [Python 3,8](https://www.python.org/downloads/release/python-380/) e [Pip](https://pip.pypa.io/en/stable/) instalado, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Depois que os pacotes forem baixados, você poderá importá-los para sua conta de automação.

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
