---
title: Criar um runbook do Python 3 (versão prévia) na Automação do Azure
description: Este artigo ensinará você a criar, testar e publicar um runbook simples do Python 3 (versão prévia).
services: automation
ms.subservice: process-automation
ms.date: 02/16/2021
ms.topic: tutorial
ms.openlocfilehash: c19f7e177d51a3de75e7d7ae2b83442e23efd243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546135"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Tutorial: criar um runbook do Python 3 (versão prévia)

Este tutorial orientará você durante a criação de um [runbook do Python 3](../automation-runbook-types.md#python-runbooks) (versão prévia) na Automação do Azure. Os runbooks do Python serão compilados no Python 2 e 3. Você pode editar o código do runbook diretamente usando o editor de texto do portal do Azure.

> [!div class="checklist"]
> * Criar um runbook simples do Python
> * Testar e publicar o runbook
> * Executar e acompanhar o status do trabalho do runbook
> * Atualizar o runbook para iniciar uma máquina virtual do Azure com parâmetros de runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

- Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Conta de automação](../automation-security-overview.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.

- Uma máquina virtual do Azure. Durante este tutorial, você iniciará e interromperá o computador. Portanto, ele não deverá ser uma VM de produção.

- É possível usar um Hybrid Runbook Worker do Linux ou do Windows para executar runbooks do Python 3. Siga as instruções abaixo para instalar um Hybrid Runbook Worker do  [Linux](../automation-linux-hrw-install.md)  ou do  [Windows](../automation-windows-hrw-install.md) . Não há um pré-requisito específico para obter o Hybrid Worker do Linux. No entanto, configure o computador que hospeda o trabalho de runbook a fim de usar um Hybrid Worker do Windows para executar runbooks do Python 3 com base nestas informações: se há suporte somente para Python 3 ou se o Python 2 e 3 precisam coexistir.

   * Caso tenha *somente* o Python 2 ou o Python 3 instalado, será necessário acrescentar o caminho da pasta em que o arquivo python.exe está localizado à variável de ambiente **PATH**. Por exemplo, caso o arquivo python.exe esteja no caminho de instalação `C:\Python`, esse caminho precisará ser acrescentado à variável de ambiente **PATH**.

   * Caso tenha o Python 2 e o Python 3 instalados e queira executar os dois tipos de runbooks, será necessário configurar as seguintes variáveis de ambiente:

     * Python 2 – Crie uma variável de ambiente chamada `PYTHON_2_PATH` e especifique a pasta de instalação. Por exemplo, caso a pasta de instalação seja `C:\Python27`, esse caminho precisará ser adicionado à variável.

     * Python 3 – Crie uma variável de ambiente chamada `PYTHON_3_PATH` e especifique a pasta de instalação. Por exemplo, caso a pasta de instalação seja `C:\Python3`, esse caminho precisará ser adicionado à variável.

## <a name="create-a-new-runbook"></a>Criar um novo runbook

Você começa criando um runbook simples que exibe o texto *Olá, Mundo*.

1. No portal do Azure, abra sua conta da Automação.

    A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos é de módulos incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).

2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Selecione **Adicionar um runbook** para criar um runbook.

4. Atribua o nome **MyFirstRunbook-Python** ao runbook.

5. Clique em **Python 3** para selecionar a opção **Tipo de runbook**.

6. Clique em **Criar** para criar o runbook e abrir o editor textual.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Agora, você adiciona um comando simples para imprimir o texto `Hello World`.

```python
print("Hello World!")
```

Clique em **Salvar** para salvar o runbook.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, teste-o para verificar se ele funciona corretamente. Ao testar um runbook, execute a versão de rascunho dele e veja a respectiva saída de modo interativo.

1. Clique em **Testar painel** para abrir o painel **Teste**.

2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.

3. Um [trabalho de runbook](../automation-runbook-execution.md) é criado e seu status é exibido.
   O status do trabalho iniciará como **Na fila**, indicando que ele está aguardando um trabalho de runbook ficar disponível na nuvem. O status será alterado para **Iniciando** quando uma função de trabalho reivindicar o trabalho. Depois para **Em execução** quando o runbook realmente iniciar a execução.

4. Quando o trabalho do runbook é concluído, sua saída é exibida. Neste caso, você deve ver `Hello World`.

5. Feche o painel **Teste** para retornar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook criado ainda está em modo de rascunho. Você precisa publicá-lo antes que possa executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de rascunho. Neste caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Selecione **Publicar** para publicar o runbook e em **Sim** quando solicitado.

2. Se você rolar para a esquerda para exibir o runbook na página **Runbooks**, ele mostrará um **Status de Criação** **Publicado**.

