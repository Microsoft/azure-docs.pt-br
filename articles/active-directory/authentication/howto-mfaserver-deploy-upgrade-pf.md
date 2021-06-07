---
title: Atualizar PhoneFactor para o servidor MFA do Azure-Azure Active Directory
description: Introdução ao servidor Azure MFA ao atualizar do phonefactor agent mais antigo.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808109e00decf5c4084be5be550b49e2e7d4628c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742350"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizar o PhoneFactor Agent para o Servidor de Autenticação Multifator do Azure

Para atualizar o PhoneFactor Agent v5.x ou mais antigo para o Servidor de Autenticação Multifator do Azure, desinstale o Agente PhoneFactor e os componentes afiliados primeiro. Em seguida, o Servidor de Autenticação Multifator e seus componentes afiliados podem ser instalados.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="uninstall-the-phonefactor-agent"></a>Desinstale o agente PhoneFactor

1. Primeiro, faça backup do arquivo de dados do PhoneFactor. O local de instalação padrão é C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.

2. Se o Portal do Usuário estiver instalado:
   1. Navegue até a pasta de instalação e faça backup do arquivo web.config O local de instalação padrão é C:\inetpub\wwwroot\PhoneFactor.

   2. Se você adicionou temas personalizados ao portal, faça backup de sua pasta personalizada abaixo do diretório C:\inetpub\wwwroot\PhoneFactor\App_Themes.

   3. Desinstale o Portal do Usuário com o PhoneFactor Agent (disponível apenas se instalado no mesmo servidor que o PhoneFactor Agent) ou com Programa e Recursos do Windows.

3. Se o serviço Web dos Aplicativos Móveis estiver instalado:

   1. Vá para a pasta de instalação e faça backup do arquivo web.config O local de instalação padrão é C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

   2. Desinstale o Serviço Web de Aplicativos Móveis com Programas e Recursos do Windows.

4. Se o SDK de serviço da Web estiver instalado, desinstale-a com o PhoneFactor Agent ou com Programa e Recursos do Windows.

5. Desinstale o PhoneFactor Agent com Programas e Recursos do Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instalar o Servidor de Autenticação Multifator

O caminho de instalação é obtido do registro da instalação anterior do PhoneFactor Agent, de modo que ele deve ser instalado no mesmo local (por exemplo, C:\Arquivos de Programas\PhoneFactor). As novas instalações terão um padrão diferente (por exemplo, C:\Arquivos de Programas\Servidor de Autenticação Multifator). O arquivo de dados deixado pelo PhoneFactor Agent anterior deve ser atualizado durante a instalação, para que seus usuários e configurações ainda estejam lá após instalar o novo Servidor de Autenticação Multifator.

1. Se solicitado, ative o Servidor de Autenticação Multifator e certifique-se de que ele esteja atribuído ao grupo de replicação correto.

2. Se o SDK de Serviço Web já foi instalado, instale o novo SDK de Serviço Web por meio da interface do usuário do Servidor de Autenticação Multifator.

   O nome do diretório virtual padrão agora é **MultiFactorAuthWebServiceSdk** em vez de **PhoneFactorWebServiceSdk**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, será preciso alterar a URL em todos os aplicativos que referenciem o SDK de Serviço Web (como o Portal do Usuário e o Serviço Web de Aplicativos Móveis) para apontar para o local correto.

3. Se o Portal do Usuário já foi instalado no Servidor do PhoneFactor Agent, instale o novo Portal do Usuário da Autenticação Multifator pela interface do usuário do Servidor de Autenticação Multifator.

   O nome do diretório virtual padrão agora é **MultiFactorAuth** em vez de **PhoneFactor**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do Portal do usuário no Servidor de Autenticação Multifator e atualize a URL do Portal do Usuário na guia Configurações.

4. Se o Portal do Usuário e/ou o Serviço Web de Aplicativos Móveis foi instalado anteriormente em um servidor diferente do Agente PhoneFactor:

   1. Vá para o local de instalação (por exemplo, C:\Arquivos de Programas\phonefactor) e copie um ou mais instaladores para o outro servidor. Há instaladores de 32 bits e 64 bits para o Portal do Usuário e o Serviço Web de Aplicativos Móveis. Eles são chamados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

   2. Para instalar o Portal do Usuário no servidor Web, abra um prompt de comando como administrador e execute MultiFactorAuthenticationUserPortalSetupXX.msi.

      O nome do diretório virtual padrão agora é **MultiFactorAuth** em vez de **PhoneFactor**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do portal do usuário na Servidor de Autenticação Multifator e atualize a URL do portal do usuário na guia Configurações. Os usuários existentes precisam ser informados sobre a nova URL.

   3. Vá para o local de instalação do Portal do Usuário (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) e edite o arquivo web.config. Copie os valores nas seções appSettings e applicationSettings do arquivo web.config original que foi salvo em backup antes da atualização para o novo arquivo web.config. Se o novo nome do diretório virtual padrão foi mantido ao instalar o SDK do Serviço Web, altere a URL na seção applicationSettings para apontar para o local correto. Se outros padrões foram alterados no arquivo web.config anterior, aplique as mesmas alterações ao novo arquivo web.config.

> [!NOTE]
> Ao fazer upgrade de uma versão do Azure MFA Server mais antiga do que 8.0 a 8.0+ que o serviço Web de aplicativos móveis pode ser desinstalado após o upgrade

## <a name="next-steps"></a>Próximas etapas

- [Instale o portal dos usuários](howto-mfaserver-deploy-userportal.md) para o Servidor de Autenticação Multifator do Azure.

- [Configurar a autenticação do Windows](howto-mfaserver-windows.md) para seus aplicativos. 
