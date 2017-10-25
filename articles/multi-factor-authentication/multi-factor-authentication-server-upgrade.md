---
title: "Atualização do Servidor do Azure MFA | Microsoft Docs"
description: "Etapas e diretrizes para atualizar o Servidor de Autenticação Multifator do Azure para uma versão mais nova."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 6e4e09f8539aad56f92ad9137f4a6b9eb0d82370
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Atualizar para o último Servidor de Autenticação Multifator do Azure

Este artigo descreve o processo de atualização do Servidor do Azure MFA (Autenticação Multifator) v6.0 ou superior. Se precisar atualizar uma versão antiga do Agente PhoneFactor, consulte [Atualizar o Agente PhoneFactor para o Servidor de Autenticação Multifator do Azure](multi-factor-authentication-get-started-server-upgrade.md).

Se estiver atualizando da v6.x ou anterior para a v7.x ou mais nova, todos os componentes serão alterados do .NET 2.0 para o .NET 4.5. Todos os componentes também exigem os Pacotes Redistribuíveis do Microsoft Visual C++ 2015 Atualização 1 ou posterior. O instalador do Servidor MFA instala as versões x86 e x64 desses componentes, caso ainda não estejam instalados. Se o Portal do Usuário e o Serviço Web do Aplicativo Móvel forem executados em servidores separados, você precisará instalar esses pacotes antes de atualizar esses componentes. É possível pesquisar a última atualização dos Pacotes Redistribuíveis do Microsoft Visual C++ 2015 no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Instalar a última versão do Servidor do Azure MFA

