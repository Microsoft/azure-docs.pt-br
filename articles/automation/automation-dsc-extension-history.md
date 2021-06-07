---
title: Trabalhar com o histórico de versão da extensão Desired State Configuration do Azure
description: Este artigo compartilha informações de histórico de versão para a extensão de configuração de estado desejado (DSC) no Azure.
ms.date: 02/17/2021
keywords: dsc, powershell, azure, extensão
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651795"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Trabalhar com o histórico de versão da extensão Desired State Configuration do Azure

A [extensão](../virtual-machines/extensions/dsc-overview.md) de VM da configuração de estado desejado (DSC) do Azure é atualizada conforme necessário para dar suporte a aprimoramentos e novos recursos fornecidos pelo Azure, pelo Windows Server e pelo WMF (Windows Management Framework) que inclui o Windows PowerShell.

Este artigo fornece informações sobre cada versão da extensão de VM da DSC do Azure, em quais ambientes ele dá suporte, e comentários e comentários sobre novos recursos ou alterações.

## <a name="latest-version"></a>Última versão

### <a name="version-283"></a>Versão 2,83

- **Data de lançamento:**
  - Fevereiro de 2021
- **Suporte a SO:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Client 7/8.1/10
  - Nano Server
- **Suporte a WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Atualização do WMF 4.0
  - WMF 4.0
- **Ambiente:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Comentários:** Esta versão inclui uma correção para binários não assinados com a extensão de VM do Windows.

### <a name="version-280"></a>Versão 2,80

- **Data de lançamento:**
  - 26 de setembro de setembro de 2019 (Azure) | 6 de julho de 2020 (Azure China vianet 21) | 20 de julho de 2020 (Azure governamental)
- **Suporte a SO:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Client 7/8.1/10
  - Nano Server
- **Suporte a WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Atualização do WMF 4.0
  - WMF 4.0
- **Ambiente:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Comentários:** Não há novos recursos incluídos nesta versão.

### <a name="version-276"></a>Versão 2.76

- **Data de lançamento:**
  - 9 de maio de 2018 (Azure) | 21 de junho de 2018 (Azure China Vianet 21, Azure Governamental)
- **Suporte a SO:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Client 7/8.1/10
  - Nano Server
- **Suporte a WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Atualização do WMF 4.0
  - WMF 4.0
- **Ambiente:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Melhoria nos metadados de extensão de substatus e outras correções de bugs secundárias.

## <a name="supported-versions"></a>Versões com suporte

> [!WARNING]
> As versões de 2,4 a 2,13 usam a visualização pública do WMF 5,0, cujos certificados de autenticação expiraram em 2016 de agosto.
> Para obter mais informações sobre esse problema, consulte o seguinte [artigo de blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Versão 2.75

- **Data de lançamento:** 5 de março de 2018
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Após a imposição de TLS 1,2 do GitHub, você não pode integrar uma VM à configuração de estado da automação do Azure usando modelos do DIY Resource Manager disponíveis no Azure Marketplace ou usar a extensão de DSC para recuperar quaisquer configurações hospedadas no GitHub. Um erro semelhante ao seguinte durante a implantação da extensão é retornado:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - A nova versão da extensão, o TLS 1.2 agora é imposto. Ao implantar a extensão, se você já especificou `AutoUpgradeMinorVersion = true` no modelo do Resource Manager, a extensão será autoatualizada para 2,75. Para obter atualizações manuais, especifique `TypeHandlerVersion = 2.75` em seu modelo do Resource Manager.

### <a name="version-270---272"></a>Versão 2.70 – 2.72

- **Data de lançamento:** 13 de novembro de 2017
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Correções de bugs & aprimoramentos que simplificam o uso da configuração de estado da automação do Azure no portal e com um modelo do Resource Manager. Para obter mais informações, consulte [script de configuração padrão](../virtual-machines/extensions/dsc-overview.md) na documentação da extensão de DSC.

### <a name="version-226"></a>Versão 2.26

- **Data de lançamento:** 9 de junho de 2017
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Melhorias de telemetria.

### <a name="version-225"></a>Versão 2.25

- **Data de lançamento:** 2 de junho de 2017
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Várias correções de bug e outras pequenas melhorias foram adicionadas.

### <a name="version-224"></a>Versão 2.24

- **Data de lançamento:** 13 de abril de 2017
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Expõe a ID do Agente do DSC e a UUID da VM como metadados de extensão. Outros aprimoramentos secundários foram adicionados.

### <a name="version-223"></a>Versão 2.23

- **Data de lançamento:** 15 de março de 2017
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - Correções de bugs e outros aprimoramentos foram adicionados.

### <a name="version-222"></a>Versão 2.22

- **Data de lançamento:** 8 de fevereiro de 2017
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte a WMF:** WMF 5.1, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - A extensão DSC agora dá suporte a WMF 5,1.
  - Outros aprimoramentos secundários foram adicionados.

### <a name="version-221"></a>Versão 2.21

- **Data de lançamento:** 2 de dezembro de 2016
- **Suporte a SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte a WMF:** WMF 5.1 Versão Prévia, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização). Para o Nano Server, a função de DSC é instalada na VM.
- **Novos recursos:**
  - A extensão de DSC agora está disponível no nano Server. Esta versão contém principalmente alterações de código para executar a extensão no nano Server.
  - Outros aprimoramentos secundários foram adicionados.

