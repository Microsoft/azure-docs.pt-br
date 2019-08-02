---
title: Parâmetros de entrada do Runbook
description: Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks permitindo transmitir dados para um runbook quando ele é iniciado. Este artigo descreve os diferentes cenários em que os parâmetros de entrada são usados em runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fb89af0363ddc25f2bccfa1169834aa856e514a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478243"
---
# <a name="runbook-input-parameters"></a>Parâmetros de entrada do Runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks, permitindo transmitir dados para este quando ele é iniciado. Os parâmetros permitem que as ações de runbook sejam direcionadas para ambientes e cenários específicos. Neste artigo, você é orientado quanto aos diferentes cenários em que os parâmetros de entrada são usados em runbooks.

## <a name="configure-input-parameters"></a>Como configurar parâmetros de entrada

Parâmetros de entrada podem ser configurados no PowerShell, no Fluxo de Trabalho do PowerShell, em Python e em runbooks gráficos. Um runbook pode ter vários parâmetros com tipos de dados diferentes, ou não ter nenhum parâmetro. Os parâmetros de entrada podem ser obrigatório ou opcionais, e você pode ter um valor padrão para parâmetros opcionais. Você atribui valores aos parâmetros de entrada para um runbook quando o inicia por meio de um dos métodos disponíveis. Esses métodos incluem a inicialização de um runbook a partir do portal do Azure, de um serviço Web ou do PowerShell. Você também pode iniciá-lo como um runbook filho, que é chamado de embutido em outro runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurar parâmetros de entrada em runbooks do PowerShell

O PowerShell e os runbooks de Fluxo de Trabalho do PowerShell na Automação do Azure dão suporte a parâmetros de entrada definidos usando os atributos a seguir:  

| **Propriedade** | **Descrição** |
|:--- |:--- |
| `Type` |Obrigatório. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| `Name` |Obrigatório. O nome do parâmetro. Deve ser exclusivo no runbook e pode conter apenas letras, números ou caracteres de sublinhado. Deve começar com uma letra. |
| `Mandatory` |Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se você definir isso como **\$verdadeiro**, um valor deverá ser fornecido quando o runbook for iniciado. Se você definir isso como **\$falso**, um valor será opcional. |
| `Default value` |Opcional. Especifica um valor que é usado para o parâmetro se um valor não for transmitido ao iniciar o runbook. Um valor padrão pode ser definido para qualquer parâmetro e tornará automaticamente o parâmetro opcional, independentemente da configuração obrigatória. |

O Windows PowerShell dá suporte a mais atributos de parâmetros de entrada do que aqueles listados aqui, como validação, aliases e conjuntos de parâmetros. No entanto, a Automação do Azure atualmente dá suporte apenas aos parâmetros de entrada anteriores.

Uma definição de parâmetro nos runbooks de Fluxo de Trabalho do PowerShell tem o formato geral a seguir, em que vários parâmetros são separados por vírgulas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Ao definir parâmetros, se você não especificar o atributo **Mandatory** , por padrão, o parâmetro será considerado opcional. Além disso, se você definir um valor padrão para um parâmetro em runbooks de Fluxo de Trabalho do PowerShell, ele será tratado pelo PowerShell como um parâmetro opcional, independentemente do valor do atributo **Mandatory**.

Por exemplo, vamos configurar os parâmetros de entrada para um runbook do Fluxo de Trabalho do PowerShell que gera detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs em um grupo de recursos. Esse runbook tem dois parâmetros, conforme mostrado na captura de tela a seguir: o nome da máquina virtual e o nome do grupo de recursos.

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nessa definição de parâmetro, os parâmetros **\$VMName** e **\$resourceGroupName** são parâmetros simples do tipo cadeia de caracteres. No entanto, o PowerShell e os runbooks do Fluxo de Trabalho do PowerShell dão suporte a todos os tipos simples e complexos, como **objeto** ou **PSCredential** para parâmetros de entrada.

