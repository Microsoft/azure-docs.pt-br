---
title: Configurar parâmetros de entrada de runbook na Automação do Azure
description: Este artigo mostra como configurar parâmetros de entrada de runbook, que permitem que os dados sejam transmitidos para um runbook quando ele é iniciado.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 73e4dbb24b4e7c0c651f7d082c75b0f4a17158b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98890874"
---
# <a name="configure-runbook-input-parameters"></a>Configurar parâmetros de entrada do runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade de um runbook, permitindo transmitir dados para este quando ele é iniciado. Os parâmetros permitem que as ações de runbook sejam direcionadas para ambientes e cenários específicos. Este artigo descreve a configuração e o uso de parâmetros de entrada em seus runbooks.

Você pode configurar parâmetros de entrada para runbooks do PowerShell, Fluxo de Trabalho do PowerShell, gráfico e Python. Um runbook pode ter vários parâmetros com tipos de dados diferentes, ou não ter nenhum parâmetro. Os parâmetros de entrada podem ser obrigatórios ou opcionais, e você pode usar um valor padrão para parâmetros opcionais.

Atribua valores aos parâmetros de entrada para um runbook ao iniciá-lo. Você pode iniciar um runbook a partir do portal do Azure, de um serviço Web ou do PowerShell. Você também pode iniciá-lo como um runbook filho, que é chamado de embutido em outro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurar parâmetros de entrada em runbooks do PowerShell

O PowerShell e os runbooks de Fluxo de Trabalho do PowerShell na Automação do Azure aceitam parâmetros de entrada definidos usando as propriedades a seguir. 

| **Propriedade** | **Descrição** |
|:--- |:--- |
| Type |Obrigatórios. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| Nome |Obrigatórios. O nome do parâmetro. Esse nome deve ser exclusivo dentro do runbook, deve começar com uma letra e conter apenas letras, números ou sublinhado. |
| Obrigatório |Opcional. Valor booliano que especifica se o parâmetro exige um valor. Se você definir ele como Verdadeiro, um valor deverá ser fornecido quando o runbook for iniciado. Se você definir ele como Falso, um valor será opcional. Se você não especificar um valor para a propriedade `Mandatory`, o PowerShell considerará o parâmetro de entrada opcional por padrão. |
| Valor padrão |Opcional. Um valor que será usado para o parâmetro se nenhum valor de entrada for transmitido quando o runbook for iniciado. O runbook pode definir um valor padrão para qualquer parâmetro. |

O Windows PowerShell permite mais atributos de parâmetros de entrada do que aqueles listados aqui, como validação, aliases e conjuntos de parâmetros. No entanto, a Automação do Azure atualmente permite apenas as propriedades de parâmetro de entrada listadas.

Como exemplo, vamos examinar uma definição de parâmetro em um runbook de Fluxo de Trabalho do PowerShell. Essa definição tem o seguinte formato geral, em que vários parâmetros são separados por vírgulas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Agora, vamos configurar os parâmetros de entrada para um runbook do Fluxo de Trabalho do PowerShell que gera detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs em um grupo de recursos. Esse runbook tem dois parâmetros, conforme mostrado na captura de tela a seguir: o nome da máquina virtual (`VMName`) e o nome do grupo de recursos (`resourceGroupName`).

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nessa definição de parâmetro, os parâmetros de entrada são parâmetros simples do tipo cadeia de caracteres.

Observe que o PowerShell e os runbooks do Fluxo de Trabalho do PowerShell são compatíveis com todos os tipos simples e complexos, como `Object` ou `PSCredential` para parâmetros de entrada. Se o runbook tiver um parâmetro de entrada de tipo objeto, será preciso usar uma tabela de hash do PowerShell com pares nome-valor para passar um valor. Por exemplo, se você tiver o parâmetro a seguir em um runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Nesse caso, você poderá passar o valor a seguir para o parâmetro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando você não passa um valor para um parâmetro de cadeia de caracteres opcional com um valor padrão nulo, o valor do parâmetro é uma cadeia de caracteres vazia em vez de Nulo.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Como configurar parâmetros de entrada em runbooks gráficos

Para ilustrar a configuração de parâmetros de entrada para um runbook gráfico, vamos criar um runbook que gere detalhes sobre máquinas virtuais, uma única VM ou todas as VMs em um grupo de recursos. Para obter detalhes, veja [Meu primeiro runbook gráfico](./learn/automation-tutorial-runbook-graphical.md).

Um runbook gráfico usa essas atividades principais de runbook:

* Configuração da conta Executar como do Azure para autenticar com o Azure. 
* Definição de um cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) para obter as propriedades da VM.
* O uso da atividade [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) para gerar os nomes de VM. 