### <a name="version-220"></a>Versão 2.20

- **Data de lançamento:** 2 de agosto de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.1 Versão Prévia, WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - Suporte para o WMF 5.1 Versão Prévia. Quando publicada pela primeira vez, esta versão era uma atualização opcional e era preciso especificar Wmfversion = '5.1PP' em modelos do Resource Manager para instalar a versão prévia do WMF 5.1.
    Wmfversion = 'latest' ainda instala o [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Para obter mais informações sobre o WMF 5.1 Versão Prévia, consulte [este blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Outros aprimoramentos e correções secundários foram adicionados.

### <a name="version--219"></a>Versão 2.19

- **Data de lançamento:** 3 de junho de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure, Azure China Vianet 21, Azure Governamental
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - A extensão de DSC agora está disponível no Azure China vianet 21. Esta versão contém correções para executar a extensão no Azure China vianet 21.

### <a name="version-218"></a>Versão 2.18

- **Data de lançamento:** 3 de junho de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - Torne a telemetria sem bloqueio quando ocorre um erro durante o download do hotfix de telemetria (problema conhecido do DNS do Azure) ou durante a instalação.
  - Corrija o problema intermitente em que a extensão interrompe o processamento da configuração após uma reinicialização. Isso estava fazendo com que a extensão de DSC permaneça no estado ' em transição '.
  - Outros aprimoramentos e correções secundários foram adicionados.

### <a name="version-217"></a>Versão 2.17

- **Data de lançamento:** 26 de abril de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.0 RTM, Atualização do WMF 4.0, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - Suporte para a Atualização do WMF 4.0. Para obter mais informações sobre o WMF 4.0 Update, consulte [este blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Lógica de repetição em erros que ocorrem durante a instalação da extensão de DSC ou ao aplicar uma instalação de extensão de postagem de configuração DSC. Como parte dessa alteração, a extensão tentará novamente realizar a instalação se uma instalação anterior tiver falhado ou tentará aplicar novamente uma configuração de DSC que falhou anteriormente, no máximo três vezes, até que ela atinja o estado de conclusão (Êxito/Erro) ou que uma nova solicitação chegue. Se a extensão falhar devido a configurações de usuário/entrada do usuário inválidas, ela não tentará novamente. Nesse caso, a extensão deve ser invocada novamente com uma nova solicitação e configurações do usuário corretas. 

  > [!NOTE]
   > A extensão de DSC depende do agente de VM do Azure para as novas tentativas. O Agente de VM do Azure invoca a extensão com a última solicitação com falha até ela atingir um estado de êxito ou de erro.

### <a name="version-216"></a>Versão 2.16

- **Data de lançamento:** 21 de abril de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - Melhoria no tratamento de erros e outras correções de bugs secundárias.
  - Nova Propriedade nas configurações de extensão de DSC. `ForcePullAndApply` em AdvancedOptions é adicionado para habilitar a extensão DSC aplicar configurações DSC quando o modo de atualização é pull (em oposição ao modo de push padrão). Para obter mais informações sobre as configurações de extensão de DSC, consulte [este blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>Versão 2.15

- **Data de lançamento:** 14 de março de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - Na versão da extensão 2.14, foram incluídas alterações para instalar o WMF RTM. Durante a atualização de versão da extensão 2.13.2.0 para 2.14.0.0, observe que alguns cmdlets do DSC falham ou sua configuração falha com um erro – "Nenhuma instância encontrada com os valores de propriedade fornecidos". Para ibter mais informações, consulte as [notas de versão do DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). As soluções alternativas para esses problemas foram adicionadas na versão 2.15. 
  - Se você já tiver instalado a versão 2,14 e estiver executando um dos dois problemas acima, será necessário executar essas etapas manualmente. Em uma sessão do PowerShell com privilégios elevados, execute os seguintes comandos:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Versão 2.14

- **Data de lançamento:** 25 de fevereiro de 2016
- **Suporte a SO:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte a WMF:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Azure
- **Comentários:** Esta versão usa o DSC como incluído no Windows Server 2016 Technical Preview; para outros sistemas operacionais Windows, ele instala o [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (a instalação do WMF requer uma reinicialização).
- **Novos recursos:**
  - Usa o WMF RTM.
  - Habilita a coleta de dados para melhorar a qualidade da extensão de DSC. Para obter mais informações, consulte este [artigo de blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Fornece um formato de configurações atualizadas para a extensão em um modelo do Resource Manager. Para obter mais informações, consulte este [artigo de blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Correções de bug e outros aprimoramentos.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o DSC do PowerShell, consulte o [Centro de documentação do PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o [modelo do Resource Manager para a extensão de DSC](../virtual-machines/extensions/dsc-template.md).
- Para obter outras funcionalidades e recursos que você pode gerenciar com o DSC do PowerShell, procure a [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão de DSC](../virtual-machines/extensions/dsc-credentials.md).