Se o runbook tiver um parâmetro de entrada de tipo objeto, use uma tabela de hash do PowerShell com pares (nome, valor) para passar um valor. Por exemplo, se você tiver o seguinte parâmetro em um runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Poderá passar o seguinte valor para o parâmetro:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando você não transmite nenhum valor a um parâmetro `[String]` opcional que tenha um _valor padrão_ de `\$null`, o valor do parâmetro será uma _sequência vazia_,  **não** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Como configurar parâmetros de entrada em runbooks gráficos

Para [configurar um runbook gráfico](automation-first-runbook-graphical.md) com parâmetros de entrada, vamos criar um runbook gráfico que gera detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs em um grupo de recursos. A configuração de um runbook consiste em duas atividades principais, conforme descrito abaixo.

[**Autenticar runbooks com uma conta Executar como do Azure**](automation-sec-configure-azure-runas-account.md) para autenticar com o Azure.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) para obter as propriedades de uma máquina virtual.

Você pode usar a atividade [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) para gerar os nomes das máquinas virtuais. A atividade **Get-AzureRmVm** aceitará dois parâmetros: o **nome da máquina virtual** e o **nome do grupo de recursos**. Uma vez que esses parâmetros exigiriam valores diferentes sempre que você iniciasse o runbook, você pode adicionar parâmetros de entrada ao runbook. Aqui estão as etapas para adicionar parâmetros de entrada:

1. Selecione o runbook gráfico na folha **Runbooks** e então clique em [**Editar**](automation-graphical-authoring-intro.md).
2. No editor de runbook, clique em **Entrada e saída** para abrir a folha **Entrada e saída**.

   ![Runbook gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A folha **Entrada e saída** exibe uma lista de parâmetros de entrada definidos para o runbook. Nessa folha, você pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro ao runbook, clique **Adicionar entrada** para abrir a folha **Parâmetro de Entrada do Runbook**. Nela, você pode configurar os seguintes parâmetros:

   | **Propriedade** | **Descrição** |
   |:--- |:--- |
   | `Name` |Obrigatório. O nome do parâmetro. Deve ser exclusivo no runbook e pode conter apenas letras, números ou caracteres de sublinhado. Deve começar com uma letra. |
   | `Description` |Opcional. Descrição da finalidade do parâmetro de entrada. |
   | `Type` |Opcional. O tipo de dados esperado para o valor do parâmetro. Os tipos de parâmetro com suporte são **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** e **Object**. Se um tipo de dados não estiver selecionado, será usado **String**por padrão. |
   | `Mandatory` |Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se você escolher **sim**, um valor deverá ser fornecido quando o runbook for iniciado. Se você escolher **não**, um valor não será necessário quando o runbook for iniciado, e um valor padrão poderá ser definido. |
   | `Default Value` |Opcional. Especifica um valor que é usado para o parâmetro se um valor não for transmitido ao iniciar o runbook. Um valor padrão pode ser definido para um parâmetro que não é obrigatório. Para definir um valor padrão, escolha **personalizado**. Esse valor será usado a menos que outro valor seja fornecido quando o runbook for iniciado. Escolha **Nenhum** se não quiser fornecer nenhum valor padrão. |

    ![Como adicionar nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as seguintes propriedades que serão usadas pela atividade **Get-AzureRmVm**:

   * **Parameter1:**
     * Nome - VMName
     * Tipo - String
     * Obrigatório - Não
   * **Parameter2:**
     * Nome - resourceGroupName
     * Tipo - String
     * Obrigatório - Não
     * Valor padrão - Personalizado
     * Valor padrão personalizado - \<Nome do grupo de recursos que contém as máquinas virtuais\>

5. Depois de adicionar os parâmetros, clique em **OK**. Agora você pode exibi-los na **página Entrada e saída**. Clique em **OK** novamente e clique em **Salvar** e **Publicar** o runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em runbooks Python

Ao contrário do PowerShell, do Fluxo de Trabalho do PowerShell e dos runbooks gráficos, os runbooks Python não aceitam parâmetros nomeados.
Todos os parâmetros de entrada são analisados como uma matriz de valores de argumento.
A matriz pode ser acessada importando o módulo `sys` para o script Python e, em seguida, usando a matriz `sys.argv`.
É importante observar que o primeiro elemento da matriz, `sys.argv[0]`, é o nome do script, portanto, o primeiro parâmetro de entrada real é `sys.argv[1]`.

Para obter um exemplo de como usar parâmetros de entrada em um runbook Python, consulte [Meu primeiro runbook Python na Automação do Azure](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Como atribuir valores a parâmetros de entrada em runbooks

Você pode passar valores para parâmetros de entrada em runbooks nos cenários a seguir:

### <a name="start-a-runbook-and-assign-parameters"></a>Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de diversas maneiras: por meio do portal do Azure, com um webhook, com cmdlets do PowerShell, com a API REST ou com o SDK. A seguir, discutiremos diferentes métodos para iniciar um runbook e atribuir parâmetros.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Como iniciar um runbook publicado usando o portal do Azure e atribuir parâmetros

Quando você [inicia o runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal), a folha **Iniciar Runbook** é aberta e é possível inserir valores para os parâmetros criados.

![Como começar a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

No rótulo abaixo da caixa de entrada, você pode ver os atributos que foram definidos para o parâmetro. Os atributos incluem obrigatório ou opcional, tipo e valor padrão. No balão de ajuda ao lado do nome de parâmetro, você pode ver todas as informações principais necessárias para tomar decisões sobre os valores de entrada de parâmetro. Essas informações incluem se um parâmetro é obrigatório ou opcional. Também incluem o tipo e o valor padrão (se houver) e outras observações úteis.

> [!NOTE]
> Parâmetros do tipo String dão suporte a valores de Cadeia de Caracteres **Vazia** .  Inserir **[EmptyString]** na caixa do parâmetro de entrada passa uma cadeia de caracteres vazia para o parâmetro. Além disso, parâmetros do tipo de cadeia de caracteres não dão suporte à passagem de valores **Nulos** . Se você não passar um valor para o parâmetro de Cadeia de Caracteres, o PowerShell o interpretará como nulo.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Como iniciar um runbook publicado usando cmdlets do PowerShell e atribuir parâmetros

* **Cmdlets do Azure Resource Manager:** Você pode iniciar um runbook de Automação criado em um grupo de recursos usando [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Exemplo:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Cmdlets do modelo de implantação clássico do Azure:** Você pode iniciar um runbook de automação criado em um grupo de recursos padrão usando [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Exemplo:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Quando você inicia um runbook usando cmdlets do PowerShell, um parâmetro padrão, **MicrosoftApplicationManagementStartedBy**, é criado com o valor **PowerShell**. Você pode exibir esse parâmetro na página **Detalhes do trabalho**.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Como uniciar um runbook usando o SDK e atribuir parâmetros

* **Método do Azure Resource Manager:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Método do modelo de implantação clássico do Azure:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Para iniciar esse método, crie um dicionário para armazenar os parâmetros de runbook, **VMName** e **resourceGroupName**, e seus valores. Em seguida, inicie o runbook. Abaixo está o snippet de código em C# para chamar o método definido acima.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Como iniciar um runbook usando a API REST e atribuir parâmetros

Um trabalho de runbook pode ser criado e iniciado com a API REST de Automação do Azure usando o método **PUT** com o URI de solicitação a seguir: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


No URI de solicitação, substitua os seguintes parâmetros:

* **subscriptionId:** Sua ID da assinatura do Azure.  
* **resourceGroupName:** O nome do grupo de recursos para a Conta de Automação.
* **automationAccountName:** O nome de sua conta de automação hospedada no serviço de nuvem especificado.  
* **jobName:** O GUID do trabalho. Os GUIDs do PowerShell pode ser criados usando o cmdlet **[GUID]::NewGuid().ToString ()** .

Para passar parâmetros para o trabalho de runbook, use o corpo da solicitação. Ele usa as duas seguintes propriedades fornecidas no formato JSON:

* **Nome do runbook:** Obrigatório. O nome do runbook para que o trabalho seja iniciado.  
* **Parâmetros do runbook:** Opcional. Um dicionário da lista de parâmetros no formato (nome, valor) em que o nome deve ser do tipo cadeia de caracteres e o valor pode ser qualquer valor JSON válido.

Se você quiser iniciar o runbook **Get-AzureVMTextual** criado anteriormente com **VMName** e **resourceGroupName** como parâmetros, use o formato JSON a seguir para o corpo da solicitação.

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

Um código de status HTTP 201 será retornado se o trabalho for criado com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo da resposta, consulte o artigo sobre como [criar um trabalho de runbook usando a API REST.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Testar um runbook e atribuir parâmetros

Quando você [testa a versão de rascunho do runbook](automation-testing-runbook.md) usando a opção de teste, é aberta a página **Teste** e você pode configurar valores para os parâmetros criados.

![Como testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Vincular um agendamento para um runbook e atribuir parâmetros

Você pode [vincular uma agenda](automation-schedules.md) ao runbook para que ele seja iniciado em um momento específico. Você atribui parâmetros de entrada ao criar a agenda. O runbook usa esses valores quando é iniciado pela agenda. Não é possível salvar a agenda até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Criar um webhook de um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar parâmetros de entrada do runbook. Não é possível salvar o webhook até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como criar o webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando você executa um runbook usando um webhook, o parâmetro de entrada predefinido **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** é enviado junto com os parâmetros de entrada que você definiu. Você pode clicar para expandir o parâmetro **WebhookData** para obter mais detalhes.

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Passar um objeto JSON para um runbook

Isso pode ser útil para armazenar os dados que você deseja passar para um runbook em um arquivo JSON.
Por exemplo, você pode criar um arquivo JSON que contém todos os parâmetros que você deseja passar para um runbook. Para fazer isso, você precisa converter o JSON para uma cadeia de caracteres e, em seguida, converter a cadeia de caracteres em um objeto do PowerShell antes de passar o seu conteúdo para o runbook.

Neste exemplo, você tem um script do PowerShell que chama [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) para iniciar um runbook do PowerShell, passando o conteúdo de JSON para o runbook.
O runbook do PowerShell inicia uma VM do Azure, obtendo os parâmetros para a VM de JSON que foi passado.

### <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o seguinte teste em um arquivo de texto e salve-o como `test.json` em algum lugar no seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook PowerShell chamado "Test-Json" na Automação do Azure.
Para saber como criar um novo runbook do PowerShell, consulte [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve utilizar um objeto como um parâmetro de entrada.

O runbook pode, então, usar as propriedades definidas no JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Salve e publique este runbook na sua conta de Automação.

### <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora você pode chamar o runbook de seu computador local usando o Azure PowerShell.
Execute os seguintes comandos do PowerShell:

1. Entrar no Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   Você recebe uma solicitação para inserir suas credenciais do Azure.

   > [!IMPORTANT]
   > **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

1. Obtenha o conteúdo do arquivo JSON e converta-o em uma cadeia de caracteres:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` é o caminho em que você salvou o arquivo JSON.

1. Converta o conteúdo da cadeia de caracteres do `$json` em um objeto do PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Crie uma tabela de hash para os parâmetros para `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observe que você está definindo o valor de `Parameters` para o objeto do PowerShell que contém os valores do arquivo JSON.
1. Iniciar o runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [Como iniciar um runbook](automation-starting-a-runbook.md).
* Para editar um runbook textual, consulte [Como editar runbooks textuais](automation-edit-textual-runbook.md).
* Para editar um runbook gráfico, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