A atividade `Get-AzVM` define duas entradas, o nome da VM e o nome do grupo de recursos. Como esses nomes podem ser diferentes cada vez que o runbook é iniciado, é preciso adicionar parâmetros de entrada ao seu runbook para aceitar essas entradas. Veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

Siga estas etapas para configurar os parâmetros de entrada.

1. Escolha o runbook gráfico na página Runbooks e então clique em **Editar**.
2. No editor gráfico, clique no botão **Entrada e Saída** e em **Adicionar Entrada** para abrir o painel Parâmetro de Entrada do Runbook.

   ![Runbook gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. O controle Entrada e Saída exibe uma lista de parâmetros de entrada que são definidos para o runbook. Aqui, é possível adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro para o runbook, clique em **Adicionar Entrada** para abrir a folha **Parâmetro** de Entrada do Runbook, em que é possível configurar parâmetros usando as propriedades definidas em [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

    ![Como adicionar nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as propriedades a seguir a serem usadas pela atividade `Get-AzVM` e clique em **OK**.

   * Parâmetro 1:
        * **Nome** -- **VMName**
        * **Tipo** -- String
        * **Obrigatório** -- **Não**

   * Parâmetro 2:
        * **Nome** -- **resourceGroupName**
        * **Tipo** -- String
        * **Obrigatório** -- **Não**
        * **Valor padrão** -- **Personalizado**
        * Valor padrão personalizado: nome do grupo de recursos que contém as VMs

5. Veja os parâmetros no controle Entrada e Saída. 
6. Clique em **OK** novamente e em **Salvar**.
7. Clique em **Publicar** para publicar o runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em runbooks Python

Ao contrário do PowerShell, do Fluxo de Trabalho do PowerShell e dos runbooks gráficos, os runbooks Python não aceitam parâmetros nomeados. O editor de runbook analisa todos os parâmetros de entrada como uma matriz de valores de argumento. A matriz pode ser acessada importando o módulo `sys` para o script Python e usando a matriz `sys.argv`. É importante observar que o primeiro elemento da matriz, `sys.argv[0]`, é o nome do script. Portanto, o primeiro parâmetro de entrada real é `sys.argv[1]`.

Para obter um exemplo de como usar parâmetros de entrada em um runbook Python, consulte [Meu primeiro runbook Python na Automação do Azure](./learn/automation-tutorial-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Como atribuir valores a parâmetros de entrada em runbooks

Esta seção descreve várias maneiras de passar valores para parâmetros de entrada em runbooks. É possível atribuir valores de parâmetro quando você:

* [Iniciar um runbook](#start-a-runbook-and-assign-parameters)
* [Testar um runbook](#test-a-runbook-and-assign-parameters)
* [Vincular um agendamento ao runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Criar um webhook para o runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de diversas maneiras: por meio do portal do Azure, com um webhook, com cmdlets do PowerShell, com a API REST ou com o SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Iniciar um runbook publicado usando o portal do Azure e atribuir parâmetros

Quando você [inicia o runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) no portal do Azure, a folha **Iniciar Runbook** é aberta, e é possível inserir valores para os parâmetros criados.

![Como começar a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

No rótulo abaixo da caixa de entrada, é possível ver as propriedades que foram configuradas para definir atributos de parâmetro, por exemplo, obrigatório ou opcional, tipo, valor padrão. O balão de ajuda ao lado do nome do parâmetro também define as principais informações necessárias para tomar decisões sobre valores de entrada de parâmetro. 

> [!NOTE]
> Parâmetros de cadeia de caracteres são compatíveis com valores vazios do tipo Cadeia de caracteres. A inserção de `[EmptyString]` na caixa do parâmetro de entrada passa uma cadeia de caracteres vazia para o parâmetro. Além disso, os parâmetros de cadeia de caracteres não permitem Nulo. Se você não passar um valor para o parâmetro Cadeia de caracteres, o PowerShell o interpretará como Nulo.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Iniciar um runbook publicado usando cmdlets do PowerShell e atribuir parâmetros

* **Cmdlets do Azure Resource Manager:** É possível iniciar um runbook de Automação criado em um grupo de recursos usando [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets do modelo de implantação clássico do Azure:** Você pode iniciar um runbook de automação criado em um grupo de recursos padrão usando [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Quando você inicia um runbook usando cmdlets do PowerShell, um parâmetro padrão, `MicrosoftApplicationManagementStartedBy`, é criado com o valor `PowerShell`. Você pode exibir esse parâmetro no painel Detalhes do Trabalho.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Iniciar um runbook usando o SDK e atribuir parâmetros

* **Método do Azure Resource Manager:** É possível iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Método do modelo de implantação clássico do Azure:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Para iniciar esse método, crie um dicionário para armazenar os parâmetros de runbook `VMName` e `resourceGroupName` e seus valores. Em seguida, inicie o runbook. Abaixo está o snippet de código em C# para chamar o método definido acima.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Iniciar um runbook usando a API REST e atribuir parâmetros

Você pode criar e iniciar um trabalho de runbook com a API REST da Automação do Azure usando o método `PUT` com o seguinte URI de solicitação: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

No URI de solicitação, substitua os seguintes parâmetros:

* `subscriptionId`: Sua ID da assinatura do Azure.  
* `resourceGroupName`: O nome do grupo de recursos para a conta de Automação.
* `automationAccountName`: O nome da conta de Automação hospedada no serviço de nuvem especificado.  
* `jobName`: O GUID do trabalho. Os GUIDs no PowerShell podem ser criados usando `[GUID]::NewGuid().ToString()*`.

Para passar parâmetros para o trabalho de runbook, use o corpo da solicitação. Ele usa as seguintes informações, fornecidas no formato JSON:

* Nome do runbook: Obrigatórios. O nome do runbook para que o trabalho seja iniciado.  
* Parâmetros do runbook: Opcional. Um dicionário da lista de parâmetros no formato (nome, valor), em que o nome deve ser do tipo Cadeia de caracteres, e o valor pode ser qualquer valor JSON válido.

Se você quiser iniciar o runbook **Get-AzureVMTextual** criado anteriormente com `VMName` e `resourceGroupName` como parâmetros, use o formato JSON a seguir para o corpo da solicitação.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Um código de status HTTP 201 será retornado se o trabalho for criado com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo da resposta, veja [Criar um trabalho de runbook usando a API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testar um runbook e atribuir parâmetros

Quando você [testar a versão de rascunho do seu runbook ](./manage-runbooks.md) usando a opção de teste, a página Teste será aberta. Use esta página para configurar valores para os parâmetros que você criou.

![Como testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Vincular um agendamento para um runbook e atribuir parâmetros

Você pode [vincular uma agenda](./shared-resources/schedules.md) ao runbook para que ele seja iniciado em um momento específico. Você atribui parâmetros de entrada ao criar a agenda. O runbook usa esses valores quando é iniciado pela agenda. Não é possível salvar a agenda até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Criar um webhook de um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar parâmetros de entrada do runbook. Não é possível salvar o webhook até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como criar o webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando você executa um runbook usando um webhook, o parâmetro de entrada predefinido `[WebhookData](automation-webhooks.md)` é enviado com os parâmetros de entrada que você definiu. 

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Passar um objeto JSON para um runbook

Isso pode ser útil para armazenar os dados que você deseja passar para um runbook em um arquivo JSON. Por exemplo, você pode criar um arquivo JSON que contém todos os parâmetros que você deseja passar para um runbook. Para fazer isso, você precisa converter o código JSON para uma cadeia de caracteres e, em seguida, converter a cadeia de caracteres em um objeto do PowerShell antes de passá-lo para o runbook.

Esta seção usa um exemplo em que um script do PowerShell chama [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) para iniciar um runbook do PowerShell, passando o conteúdo do arquivo JSON para o runbook. O runbook do PowerShell inicia uma VM do Azure recuperando os parâmetros para a VM do objeto JSON.

### <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o código a seguir em um arquivo de texto e salve-o como **test.json** em algum lugar no seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook do PowerShell chamado **Test-Json** na Automação do Azure. Veja [Meu primeiro runbook do PowerShell](./learn/automation-tutorial-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve utilizar um objeto como um parâmetro de entrada. O runbook pode, então, usar as propriedades definidas no arquivo JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Salve e publique este runbook na sua conta de Automação.

### <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora você pode chamar o runbook de seu computador local usando o Azure PowerShell. 

1. Entre no Azure, conforme mostrado. Depois, você recebe uma solicitação para inserir suas credenciais do Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Observe que esses aliases não estão disponíveis para runbooks gráficos. Um runbook gráfico só pode usar a própria `Connect-AzAccount`.

1. Obtenha o conteúdo do arquivo JSON salvo e converta-o em uma cadeia de caracteres. `JsonPath` indica o caminho em que você salvou o arquivo JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converta o conteúdo da cadeia de caracteres do `$json` em um objeto do PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Crie uma tabela de hash para os parâmetros para `Start-AzAutomationRunbook`. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observe que você está definindo o valor de `Parameters` para o objeto do PowerShell que contém os valores do arquivo JSON.
1. Iniciar o runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Próximas etapas

* Para preparar um runbook textual, veja [Editar runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md).
* Para preparar um runbook gráfico, confira [Criar runbooks gráficos na Automação do Azure](automation-graphical-authoring-intro.md).
