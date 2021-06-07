---
title: Testar uma imagem de máquina virtual do Azure para o Azure Marketplace
description: Saiba como testar e enviar uma oferta de máquina virtual do Azure no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 467b7d605b57c479d84fc995b4e0dc53b3ac5275
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558288"
---
# <a name="test-a-virtual-machine-image"></a>Testar uma imagem de máquina virtual

Este tópico abordará as etapas para testar uma imagem de máquina virtual (VM) para implantação no Azure Marketplace.

## <a name="deploy-an-azure-vm"></a>Implantar uma VM do Azure

Para implantar uma VM da imagem da Galeria de imagens compartilhada:

1. Navegue até a versão da imagem da Galeria de imagens compartilhadas
1. Clique em criar VM
1. Forneça um nome de máquina virtual e selecione um tamanho de VM
1. Clique em Revisar + Criar. Depois que a validação for aprovada, clique em criar

> [!NOTE]
> Se você precisar criar uma VM de um arquivo VHD, siga as instruções nos artigos a seguir, [Prepare um modelo de Azure Resource Manager](#connect-the-certification-tool-to-a-vm-image) ou [implante uma VM do Azure usando o PowerShell](#how-to-use-powershell-to-consume-the-self-test-api).

Este artigo descreve como testar e enviar uma imagem de VM (máquina virtual) no Marketplace comercial para garantir que ela atenda aos requisitos mais recentes de publicação do Azure Marketplace.

Conclua estas etapas antes de enviar sua oferta de VM:

- Implantar uma VM do Azure usando sua imagem generalizada; consulte [criar uma máquina virtual usando uma base aprovada](azure-vm-create-using-approved-base.md) ou [criar uma máquina virtual usando sua própria imagem](azure-vm-create-using-own-image.md).
- Execute as validações.

## <a name="run-validations"></a>Executar as validações

Há duas maneiras de executar validações na imagem implantada.

### <a name="use-certification-test-tool-for-azure-certified"></a>Use a Ferramenta do teste de certificação para o Certificado Azure

#### <a name="download-and-run-the-certification-test-tool"></a>Baixar e executar a ferramenta de teste de certificação

A Ferramenta de Teste de Certificação para Certificado do Azure executa em uma máquina Windows local, mas testa uma VM do Linux ou do Windows baseado no Azure. Ela verifica se a imagem da VM do usuário pode ser usada com o Microsoft Azure, ou seja, se as diretrizes e os requisitos de preparação do VHD foram cumpridos.

1. Baixe e instale a [Ferramenta de Teste de Certificação para Certificado do Azure](https://www.microsoft.com/download/details.aspx?id=44299) mais recente.
2. Abra a ferramenta de certificação e, em seguida, clique em **Iniciar Novo Teste**.
3. Na tela Informações do Teste, insira um **Nome de Teste** para a execução de teste.
4. Selecione a plataforma para sua VM, **Windows Server** ou **Linux**. A escolha da plataforma impactará nas opções restantes.
5. Se a VM estiver usando esse serviço de banco de dados, marque a caixa de seleção **Testar para Banco de Dados SQL do Azure**.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Conectar a ferramenta de certificação a uma imagem da VM

1. Selecione o modo Autenticação SSH: Autenticação de senha ou de arquivo de chave.
2. Se estiver usando a autenticação baseada em senha, insira valores para o **nome DNS da VM**, o **nome de usuário** e a **senha**. É possível alterar o número da Porta SSH padrão.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Mostra a seleção de informações de teste da VM.":::

3. Se estiver usando a autenticação baseada em arquivo, insira valores para o Nome DNS da VM, Nome de usuário e local da Chave Privada. Você também pode incluir uma frase secreta ou alterar o número da Porta SSH.
4. Insira o nome DNS totalmente qualificado da VM (por exemplo, MyVMName.Cloudapp.net).
5. Insira o **nome de usuário** e a **senha**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Mostra a seleção de nome de usuário e senha da VM.":::

6. Selecione **Avançar**.

#### <a name="run-a-certification-test"></a>Executar um teste de certificação

Depois de obter os valores de parâmetro para sua imagem de VM na ferramenta de certificação, selecione testar conexão para criar uma conexão válida com sua VM. Após verificar a conexão, selecione **Avançar** para iniciar o teste. Quando o teste é concluído, os resultados são exibidos em uma tabela. A coluna status mostra (aprovação/falha/aviso) para cada teste. Se algum dos testes falhar, a imagem não será certificada. Nesse caso, revise os requisitos e as mensagens de falha, faça as alterações sugeridas e execute novamente o teste.

Após a conclusão do teste automatizado, forneça informações adicionais sobre a imagem da VM nas duas guias da tela Questionário, Avaliação geral e Personalização do kernel e, em seguida, selecione Próximo.

A última tela permite que você forneça mais informações, como informações de acesso SSH para uma imagem de VM do Linux e uma explicação para quaisquer avaliações com falha se você estiver procurando por exceções.

Por fim, selecione Gerar Relatório para baixar os resultados do teste e os arquivos de log dos casos de teste juntamente com as respostas ao questionário.
> [!Note]
> Alguns Publicadores têm cenários em que as VMs precisam ser bloqueadas, pois têm software como firewalls instalados na VM. Nesse caso, baixe a [ferramenta de teste certificada](https://aka.ms/AzureCertificationTestTool) aqui e envie o relatório no [suporte](https://aka.ms/marketplacepublishersupport)do Partner Center.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Como usar o PowerShell para consumir a API de Self-Test

### <a name="on-linux-os"></a>No SO Linux

Chame a API no PowerShell:

1. Use o comando Invoke-WebRequest para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código e na captura de tela a seguir.
3. Especifique parâmetros de corpo no formato JSON.

Este exemplo a seguir mostra uma chamada do PowerShell para a API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Aqui está um exemplo de como chamar a API no PowerShell:

[![Exemplo de tela para chamar a API no PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Usando o exemplo anterior, você poderá recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Esta tela de exemplo, que mostra `$res.Content` , mostra detalhes dos resultados do teste no formato JSON:

[![Exemplo de tela para chamar a API no PowerShell com detalhes de resultados de teste.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Aqui está um exemplo de resultados de teste JSON exibidos em um visualizador JSON online (como [código Beautify](https://codebeautify.org/jsonviewer) ou [Visualizador JSON](https://jsonformatter.org/json-viewer)).

![Mais resultados de teste em um visualizador JSON online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>No sistema operacional Windows

Chame a API no PowerShell:

1. Use o comando Invoke-WebRequest para chamar a API.
2. O método é post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código e na tela de exemplo a seguir.
3. Crie os parâmetros de corpo no formato JSON.

Este exemplo de código mostra uma chamada do PowerShell para a API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Essas telas de exemplo mostram o exemplo para chamar a API no PowerShell:

**Com chave SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Chamar a API no PowerShell com uma chave SSH.":::

**Com a senha**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Chamar a API no PowerShell com uma senha.":::

<br>Usando o exemplo anterior, você poderá recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Essa tela, que mostra `$res.Content` , mostra os detalhes dos resultados do teste no formato JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Detalhes dos resultados de teste no formato JSON.":::

<br>Aqui está um exemplo de resultados de teste exibidos em um visualizador JSON online (como [código Beautify](https://codebeautify.org/jsonviewer) ou [Visualizador JSON](https://jsonformatter.org/json-viewer)).

![Resultados de teste em um visualizador JSON online.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Como usar a rotação para consumir a API de Self-Test no sistema operacional Linux

Neste exemplo, a ondulação será usada para fazer uma chamada à API POST para Azure Active Directory e a VM Self-Host.

1. Solicitar um token do Azure AD para autenticar na VM de hospedagem interna

   Certifique-se de que os valores corretos sejam substituídos na solicitação de ondulação.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Aqui está um exemplo da resposta da solicitação:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Enviar uma solicitação para a VM de teste automático

   Verifique se o token de portador e os parâmetros são substituídos pelos valores corretos.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Exemplo de resposta da chamada à API de VM de autoteste

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Próximas etapas

- Entre no [Partner Center](https://partner.microsoft.com/).
