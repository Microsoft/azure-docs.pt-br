---
title: Cadência da aplicação de patch do sistema operacional e tempo de execução
description: Saiba como Azure App serviço atualiza o sistema operacional e os tempos de execução, os tempos de execução e o nível de patch que seus aplicativos têm e como você pode obter comunicados de atualização.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b52223aea0f0bdfecf58906ac192e893da3b47d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96558480"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Aplicação de patch do sistema operacional e do runtime no Serviço de Aplicativo do Azure

Este artigo mostra como obter determinadas informações de versão sobre o sistema operacional ou o software no [Serviço de Aplicativo](overview.md). 

O Serviço de Aplicativo é considerado Plataforma como Serviço, o que significa que o sistema operacional e a pilha de aplicativos são gerenciados para você pelo Azure; você apenas gerencia seu aplicativo e os dados do aplicativo. Mais controle sobre o sistema operacional e a pilha de aplicativos está disponível em [Máquinas Virtuais do Azure](../virtual-machines/index.yml). Mesmo considerando isso, é útil para você, como um usuário do Serviço de Aplicativo, saber mais informações, como:

-   Como e quando as atualizações do sistema operacional são aplicadas?
-   Como o Serviço de Aplicativo é corrigido contra vulnerabilidades significativas (como ameaça de dia zero)?
-   Quais versões do sistema operacional e do runtime estão executando seus aplicativos?

Por motivos de segurança, algumas informações específicas sobre segurança não são publicadas. No entanto, o artigo tem como alvo reduzir preocupações, aumentando a transparência sobre o processo e demonstrar como você pode permanecer atualizado sobre comunicados ou atualizações de runtime relacionados à segurança.

## <a name="how-and-when-are-os-updates-applied"></a>Como e quando as atualizações do sistema operacional são aplicadas?

O Azure gerencia a aplicação de patch do sistema operacional em dois níveis: nos servidores físicos e nas máquinas virtuais (VMs) convidadas que executam os recursos do Serviço de Aplicativo. Ambos são atualizados mensalmente, o que se alinha ao agendamento do [Patch Tuesday](/security-updates/) mensal. Essas atualizações são aplicadas automaticamente, de maneira a garantir o SLA de alta disponibilidade dos serviços do Azure. 

Para obter informações detalhadas de como as atualizações são aplicadas, consulte [Desmistificando a mágica por trás das atualizações do sistema operacional do Serviço de Aplicativo](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Como o Azure lida com vulnerabilidades significativas?

Quando vulnerabilidades graves exigem aplicação imediata de patches, como [vulnerabilidades de dia zero](https://wikipedia.org/wiki/Zero-day_(computing)), as atualizações de alta prioridade são tratadas caso a caso.

Mantenha-se atualizado com comunicados de segurança críticos no Azure visitando o [Blog de segurança do Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Quando os runtimes de linguagem compatíveis são atualizados, adicionados ou preteridos?

As novas versões estáveis dos runtimes de linguagem compatíveis (principal, secundária ou patch) são acrescentadas periodicamente às instâncias do Serviço de Aplicativo. Algumas atualizações substituem a instalação existente, enquanto outras são instaladas lado a lado com as versões existentes. Em uma instalação de substituição, seu aplicativo é executado automaticamente no runtime atualizado. Em uma instalação lado a lado, você deve migrar manualmente seu aplicativo para aproveitar uma nova versão do runtime. Para obter mais informações, consulte uma das subseções.

As substituições e atualizações de runtime são anunciadas aqui:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> As informações aqui se aplicam a runtimes de linguagem que são criados em um aplicativo do Serviço de Aplicativo. Um runtime personalizado que você carrega no Serviço de Aplicativo, por exemplo, permanece inalterado, a menos que você o atualize manualmente.
>
>

### <a name="new-patch-updates"></a>Novas atualizações de patch

Atualizações de patch para .NET, PHP, SDK do Java ou versão do Tomcat são aplicadas automaticamente, substituindo a instalação existente pela versão mais recente. As atualizações de patch do Node.js são instaladas lado a lado com as versões existentes (de modo semelhante às versões principais e secundárias na próxima seção). As novas versões de patch do Python podem ser instaladas manualmente por meio de [extensões do site](https://azure.microsoft.com/blog/azure-web-sites-extensions/), lado a lado com as instalações internas do Python.

### <a name="new-major-and-minor-versions"></a>Novas versões principais e secundárias

Quando uma nova versão principal ou secundária é adicionada, ela é instalada lado a lado com as versões existentes. Você pode atualizar manualmente seu aplicativo para a nova versão. Se você configurou a versão do runtime em um arquivo de configuração (como `web.config` e `package.json`), é necessário atualizar com o mesmo método. Se você tiver usado uma configuração do Serviço de Aplicativo para configurar a sua versão do runtime, você poderá alterá-la no [Portal do Azure](https://portal.azure.com) ou por meio da execução de um comando da [CLI do Azure](/cli/azure/get-started-with-azure-cli) no [Cloud Shell](../cloud-shell/overview.md), como mostrado nos exemplos a seguir:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Versões preteridas  

Quando uma versão mais antiga é preterida, a data de remoção é comunicada para que você possa planejar adequadamente a atualização de versão do runtime. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Como faço para consultar o status da atualização do sistema operacional e do runtime nas minhas instâncias?  

Embora as informações críticas do sistema operacional tenham acesso bloqueado (consulte [Funcionalidade do sistema operacional no Serviço de Aplicativo do Azure](operating-system-functionality.md)), o [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) permite que você consulte a sua instância do Serviço de Aplicativo em relação à versão do sistema operacional e às versões do runtime. 

A tabela a seguir mostra como encontrar informações sobre as versões do Windows e do runtime da linguagem que está executando seus aplicativos:

| Informações do | Onde encontrá-las | 
|-|-|
| Versão do Windows | Consulte `https://<appname>.scm.azurewebsites.net/Env.cshtml` (em Informações do Sistema) |
| Versão do .NET | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| Versão do .NET Core | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `dotnet --version` |
| Versão do PHP | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `php --version` |
| Versão do Node.js padrão | No [Cloud Shell](../cloud-shell/overview.md), execute o seguinte comando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Versão do Python | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `python --version` |  
| Versão Java | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `java -version` |  

> [!NOTE]  
> O acesso ao local do registro `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, em que as informações sobre [patches "KB"](/security-updates/SecurityBulletins/securitybulletins) é armazenada, está bloqueado.
>
>

## <a name="more-resources"></a>Mais recursos

[Central de Confiabilidade: segurança](https://www.microsoft.com/en-us/trustcenter/security)  
[ASP.NET Core de 64 bits no Serviço de Aplicativo do Azure](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
