---
title: Implantar conteúdo usando FTP/S
description: Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP ou FTPS. Melhorar a segurança do site desabilitando o FTP não criptografado.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045795"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S

Este artigo mostra como usar o FTP ou FTPS para implantar seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API [o serviço de aplicativo do Azure](./overview.md).

O ponto de extremidade FTP/S para seu aplicativo já está ativo. Nenhuma configuração é necessária para habilitar a implantação de FTP/S.

> [!NOTE]
> A página do **centro de desenvolvimento (clássico)** na portal do Azure, que é a antiga experiência de implantação, será preterida em março de 2021. Essa alteração não afetará nenhuma configuração de implantação existente em seu aplicativo e você poderá continuar a gerenciar a implantação do aplicativo na página do **centro de implantação** .

## <a name="get-deployment-credentials"></a>Obter credenciais de implantação

1. Siga as instruções em [configurar credenciais de implantação para Azure app serviço](deploy-configure-credentials.md) para copiar as credenciais de escopo de aplicativo ou definir as credenciais de escopo do usuário. Você pode se conectar ao ponto de extremidade FTP/S do seu aplicativo usando qualquer uma das credenciais.

1. Crie o nome de usuário FTP no seguinte formato, dependendo de sua escolha de escopo de credencial:

    | Escopo do aplicativo | Escopo do usuário |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    No serviço de aplicativo, o ponto de extremidade FTP/S é compartilhado entre aplicativos. Como as credenciais de escopo do usuário não estão vinculadas a um recurso específico, você precisa preceder o nome de usuário do escopo do User com o Name do aplicativo, como mostrado acima.

## <a name="get-ftps-endpoint"></a>Obter ponto de extremidade FTP/S
    
# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Na mesma página de gerenciamento para seu aplicativo em que você copiou as credenciais de implantação (credenciais de FTP da **central de implantação**  >  ), copie o **ponto de extremidade de FTPS**.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Execute o comando [AZ webapp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . O exemplo a seguir usa um [caminho JMES](https://jmespath.org/) para extrair os pontos de extremidade de FTP/S da saída.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Cada aplicativo tem dois pontos de extremidade FTP/S, um é de leitura/gravação, enquanto o outro é somente leitura ( `profileName` contém `ReadOnly` ) e é para cenários de recuperação de dados. Para implantar arquivos com o FTP, copie a URL do ponto de extremidade de leitura/gravação.

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Execute o comando [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . O exemplo a seguir extrai o ponto de extremidade de FTP/S da saída XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Implantar arquivos no Azure

1. Do seu cliente de FTP (por exemplo, [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) ou [WinSCP](https://winscp.net/index.php)), use as informações de conexão que você reuniu para se conectar ao seu aplicativo.
2. Copie seus arquivos e a respectiva estrutura de diretórios para o diretório [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o diretório **/site/wwwroot/App_Data/Jobs/** para Trabalhos Web).
3. Navegue até a URL do aplicativo para verificar se ele está sendo executado corretamente. 

> [!NOTE] 
> Diferentemente das [implantações baseadas em git](deploy-local-git.md) e da [implantação de zip](deploy-zip.md), a implantação de FTP não dá suporte à automação de compilação, como: 
>
> - restauração de dependência (como automações NuGet, NPM, PIP e de criador)
> - compilação de binários do .NET
> - geração de Web. config (eis uma [Node. js exemplo](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Gere esses arquivos necessários manualmente no computador local e implante-os junto com seu aplicativo.
>

## <a name="enforce-ftps"></a>Impor o FTPS

Para aumentar a segurança, você deve permitir FTP somente em TLS/SSL. Você também pode desabilitar FTP e FTPS se não usa a implantação de FTP.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Na página de recursos do aplicativo no [portal do Azure](https://portal.azure.com), selecione   >  **configurações gerais** de configuração no painel de navegação esquerdo.

2. Para desabilitar o FTP não criptografado, selecione **FTPS somente** no **estado do FTP**. Para desabilitar totalmente o FTP e a FTPS, selecione **desabilitado**. Ao terminar, clique em **Salvar**. Se estiver usando **apenas FTPS**, você deve impor o TLS 1,2 ou superior navegando até a folha **configurações de TLS/SSL** do seu aplicativo Web. Não há suporte para TLS 1.0 e 1.1 com **FTPS Apenas**.

    ![Desabilitar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Execute o comando [AZ webapp config Set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) com o `--ftps-state` argumento.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Os valores possíveis para `--ftps-state` são `AllAllowed` (FTP e FTPS habilitado), `Disabled` (FTP e FTPS desabilitados) e `FtpsOnly` (somente FTPS).

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Execute o comando [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) com o `-FtpsState` parâmetro.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Os valores possíveis para `--ftps-state` são `AllAllowed` (FTP e FTPS habilitado), `Disabled` (FTP e FTPS desabilitados) e `FtpsOnly` (somente FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Solucionar problemas de implantação de FTP

- [Como soluciono problemas de implantação de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Não consigo fazer o FTP e publicar meu código. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Como faço para me conectar ao FTP no Serviço de Aplicativo do Azure por meio do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como soluciono problemas de implantação de FTP?

A primeira etapa para solucionar problemas de implantação de FTP é isolar um problema de implantação de um problema de aplicativo de runtime.

Um problema de implantação normalmente resulta em nenhum arquivo ou arquivos incorretos implantados em seu aplicativo. Você pode resolver isso investigando a implantação de FTP ou selecionando um caminho de implantação alternativo (por exemplo, controle do código-fonte).

Um problema de aplicativo de runtime normalmente resulta no conjunto de arquivos correto implantado no seu aplicativo, mas em comportamento incorreto do aplicativo. Você pode resolver isso concentrando-se no comportamento do código no runtime e investigando os caminhos de falha específicos.

Para determinar um problema de implantação ou de runtime, consulte [Problemas de implantação versus problemas de runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Não consigo usar FTP e publicar meu código. Como resolvo esse problema?
Verifique se você inseriu o [nome de host](#get-ftps-endpoint) e [as credenciais](#get-deployment-credentials)corretos. Verifique também se as seguintes portas de FTP no seu computador não estão bloqueadas por um firewall:

- Porta de conexão de controle FTP: 21, 990
- Porta de conexão de dados FTP: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como faço para me conectar ao FTP no Serviço de Aplicativo do Azure por meio do modo passivo?
O Serviço de Aplicativo do Azure é compatível com a conexão por modo Ativo e Passivo. O modo Passivo é preferencial porque seus computadores de implantação geralmente estão atrás de um firewall (no sistema operacional ou como parte de uma rede residencial ou comercial). Veja um [exemplo na documentação do WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Mais recursos

* [Implantação do Git local no Serviço de Aplicativo do Azure](deploy-local-git.md)
* [Credenciais de implantação do Serviço de Aplicativo do Azure](deploy-configure-credentials.md)
* [Exemplo: criar um aplicativo Web e implantar arquivos com FTP (CLI do Azure)](./scripts/cli-deploy-ftp.md).
* [Exemplo: carregar arquivos em um aplicativo Web usando o FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).
