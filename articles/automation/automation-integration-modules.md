---
title: "Criar um Módulo de Integração da Automação do Azure | Microsoft Docs"
description: "Tutorial que explica a criação, os testes e o uso de exemplo dos módulos de integração na Automação do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: 27798efb-08b9-45d9-9b41-5ad91a3df41e
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/13/2017
ms.author: magoedte
ms.openlocfilehash: 061a9856ef4a9bc4f569b812a061fad98b044f04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-integration-modules"></a>Módulos de integração de automação do Azure
O PowerShell é a tecnologia fundamental por trás da Automação do Azure. Como a Automação do Azure foi desenvolvida sobre o PowerShell, os módulos do PowerShell são fundamentais para a extensibilidade da Automação do Azure. Neste artigo, orientaremos você pelas questões específicas da utilização de módulos do PowerShell pela Automação do Azure, chamados de “Módulos de Integração”, e as práticas recomendadas para a criação de seus próprios módulos do PowerShell para ter certeza de que eles funcionam como Módulos de Integração dentro da Automação do Azure. 

## <a name="what-is-a-powershell-module"></a>O que é um módulo do PowerShell?
Um módulo do PowerShell é um grupo de cmdlets do PowerShell, como **Get-Date** ou **Copy-Item**, que pode ser usado no console do PowerShell, em scripts, fluxos de trabalho, runbooks e em recursos de DSC do PowerShell, como WindowsFeature ou File, que podem ser usados por meio das configurações de DSC do PowerShell. Toda a funcionalidade do PowerShell é exposta por meio de cmdlets e recursos de DSC, e todo cmdlet/recurso de DSC tem o suporte de um módulo do PowerShell, muitos deles acompanham o próprio PowerShell. Por exemplo, o cmdlet **Get-Date** faz parte do módulo Microsoft.PowerShell.Utility do PowerShell, e o cmdlet **Copy-Item** faz parte do módulo Microsoft.PowerShell.Management do PowerShell, e o recurso Package DSC faz parte do módulo PSDesiredStateConfiguration do PowerShell. Esses dois módulos são fornecidos com o PowerShell. Porém, muitos módulos do PowerShell não acompanham o PowerShell, mas são distribuídos com produtos internos ou de terceiros, como o System Center 2012 Configuration Manager, ou pela vasta comunidade do PowerShell, em locais como a Galeria do PowerShell.  Os módulos são úteis porque simplificam tarefas complexas por meio de funcionalidade encapsulada.  Saiba mais sobre [Módulos do PowerShell no MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>O que é um Módulo de Integração da Automação do Azure?
Um Módulo de Integração não é muito diferente de um módulo do PowerShell. É simplesmente um módulo do PowerShell que contém, opcionalmente, um arquivo adicional: um arquivo de metadados que especifica um tipo de conexão de Automação do Azure a ser usado com os cmdlets do módulo em runbooks. Com o arquivo opcional ou não, esses módulos do PowerShell podem ser importados na Automação do Azure a fim de disponibilizar seus cmdlets para uso dentro de runbooks, e disponibilizar os recursos de DSC para utilização em configurações de DSC. Nos bastidores, a Automação do Azure armazena esses módulos, e no momento da execução do trabalho do runbook e do trabalho de compilação do DSC os carrega nas áreas restritas da Automação do Azure, onde os runbooks são executados e as configurações de DSC são compiladas.  Quaisquer recursos de DSC em módulos também são colocados automaticamente no servidor de recepção do DSC de Automação, de modo que eles possam ser obtidos por máquinas que tentarem aplicar configurações de DSC.  

Fornecemos diversos módulos do Azure PowerShell na Automação do Azure para que você possa começar a automatizar o gerenciamento do Azure imediatamente, mas você pode importar os módulos do PowerShell para qualquer sistema, serviço ou ferramenta com o qual deseja realizar a integração. 

> [!NOTE]
> Determinados módulos são fornecidos como "módulos globais" no serviço de Automação. Esses módulos globais estão disponíveis quando você cria uma conta de automação, e os atualizamos às vezes, o que os envia automaticamente à sua conta de automação. Se não quiser que eles sejam atualizados automaticamente, você sempre poderá importar o mesmo módulo por conta própria, e ele terá precedência sobre a versão de módulo global desse módulo que fornecemos no serviço. 

O formato no qual você importa um pacote do Módulo de Integração é um arquivo compactado com o mesmo nome que o módulo e com uma extensão .zip. Ele contém o módulo do Windows PowerShell e quaisquer arquivos de suporte, incluindo um arquivo de manifesto (.psd1), se o módulo tiver um.

Se o módulo precisar conter um tipo de conexão da Automação do Azure, também deverá conter um arquivo com o nome `<ModuleName>-Automation.json` que especifica as propriedades do tipo de conexão. Esse é um arquivo json colocando dentro da pasta do módulo de seu arquivo .zip compactado, e contém os campos de uma “conexão” necessária para conectar ao sistema ou serviço representado pelo módulo. Com isso, um tipo de conexão será criado na Automação do Azure. Com esse arquivo você pode definir os nomes de campo, os tipos e se os campos devem ser criptografados e/ou opcionais, para o tipo de conexão do módulo. Veja a seguir um modelo no formato de arquivo json:

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Se você tiver implantado a Service Management Automation e criado pacotes do Módulo de Integração para seus runbooks de automação, tudo isso será bastante familiar para você. 

## <a name="authoring-best-practices"></a>Práticas recomendadas de criação
Embora os módulos de integração sejam essencialmente os módulos do PowerShell, ainda há uma série de itens a considerar durante a criação de um módulo do PowerShell, para torná-lo mais útil na Automação do Azure. Algumas deles são específicas à Automação do Azure, e outras são úteis apenas para fazer seus módulos funcionarem bem no Fluxo de Trabalho do PowerShell, independentemente de você usar ou não a Automação. 

1. Incluem uma sinopse, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, você pode usar o cmdlet **Get-Help** para definir certas informações de ajuda com relação aos cmdlets para que o usuário receba ajuda sobre como usá-los. Por exemplo, veja como você pode definir uma sinopse e um URI de ajuda para um módulo do PowerShell escrito em um arquivo .psm1.<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> Fornecer essas informações não apenas mostrará esta Ajuda usando o cmdlet **Get-Help** no console do PowerShell, mas também exporá essa funcionalidade de ajuda na Automação do Azure.  Por exemplo, ao inserir atividades durante a criação de runbooks. Clicar em "Exibir ajuda detalhada" abrirá o URI de ajuda em outra guia do navegador da Web que você está usando para acessar a Automação do Azure.<br>![Ajuda do Módulo de Integração](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Se o módulo for executado em um sistema remoto,

    a. Ele deverá conter um arquivo de metadados do Módulo de Integração que define as informações necessárias para se conectar a esse sistema remoto, ou seja, tipo de conexão.  
    b. Cada cmdlet no módulo deve ser capaz de receber um objeto de conexão (uma instância desse tipo de conexão) como um parâmetro.  

    Fica mais fácil usar os cmdlets no módulo na Automação do Azure se você permitir a passagem de um objeto com os campos do tipo de conexão como um parâmetro para o cmdlet. Dessa forma, os usuários não precisam mapear parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet sempre que eles chamarem um cmdlet. Com base no exemplo de runbook acima, ele usa um ativo de conexão Twilio chamado CorpTwilio para acessar o Twilio e retornar todos os números de telefone na conta.  Você observou como ele está mapeando os campos da conexão para os parâmetros do cmdlet?<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Uma maneira mais fácil e adequada de abordar isso é passando diretamente o objeto de conexão para o cmdlet -
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    Você pode habilitar um comportamento como esse para seus cmdlets permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto de parâmetros **SpecifyConnectionFields** abaixo é usado para passar as propriedades do campo de conexão, individualmente. **UseConnectionObject** permite que você passe a conexão diretamente. Como você pode ver, o cmdlet Send-TwilioSMS no [módulo Twilio do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) permite a passagem de qualquer uma das formas: 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
3. Defina o tipo de saída para todos os cmdlets no módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de criação para ajudar você a determinar as propriedades de saída do cmdlet, para uso durante a criação. É especialmente útil durante a criação gráfica do runbook da Automação, quando o conhecimento do tempo de criação é fundamental para uma experiência de usuário facilitada com o seu módulo.<br><br> ![Tipo de saída do runbook gráfico](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Isso é semelhante à funcionalidade "preenchimento automático" da saída de um cmdlet no ISE do PowerShell sem precisar executá-lo.<br><br> ![IntelliSense POSH](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. Os cmdlets no módulo não devem usar tipos de objeto complexos como parâmetros. O Fluxo de Trabalho do PowerShell é diferente do PowerShell, pois armazena tipos complexos no formato desserializado. Tipos primitivos permanecerão primitivos, mas tipos complexos serão convertidos em suas versões desserializadas, que são essencialmente pacotes de propriedade. Por exemplo, se você tiver usado o cmdlet **Get-Process** em um runbook (ou um Fluxo de Trabalho do PowerShell para esse fim), ele retornará um objeto do tipo [Deserialized.System.Diagnostic.Process] não o tipo [System.Diagnostic.Process] esperado. Esse tipo tem as mesmas propriedades que o tipo não desserializado, mas nenhum dos métodos. Se tentar passar esse valor como um parâmetro para um cmdlet, quando o cmdlet espera um valor [System.Diagnostic.Process] para esse parâmetro, você receberá o seguinte erro: *Não é possível processar a transformação do argumento no parâmetro 'process'. Erro: "Não é possível converter o valor "System.Diagnostics.Process (CcmExec)" do tipo "Deserialized.System.Diagnostics.Process" para o tipo "System.Diagnostics.Process".*   Isso ocorre porque há uma incompatibilidade de tipo entre o tipo [System.Diagnostic.Process] esperado e o tipo [Deserialized.System.Diagnostic.Process] fornecido. A solução alternativa a esse problema é garantir que os cmdlets de seu módulo não usem tipos complexos como parâmetros. Está é a maneira errada de fazer isso.
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    Esta é a maneira certa, usando um primitivo que pode ser usado internamente pelo cmdlet para obter o objeto complexo e usá-lo. Como os cmdlets são executadas no contexto do PowerShell, não no Fluxo de Trabalho do PowerShell, dentro do cmdlet $process torna-se o tipo correto de [System.Diagnostic.Process].  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Ativos de conexão em runbooks são tabelas de hash, que são um tipo complexo. Ainda assim, parece que essas tabelas de hash podem ser passadas para os cmdlets para seu parâmetro –Connection perfeitamente, sem qualquer exceção de conversão. Tecnicamente, alguns tipos de PowerShell podem ser convertidos apropriadamente de sua forma serializada para a forma desserializada e, assim, podem ser passados para os cmdlets para os parâmetros que aceitam o tipo não desserializado. A tabela de hash é um deles. É possível implementar os tipos definidos de um autor de módulo de uma forma que possam desserializar corretamente também, mas há algumas compensações a considerar. O tipo deve ter um construtor padrão, ter todas as suas propriedades públicas e ter um PSTypeConverter. No entanto, para os tipos já definidos que não são de propriedade do autor do módulo, não é possível "corrigi-los". Por esse motivo existe a recomendação para evitar tipos complexos para parâmetros. Dica de criação de runbook: se, por alguma razão, seus cmdlets precisarem aceitar um parâmetro de tipo complexo, ou se você estiver usando o módulo de outra pessoa que exige um parâmetro de tipo complexo, a solução nos runbooks de Fluxo de Trabalho do PowerShell, e em Fluxos de Trabalho do PowerShell no PowerShell local, é encapsular o cmdlet que gera o tipo complexo e o cmdlet que consome o tipo complexo na mesma atividade InlineScript. Como a InlineScript executa seu conteúdo como PowerShell e não como um Fluxo de Trabalho do PowerShell, o cmdlet que gera o tipo complexo produziria esse tipo correto, não o tipo complexo desserializado.
5. Torne todos os cmdlets no módulo cmdlets sem estado. O Fluxo de Trabalho do PowerShell executa cada cmdlet chamado no fluxo de trabalho em uma sessão diferente. Isso significa que todos os cmdlets que dependem do estado da sessão criado/modificada por outros cmdlets no mesmo módulo não funcionarão em runbooks do Fluxo de Trabalho do PowerShell.  Veja um exemplo do que não fazer:
   
    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```
   <br>
6. O módulo deve estar totalmente contido em um pacote capacitado para Xcopy. Como os módulos da Automação do Azure são distribuídos para as áreas restritas da Automação quando os runbooks precisam ser executados, eles precisam trabalhar independentemente do host no qual estão sendo executados. Isso significa que você deve ser capaz de compactar o pacote do módulo, movê-lo para qualquer outro host com a mesma versão do PowerShell ou mais recente, e fazê-lo funcionar normalmente quando importado para o ambiente do PowerShell desse host. Para que isso aconteça, o módulo não deve depender de quaisquer arquivos fora da pasta do módulo (a pasta que é compactada ao importar na Automação do Azure), ou de qualquer configuração de registro exclusiva em um host, como aquelas definidas pela instalação de um produto. Se essa prática recomendada não for seguida, o módulo não poderá ser usado na Automação do Azure.  

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre como criar os Módulos do PowerShell, consulte [Escrevendo um Módulo do Windows PowerShell](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