3. Role de volta para a direita a fim de exibir o painel **MyFirstRunbook-Python3**.

   As opções na parte superior nos permitem iniciar o runbook, exibi-lo ou agendá-lo para iniciar em algum momento no futuro.

4. Selecione **Iniciar** e clique em **OK** quando o painel **Iniciar Runbook** abrir.

5. Um painel chamado **Trabalho** será aberto para o trabalho de runbook criado. É possível fechar esse painel, mas vamos deixá-lo aberto para acompanhar o progresso do trabalho.

6. O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.

7. Depois que o status do runbook mostrar **Concluído**, clique em **Saída**. O painel **Saída** será aberto, onde será possível conferir o texto `Hello World`.

8. Feche o painel **Saída**.

9. Clique em **Todos os Logs** a fim de abrir o painel **Fluxos** para o trabalho de runbook. Você deverá ver apenas `Hello World` no fluxo de saída. No entanto, esse painel poderá mostrar outros fluxos para um trabalho de runbook, como **Erro** e **Detalhado**, caso o runbook faça uma gravação neles.

10. Feche os painéis **Fluxos** e **Trabalho** para retornar ao painel **MyFirstRunbook-Python3**.

11. Clique em **Trabalhos** a fim de abrir a página **Trabalhos** para esse runbook. Essa página lista todos os trabalhos criados por esse runbook. Você deve ver apenas um trabalho listado, já que executou o trabalho apenas uma vez.

12. É possível selecionar esse trabalho para abrir o mesmo painel **Trabalho** que foi exibido ao iniciar o runbook. Esse painel permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar a autenticação para gerenciar os recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure.
Para fazer isso, o script precisa ser autenticado usando as credenciais da sua conta de Automação.

> [!NOTE]
> A conta de Automação precisa ter sido criada com o recurso de entidade de serviço para que haja um certificado Executar Como.
> Se a conta de Automação não foi criada com a entidade de serviço, é possível autenticar conforme descrito em [Autenticar com as Bibliotecas de Gerenciamento do Azure para Python](/azure/python/python-sdk-azure-authenticate).

1. Abra o editor de texto clicando em **Editar** no painel **MyFirstRunbook-Python3**.

2. Adicione o código a seguir para autenticar no Azure:

    ```python
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
    ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicionar código para criar o cliente da Computação em Python e iniciar a VM

Para trabalhar com VMs do Azure, crie uma instância do [Cliente da Computação do Azure para Python](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Use o cliente de computação para iniciar a VM. Adicione o seguinte código ao runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Em que `MyResourceGroup` é o nome do grupo de recursos que contém a VM e `TestVM` é o nome da VM que você deseja iniciar.

Teste e execute o runbook novamente para ver se ele inicia a VM.

## <a name="use-input-parameters"></a>Usar parâmetros de entrada

Atualmente, o runbook usa valores embutidos em código para os nomes do grupo de recursos e da VM. Agora, vamos adicionar o código que obtém esses valores dos parâmetros de entrada.

Você usa a variável `sys.argv` para obter os valores de parâmetro. Adicione o seguinte código ao runbook imediatamente após as outras instruções `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isso importa o módulo `sys` e cria duas variáveis para armazenar os nomes do Grupo de Recursos e da VM. Observe que o elemento da lista de argumentos, `sys.argv[0]`, é o nome do script e não é fornecido pelo usuário.

Agora, é possível modificar as duas últimas linhas do runbook para usar os valores de parâmetro de entrada em vez de usar valores embutidos em código:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Ao iniciar um runbook do Python no painel **Teste** ou como um runbook publicado, será possível inserir valores para os parâmetros na página **Iniciar Runbook** em **Parâmetros**.

Depois de começar a inserir um valor na primeira caixa, uma segunda aparecerá e assim por diante, para que você possa inserir quantos valores de parâmetro forem necessários.

Os valores estão disponíveis para o script como a matriz `sys.argv` no código que você acabou de adicionar.

Insira o nome do seu grupo de recursos como o valor para o primeiro parâmetro e o nome da VM para iniciar como o valor do segundo parâmetro.

![Inserir valores de parâmetro](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Clique em **OK** para iniciar o runbook. Ele é executado e inicia a VM especificada.

## <a name="error-handling-in-python"></a>O tratamento de erro no Python

Você também pode usar as convenções a seguir para recuperar vários fluxos de seus runbooks do Python, incluindo os fluxos WARNING, ERROR e DEBUG.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

O exemplo a seguir mostra essa convenção usada em um bloco `try...except`.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os tipos de runbook, suas vantagens e limitações, confira [Tipos de runbook da Automação do Azure](../automation-runbook-types.md).

- Para obter informações sobre como desenvolver para o Azure em Python, consulte [Azure para desenvolvedores de Python](/azure/python/).

- Para ver exemplos de runbooks do Python 3, confira o [GitHub da Automação do Azure](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