1. Use as instruções descritas em [Baixar o Servidor de Autenticação Multifator do Azure](multi-factor-authentication-get-started-server.md#download-the-azure-multi-factor-authentication-server) para obter a última versão do Servidor do Azure MFA.
2. Faça um backup do arquivo de dados do Servidor MFA localizado em C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (considerando o local de instalação padrão) no Servidor mestre do MFA.
3. Se você executar vários servidores para alta disponibilidade, altere os sistemas cliente que se autenticam no Servidor MFA, para que eles interrompam o envio de tráfego para os servidores que estão sendo atualizados. Se você usar um balanceador de carga, remova um Servidor MFA do balanceador de carga, faça a atualização e, em seguida, adicione o servidor novamente ao farm.
4. Execute o novo instalador em cada Servidor MFA. Atualize os servidores subordinados primeiro, pois eles poderão ler o arquivo de dados antigo que está sendo replicado pelo mestre. 

  Não é necessário desinstalar o Servidor MFA atual antes de executar o instalador. O instalador executa uma atualização in-loco. O caminho de instalação é obtido do Registro da instalação anterior, para que ele seja instalado no mesmo local (por exemplo, C:\Program Files\Multi-Factor Authentication Server). 
  
5. Se você for solicitado a instalar uma atualização dos Pacotes Redistribuíveis do Microsoft Visual C++ 2015, aceite o prompt. As versões x86 e x64 do pacote são instaladas.
5. Se você usar o SDK do serviço Web, precisará instalar o novo SDK do serviço Web. Ao instalar o novo SDK do Serviço Web, verifique se o nome do diretório virtual corresponde ao diretório virtual instalado anteriormente (por exemplo, MultiFactorAuthWebServiceSdk).
6. Repita as etapas em todos os servidores subordinados. Promova um dos subordinados para ser o novo mestre e, em seguida, atualize o servidor mestre antigo. 

## <a name="upgrade-the-user-portal"></a>Atualizar o Portal do Usuário

1. Faça um backup do arquivo web.config que está no diretório virtual do local de instalação do Portal do Usuário (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth). Se alguma alteração foi feita no tema padrão, faça um backup da pasta App_Themes\Default também. É melhor criar uma cópia da pasta Default e criar um novo tema do que alterar o tema Padrão.
2. Se o Portal do Usuário for executado no mesmo servidor que os outros componentes do Servidor MFA, a instalação do Servidor MFA solicitará a atualização do Portal do Usuário. Aceite a solicitação e instale a atualização do Portal do Usuário. Verifique se o nome do diretório virtual corresponde ao diretório virtual instalado anteriormente (por exemplo, MultiFactorAuth).
3. Se o Portal do Usuário estiver em seu próprio servidor, copie o arquivo MultiFactorAuthenticationUserPortalSetup64.msi do local de instalação de um dos Servidores do MFA e coloque-o no servidor Web do Portal do Usuário. Execute o instalador. 

  Se ocorrer um erro informando que os Pacotes Redistribuíveis do Microsoft Visual C++ 2015 Atualização 1 ou posterior são obrigatórios, baixe e instale o último pacote de atualização no [Centro de Download da Microsoft](https://www.microsoft.com/download/). Instale as versões x86 e x64.

4. Após a instalação do software atualizado do Portal do Usuário, compare o backup de web.config criado na etapa 1 com o novo arquivo web.config. Se não houver nenhum atributo no novo web.config, copie o web.config de backup para o diretório virtual para substituir o novo. Outra opção é copiar e colar os valores de appSettings e a URL do SDK do Serviço Web do arquivo de backup para o novo web.config.

Se você tiver o Portal do Usuário em vários servidores, repita a instalação em todos eles. 


## <a name="upgrade-the-mobile-app-web-service"></a>Atualizar o Serviço Web do Aplicativo Móvel

1. Faça um backup do arquivo web.config que está no diretório virtual do local de instalação do Serviço Web do Aplicativo Móvel (por exemplo, C:\inetpub\wwwroot\app ou C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Copie o arquivo MultiFactorAuthenticationMobileAppWebServiceSetup64.msi do local de instalação dos Servidores do MFA e coloque-o no servidor Web de registro do Aplicativo Móvel.
3. Execute o instalador. 

  Se ocorrer um erro informando que os Pacotes Redistribuíveis do Microsoft Visual C++ 2015 Atualização 1 ou posterior são obrigatórios, baixe e instale o último pacote de atualização no [Centro de Download da Microsoft](https://www.microsoft.com/download/). Instale as versões x86 e x64.

4. Após a instalação do software atualizado do Serviço Web do Aplicativo Móvel, compare o arquivo web.config do qual foi criado um backup na etapa 1 com o novo arquivo web.config. Se não houver nenhum atributo no novo web.config, será possível copiar o web.config salvo novamente para o diretório virtual e substituir o novo. Outra opção é copiar e colar os valores de appSettings e a URL do SDK do Serviço Web do arquivo de backup para o novo web.config.

Se você tiver o Serviço Web do Aplicativo Móvel em vários servidores, repita a instalação em todos eles. 

## <a name="upgrade-the-ad-fs-adapters"></a>Atualizar os adaptadores do AD FS


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se o MFA for executado em servidores diferentes do que o AD FS

Essas instruções se aplicarão apenas se você executar o Servidor de Autenticação Multifator separadamente dos servidores do AD FS. Se ambos os serviços forem executados nos mesmos servidores, ignore esta seção e vá para as etapas de instalação. 

1. Salve uma cópia do MultiFactorAuthenticationAdfsAdapter.config existente que foi registrado no AD FS ou exporte a configuração usando o seguinte comando do PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. O nome do adaptador é “WindowsAzureMultiFactorAuthentication” ou “AzureMfaServerAuthentication”, dependendo da versão instalada anteriormente.
2. Copie os seguintes arquivos do local de instalação do Servidor MFA para os servidores do AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Edite o script Register-multifactorauthenticationadfsadapter.ps1 adicionando `-ConfigurationFilePath [path]` ao final do comando `Register-AdfsAuthenticationProvider`. Substitua *[caminho]* pelo caminho completo até o arquivo MultiFactorAuthenticationAdfsAdapter.config ou até o arquivo de configuração exportado na etapa anterior. 

  Verifique os atributos no novo MultiFactorAuthenticationAdfsAdapter.config para ver se eles correspondem ao arquivo de configuração antigo. Se todos os atributos foram adicionados ou removidos na nova versão, copie os valores de atributo do arquivo de configuração antigo para o novo ou modifique o arquivo de configuração antigo para que ele seja correspondente.

### <a name="install-new-ad-fs-adapters"></a>Instalar novos adaptadores do AD FS

> [!IMPORTANT] 
> Os usuários não precisarão realizar a verificação em duas etapas durante as etapas 3 a 8 desta seção. Se você tiver o AD FS configurado em vários clusters, será possível remover, atualizar e restaurar cada cluster no farm, independentemente dos outros clusters, para evitar tempo de inatividade.

1. Remova alguns servidores do AD FS do farm. Atualize esses servidores enquanto os outros ainda estiverem em execução.
2. Instale o novo adaptador do AD FS em cada servidor removido do farm do AD FS. Se o Servidor MFA for instalado em cada servidor do AD FS, será possível atualizar por meio do UX de administrador do Servidor MFA. Caso contrário, atualize executando MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Se ocorrer um erro informando que os Pacotes Redistribuíveis do Microsoft Visual C++ 2015 Atualização 1 ou posterior são obrigatórios, baixe e instale o último pacote de atualização no [Centro de Download da Microsoft](https://www.microsoft.com/download/). Instale as versões x86 e x64.

3. Acesse **AD FS** > **Políticas de Autenticação** > **Editar Política de Autenticação Multifator Global**. Desmarque **WindowsAzureMultiFactorAuthentication** ou **AzureMFAServerAuthentication** (dependendo da versão atual instalada). 

  Quando essa etapa for concluída, a verificação em duas etapas por meio do Servidor MFA não estará disponível neste cluster do AD FS após a conclusão da etapa 8.

4. Cancele o registro da versão antiga do adaptador do AD FS executando o script Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 do PowerShell. Verifique se o parâmetro *-Name* (“WindowsAzureMultiFactorAuthentication” ou "AzureMFAServerAuthentication") corresponde ao nome exibido na etapa 3. Isso se aplica a todos os servidores no mesmo cluster do AD FS, pois há uma configuração central.
5. Registre o novo adaptador do AD FS executando o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 do PowerShell. Isso se aplica a todos os servidores no mesmo cluster do AD FS, pois há uma configuração central.
6. Reinicie o serviço do AD FS em cada servidor removido do farm do AD FS.
7. Adicione os servidores atualizados novamente ao farm do AD FS e remova os outros servidores do farm.
8. Acesse **AD FS** > **Políticas de Autenticação** > **Editar Política de Autenticação Multifator Global**. Marque **AzureMfaServerAuthentication**.
9. Repita a etapa 2 para atualizar os servidores agora removidos do farm do AD FS e reinicie o serviço do AD FS nesses servidores.
10. Adicione esses servidores novamente ao farm do AD FS.

## <a name="next-steps"></a>Próximas etapas

- Obtenha exemplos de [Cenários avançados com a Autenticação Multifator do Azure e VPNs de terceiros](multi-factor-authentication-advanced-vpn-configurations.md)

- [Sincronizar o Servidor MFA com o Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)

- [Configurar a Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md) para os aplicativos
