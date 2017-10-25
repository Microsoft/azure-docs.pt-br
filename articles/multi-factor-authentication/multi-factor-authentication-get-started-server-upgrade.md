---
title: Atualizar o PhoneFactor para o servidor MFA do Azure | Microsoft Docs
description: "Introdução ao servidor Azure MFA ao atualizar do phonefactor agent mais antigo."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: kgremban
ms.openlocfilehash: 7ab7e693909f807781744ae53eed75d425096590
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizar o PhoneFactor Agent para o Servidor de Autenticação Multifator do Azure
Para atualizar o PhoneFactor Agent v5.x ou mais antigo para o Servidor de Autenticação Multifator do Azure, desinstale o Agente PhoneFactor e os componentes afiliados primeiro. Em seguida, o Servidor de Autenticação Multifator e seus componentes afiliados podem ser instalados.

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

O caminho de instalação é obtido do registro da instalação anterior do PhoneFactor Agent, de modo que ele deve ser instalado no mesmo local (por exemplo, C:\Arquivos de Programas\PhoneFactor). As novas instalações terão um padrão diferente (por exemplo, C:\Arquivos de Programas\Servidor de Autenticação Multifator). O arquivo de dados deixado pelo PhoneFactor Agent anterior deve ser atualizado durante a instalação, para que seus usuários e configurações ainda estejam lá após instalar o novo Servidor do Multi-Factor Authentication.

1. Se solicitado, ative o Servidor do Multi-Factor Authentication e certifique-se de que ele esteja atribuído ao grupo de replicação correto.

2. Se o SDK de Serviço Web já foi instalado, instale o novo SDK de Serviço Web por meio da interface do usuário do Servidor Multi-Factor Authentication.

  O nome do diretório virtual padrão agora é **MultiFactorAuthWebServiceSdk** em vez de **PhoneFactorWebServiceSdk**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, será preciso alterar a URL em todos os aplicativos que referenciem o SDK de Serviço Web (como o Portal do Usuário e o Serviço Web de Aplicativos Móveis) para apontar para o local correto.

3. Se o Portal do Usuário já foi instalado no Servidor do PhoneFactor Agent, instale o novo Portal de Usuário do Multi-Factor Authentication pela interface do usuário do Servidor Multi-Factor Authentication.

  O nome do diretório virtual padrão agora é **MultiFactorAuth** em vez de **PhoneFactor**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do Portal do usuário no Servidor Multi-Factor Authentication e atualize a URL do Portal do Usuário na guia Configurações.

4. Se o Portal do Usuário e/ou o Serviço Web de Aplicativos Móveis foi instalado anteriormente em um servidor diferente do Agente PhoneFactor:

  1. Vá para o local de instalação (por exemplo, C:\Arquivos de Programas\phonefactor) e copie um ou mais instaladores para o outro servidor. Há instaladores de 32 bits e 64 bits para o Portal do Usuário e o Serviço Web de Aplicativos Móveis. Eles são chamados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Para instalar o Portal do Usuário no servidor Web, abra um prompt de comando como administrador e execute MultiFactorAuthenticationUserPortalSetupXX.msi.

    O nome do diretório virtual padrão agora é **MultiFactorAuth** em vez de **PhoneFactor**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do Portal do usuário no Servidor Multi-Factor Authentication e atualize a URL do Portal do Usuário na guia Configurações. Os usuários existentes precisarão ser informados sobre a nova URL.

  3. Vá para o local de instalação do Portal do Usuário (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) e edite o arquivo web.config. Copie os valores nas seções appSettings e applicationSettings do arquivo web.config original que foi salvo em backup antes da atualização para o novo arquivo web.config. Se o novo nome do diretório virtual padrão foi mantido ao instalar o SDK do Serviço Web, altere a URL na seção applicationSettings para apontar para o local correto. Se outros padrões foram alterados no arquivo web.config anterior, aplique as mesmas alterações ao novo arquivo web.config.

  4. Para instalar o Portal do Usuário do Serviço Web no servidor Web, abra um prompt de comando como administrador e execute o MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

    O nome do diretório virtual padrão agora é **MultiFactorAuthMobileAppWebService** em vez de **PhoneFactorPhoneAppWebService**. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Convém escolher um nome mais curto para facilitar para os usuários finais a digitação em seus dispositivos móveis. Caso contrário, se você permite que a instalação use o novo nome padrão, clique no ícone do Portal do Usuário no Servidor Multi-Factor Authentication e atualize a URL do Serviço Web de Aplicativos Móveis.

  5. Vá para o local de instalação do Serviço Web de Aplicativos Móveis (por exemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) e edite o arquivo web.config. Copie os valores nas seções appSettings e applicationSettings do arquivo web.config original que foi salvo em backup antes da atualização para o novo arquivo web.config. Se o novo nome do diretório virtual padrão foi mantido ao instalar o SDK do Serviço Web, altere a URL na seção applicationSettings para apontar para o local correto. Se outros padrões foram alterados no arquivo web.config anterior, aplique as mesmas alterações ao novo arquivo web.config.

## <a name="next-steps"></a>Próximas etapas

- [Instale o portal dos usuários](multi-factor-authentication-get-started-portal.md) para o Servidor Autenticação Multifator do Azure.

- [Configurar a autenticação do Windows](multi-factor-authentication-get-started-server-windows.md) para seus aplicativos. 
