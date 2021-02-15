---
title: Implantar conteúdo usando FTP/S
description: Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP ou FTPS. Melhorar a segurança do site desabilitando o FTP não criptografado.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: cfec5ec5f14afc8c4eba5c21c5904687c9b187cc
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209246"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S

Este artigo mostra como usar o FTP ou FTPS para implantar seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API [o serviço de aplicativo do Azure](./overview.md).

O ponto de extremidade FTP/S para seu aplicativo já está ativo. Nenhuma configuração é necessária para habilitar a implantação de FTP/S.

## <a name="open-ftp-dashboard"></a>Abrir o painel FTP

1. Na [portal do Azure](https://portal.azure.com), procure e selecione serviços de **aplicativos**.

    ![Pesquise serviços de aplicativos.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Selecione o aplicativo Web que você deseja implantar.

    ![Selecione seu aplicativo.](media/app-service-continuous-deployment/select-your-app.png)

3. Selecione **central de implantação**  >    >  **painel** de FTP.

    ![Abrir o painel FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Obter informações de conexão de FTP

No painel de FTP, selecione **copiar** para copiar o ponto de extremidade de FTPS e as credenciais do aplicativo.

![Copiar informações de FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

É recomendável que você use as **Credenciais do Aplicativo** para implantar seu aplicativo porque ele é exclusivo para cada aplicativo. No entanto, se você clicar em **Credenciais do Usuário**, poderá definir as credenciais de nível de usuário a serem usadas para logon do FTP/S em todos os aplicativos do Serviço de Aplicativo em sua assinatura.

> [!NOTE]
> A autenticação em um ponto de extremidade de FTP/FTPS usando credenciais de nível de usuário requer um nome de usuário no seguinte formato: 
>
>`<app-name>\<user-name>`
>
> Como as credenciais de usuário estão vinculadas ao usuário e não a um recurso específico, o nome de usuário deve estar neste formato para direcionar a ação de entrada para o ponto de extremidade correto do aplicativo.
>

## <a name="deploy-files-to-azure"></a>Implantar arquivos no Azure

1. Do seu cliente de FTP (por exemplo, [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) ou [WinSCP](https://winscp.net/index.php)), use as informações de conexão que você reuniu para se conectar ao seu aplicativo.
2. Copie seus arquivos e a respectiva estrutura de diretórios para o diretório [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o diretório **/site/wwwroot/App_Data/Jobs/** para Trabalhos Web).
3. Navegue até a URL do aplicativo para verificar se ele está sendo executado corretamente. 

> [!NOTE] 
> Ao contrário de [implantações com base em Git](deploy-local-git.md), implantação de FTP não oferece suporte as automações de implantação a seguir: 
>
> - restauração de dependência (como automações NuGet, NPM, PIP e de criador)
> - compilação de binários do .NET
> - geração de Web. config (eis uma [Node. js exemplo](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Gere esses arquivos necessários manualmente no computador local e implante-os junto com seu aplicativo.
>

## <a name="enforce-ftps"></a>Impor o FTPS

Para aumentar a segurança, você deve permitir FTP somente em TLS/SSL. Você também pode desabilitar FTP e FTPS se não usa a implantação de FTP.

Na página de recursos do aplicativo no [portal do Azure](https://portal.azure.com), selecione   >  **configurações gerais** de configuração no painel de navegação esquerdo.

Para desabilitar o FTP não criptografado, selecione **FTPS somente** no **estado do FTP**. Para desabilitar totalmente o FTP e a FTPS, selecione **desabilitado**. Ao terminar, clique em **Salvar**. Se estiver usando **apenas FTPS**, você deve impor o TLS 1,2 ou superior navegando até a folha **configurações de TLS/SSL** do seu aplicativo Web. Não há suporte para TLS 1.0 e 1.1 com **FTPS Apenas**.

![Desabilitar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Para implantação de FTP usando a [CLI do Azure](/cli/azure), veja [Criar um aplicativo Web e implantar os arquivos com FTP (CLI do Azure)](./scripts/cli-deploy-ftp.md).

Para implantação de FTP usando o [Azure PowerShell](/cli/azure), veja [Carregar arquivos para um aplicativo Web usando FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Solucionar problemas de implantação de FTP

- [Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S](#deploy-your-app-to-azure-app-service-using-ftps)
  - [Abrir o painel FTP](#open-ftp-dashboard)
  - [Obter informações de conexão de FTP](#get-ftp-connection-information)
  - [Implantar arquivos no Azure](#deploy-files-to-azure)
  - [Impor o FTPS](#enforce-ftps)
  - [Automatizar com scripts](#automate-with-scripts)
  - [Solucionar problemas de implantação de FTP](#troubleshoot-ftp-deployment)
    - [Como soluciono problemas de implantação de FTP?](#how-can-i-troubleshoot-ftp-deployment)
    - [Não consigo fazer o FTP e publicar meu código. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
    - [Como faço para me conectar ao FTP no Serviço de Aplicativo do Azure por meio do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)
  - [Próximas etapas](#next-steps)
  - [Mais recursos](#more-resources)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como soluciono problemas de implantação de FTP?

A primeira etapa para solucionar problemas de implantação de FTP é isolar um problema de implantação de um problema de aplicativo de runtime.

Um problema de implantação normalmente resulta em nenhum arquivo ou arquivos incorretos implantados em seu aplicativo. Você pode resolver isso investigando a implantação de FTP ou selecionando um caminho de implantação alternativo (por exemplo, controle do código-fonte).

Um problema de aplicativo de runtime normalmente resulta no conjunto de arquivos correto implantado no seu aplicativo, mas em comportamento incorreto do aplicativo. Você pode resolver isso concentrando-se no comportamento do código no runtime e investigando os caminhos de falha específicos.

Para determinar um problema de implantação ou de runtime, consulte [Problemas de implantação versus problemas de runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Não consigo usar FTP e publicar meu código. Como resolvo esse problema?
Verifique se você inseriu corretamente o nome de host e as [credenciais](#open-ftp-dashboard). Verifique também se as seguintes portas de FTP no seu computador não estão bloqueadas por um firewall:

- Porta de conexão de controle FTP: 21, 990
- Porta de conexão de dados FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como faço para me conectar ao FTP no Serviço de Aplicativo do Azure por meio do modo passivo?
O Serviço de Aplicativo do Azure é compatível com a conexão por modo Ativo e Passivo. O modo Passivo é preferencial porque seus computadores de implantação geralmente estão atrás de um firewall (no sistema operacional ou como parte de uma rede residencial ou comercial). Veja um [exemplo na documentação do WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Próximas etapas

Para cenários mais avançados de implantação, tente [implantação no Azure com Git](deploy-local-git.md). Implantação baseada em Git no Azure permite o controle de versão, restauração do pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Credenciais de implantação do Serviço de Aplicativo do Azure](deploy-configure-credentials.md)