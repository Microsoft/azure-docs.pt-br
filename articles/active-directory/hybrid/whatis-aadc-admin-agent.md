---
title: O que é o Agente Administrativo do Azure AD Connect – Azure AD Connect | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorar seu ambiente local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91312950"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o Agente Administrativo do Azure AD Connect? 
O Agente Administrativo do Azure AD Connect é um novo componente do Azure Active Directory Connect que pode ser instalado em um servidor do Azure Active Directory Connect. Ele é usado para coletar dados específicos de seu ambiente do Active Directory que ajudam um engenheiro de suporte da Microsoft a solucionar problemas quando você abre um caso de suporte. 

>[!NOTE]
>O agente administrativo não está instalado e habilitado por padrão.  Você deve instalar o agente para coletar dados para ajudar com casos de suporte.

Quando instalado, o Agente de Administração do Azure AD Connect aguarda solicitações específicas de dados do Azure Active Directory, obtém os dados solicitados do ambiente de sincronização e os envia ao Azure Active Directory, sendo apresentado ao engenheiro de suporte da Microsoft. 

As informações que o Agente de Administração do Azure AD Connect recupera do seu ambiente não são armazenadas de nenhuma forma – ele é exibido apenas para o engenheiro de suporte da Microsoft para ajudá-lo a investigar e solucionar problemas do caso de suporte relacionado do Azure Active Directory Connect que você abriu. O Agente de Administração do Azure AD Connect não está instalado no servidor do Azure AD Connect por padrão. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalar o Agente de Administração do Azure AD Connect no servidor do Azure AD Connect 

Pré-requisitos:
1.    Azure AD Connect instalado no servidor
2.    Azure AD Connect Health instalado no servidor

![agente administrativo](media/whatis-aadc-admin-agent/adminagent0.png)

Os binários do Agente de Administração do Azure AD Connect são colocados no servidor do AAD Connect. Para instalar o agente, faça o seguinte:

1.    Abra o PowerShell no modo admin
2.    Navegue até o diretório em que o aplicativo está localizado cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    Execute o ConfigureAdminAgent.ps1

Quando solicitado, insira suas credenciais de administrador global do Azure AD. Elas devem ser as mesmas credenciais inseridas durante a instalação do Azure AD Connect.

Depois que o agente for instalado, você verá os dois novos programas a seguir na lista "Adicionar/Remover Programas" no Painel de Controle do seu servidor: 

![Captura de tela que mostra a lista Adicionar/Remover Programas que inclui os novos programas adicionados.](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Quais dados do meu serviço de sincronização são mostrados para o engenheiro de serviços da Microsoft? 
Quando você abre um caso de suporte, o engenheiro de Suporte da Microsoft pode ver, para um determinado usuário, os dados relevantes no Active Directory, o espaço do conector do Active Directory no servidor do Azure Active Directory Connect, o espaço do conector do Azure Active Directory no servidor do Azure Active Directory Connect e o Metaverso no servidor do Azure Active Directory Connect. 

O engenheiro de Suporte da Microsoft não pode alterar nenhum dado do seu sistema e não pode ver nenhuma senha. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quiser que o engenheiro de suporte da Microsoft acesse meus dados? 
Depois que o agente for instalado, se você não quiser que o engenheiro de serviços da Microsoft acesse seus dados em uma chamada de suporte, poderá desabilitar a funcionalidade modificando o arquivo de configuração do serviço, conforme descrito abaixo: 

1. Abra **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
2. Desabilite a configuração **UserDataEnabled**, conforme mostrado abaixo. Se a configuração **UserDataEnabled** existir e estiver definida como true, defina-a como false. Se a configuração não existir, adicione a configuração conforme mostrado abaixo.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Salve o arquivo de configuração.
4. Reinicie o serviço do Agente de Administração do Azure AD Connect, conforme mostrado abaixo

![Captura de tela que mostra onde reiniciar o serviço do Agente Administrador do Azure AD.](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
