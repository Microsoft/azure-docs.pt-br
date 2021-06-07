---
title: 'Guia de início rápido: Criar uma conta do Purview usando o Python'
description: Crie uma conta do Azure Purview usando o Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387087"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Guia de início rápido: Criar uma conta do Purview usando o Python

Neste guia de início rápido, você criará uma conta do Purview usando o Python. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* O próprio [locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Sua conta precisa ter permissão para criar recursos na assinatura

* Se o **Azure Policy** estiver impedindo todos os aplicativos de criarem uma **Conta de armazenamento** e um **namespace do EventHub**, será necessário criar uma exceção de política usando a marca, que poderá ser inserida durante o processo de criação de uma conta do Purview. O principal motivo é que, para cada conta do Purview criada, ele precisa criar um grupo de recursos gerenciados e, dentro desse grupo de recursos, uma conta de armazenamento e um namespace do EventHub. Para obter mais informações, veja [Criar um catálogo no portal](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Instalar o pacote do Python

1. Abra um terminal ou prompt de comando com privilégios de administrador. 
2. Primeiro, instale o pacote do Python para recursos de gerenciamento do Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Para instalar o pacote do Python para o Purview, execute o seguinte comando:

    ```python
    pip install azure-mgmt-purview
    ```

    O [SDK do Python para Purview](https://github.com/Azure/azure-sdk-for-python) dá suporte ao Python 2.7, 3.3, 3.4, 3.5, 3.6 e 3.7.

4. Para instalar o pacote do Python para a autenticação da Identidade do Azure, execute o seguinte comando:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > O pacote "azure-identity" pode ter conflitos com "azure-cli" em algumas dependências comuns. Se você encontrar qualquer problema de autenticação, remova "azure-cli" e as dependências dela ou use um computador limpo sem instalar o pacote "azure-cli" para fazê-lo funcionar.
    
## <a name="create-a-purview-client"></a>Criar um cliente do Purview

1. Crie um arquivo chamado **purview.py**. Adicione as instruções a seguir para adicionar referências aos namespaces.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Adicione o código a seguir ao método **Main**, que cria uma instância da classe PurviewManagementClient. Use esse objeto para criar uma conta do Purview, excluí-la, verificar a disponibilidade do nome e outras operações do provedor de recursos.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Criar uma conta do Purview

Adicione o código a seguir ao método **Main**, que cria uma **conta do Purview**. Se seu grupo de recursos já existir, comente a primeira instrução `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Agora, adicione a seguinte instrução para invocar o método **principal** quando o programa é executado:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Aqui está o código Python completo:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Executar o código

Compile e inicie o aplicativo, então verifique a execução do pipeline.

O console imprime o progresso de criação do data factory, do serviço vinculado, dos conjuntos de dados, do pipeline e da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath" conforme você especificou nas variáveis.

Veja o exemplo de saída:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Verificar a saída

Acesse a página **Contas do Purview** no portal do Azure e verifique a conta criada usando o código acima. 

## <a name="delete-purview-account"></a>Excluir uma conta do Purview

Para excluir uma conta do Purview, adicione o seguinte código ao programa:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Próximas etapas

O código deste tutorial cria uma conta do Purview e a exclui. Agora você pode baixar o SDK do Python e conhecer as outras ações do provedor de recursos que podem ser executadas para uma conta do Purview.

Passe para o próximo artigo para saber como permitir que os usuários acessem sua conta do Azure Purview. 

> [!div class="nextstepaction"]
> [Adicionar usuários à sua conta do Azure Purview](catalog-permissions.md)
