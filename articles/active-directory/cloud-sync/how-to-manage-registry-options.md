---
title: 'Agente de provisionamento de nuvem Azure AD Connect: Gerenciar opções de registro | Microsoft Docs'
description: Este artigo descreve como gerenciar opções de registro no agente de provisionamento de Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678214"
---
# <a name="manage-agent-registry-options"></a>Gerenciar opções de registro do agente

Esta seção descreve as opções de registro que você pode definir para controlar o comportamento de processamento em tempo de execução do agente de provisionamento do Azure AD Connect. 

## <a name="configure-ldap-connection-timeout"></a>Configurar tempo limite de conexão LDAP
Ao executar operações LDAP em Active Directory controladores de domínio configurados, por padrão, o agente de provisionamento usa o valor de tempo limite de conexão padrão de 30 segundos. Se o controlador de domínio levar mais tempo para responder, você poderá ver a seguinte mensagem de erro no arquivo de log do agente: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

As operações de pesquisa LDAP podem levar mais tempo se o atributo de pesquisa não estiver indexado. Como uma primeira etapa, se você receber o erro acima, primeiro verifique se o atributo de pesquisa/pesquisa está [indexado](/windows/win32/ad/indexed-attributes). Se os atributos de pesquisa forem indexados e o erro persistir, você poderá aumentar o tempo limite de conexão LDAP usando as seguintes etapas: 

1. Faça logon como administrador no Windows Server que executa o agente de provisionamento do Azure AD Connect.
1. Use o item de menu *executar* para abrir o editor do registro (regedit.exe) 
1. Localize a pasta de chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique com o botão direito do mouse e selecione "novo > valor da cadeia de caracteres"
1. Forneça o nome: `LdapConnectionTimeoutInMilliseconds`
1. Clique duas vezes no **nome do valor** e insira os dados do valor como `60000` milissegundos.
    > [!div class="mx-imgBorder"]
    > ![Tempo limite de conexão LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Reinicie o serviço de provisionamento de Azure AD Connect no console de *Serviços* .
1. Se você tiver implantado vários agentes de provisionamento, aplique essa alteração de registro a todos os agentes para fins de consistência. 

## <a name="configure-referral-chasing"></a>Configurar a caça de referência
Por padrão, o agente de provisionamento do Azure AD Connect não procura [referências](/windows/win32/ad/referrals). Talvez você queira habilitar a busca de referência, para dar suporte a determinados cenários de provisionamento de entrada de RH, como: 
* Verificando a exclusividade do UPN em vários domínios
* Resolvendo referências do Gerenciador de domínio cruzado

Use as etapas a seguir para ativar a busca de referência:

1. Faça logon como administrador no Windows Server que executa o agente de provisionamento do Azure AD Connect.
1. Use o item de menu *executar* para abrir o editor do registro (regedit.exe) 
1. Localize a pasta de chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique com o botão direito do mouse e selecione "novo > valor da cadeia de caracteres"
1. Forneça o nome: `ReferralChasingOptions`
1. Clique duas vezes no **nome do valor** e insira os dados do valor como `96` . Esse valor corresponde ao valor constante para `ReferralChasingOptions.All` e especifica que a subárvore e as referências de nível base serão seguidas pelo agente. 
    > [!div class="mx-imgBorder"]
    > ![Caça de referência](media/how-to-manage-registry-options/referral-chasing.png)
1. Reinicie o serviço de provisionamento de Azure AD Connect no console de *Serviços* .
1. Se você tiver implantado vários agentes de provisionamento, aplique essa alteração de registro a todos os agentes para fins de consistência.

## <a name="skip-gmsa-configuration"></a>Ignorar configuração de GMSA
Com a versão do agente 1.1.281.0 +, por padrão, ao executar o assistente de configuração do agente, você será solicitado a configurar a [conta de serviço gerenciado de grupo (GMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). A configuração do GMSA pelo assistente é usada em tempo de execução para todas as operações de sincronização e provisionamento. 

Se você estiver atualizando de uma versão anterior do agente e tiver configurado uma conta de serviço personalizada com permissões de nível de UO delegadas específicas para sua topologia de Active Directory, talvez seja melhor ignorar/adiar a configuração do GMSA e planejar essa alteração. 

> [!NOTE]
> Essas diretrizes se aplicam especificamente aos clientes que configuraram o provisionamento de entrada (workday/SuccessFactors) com versões de agente anteriores ao 1.1.281.0 e que instalaram uma conta de serviço personalizada para operações de agente. A longo prazo, recomendamos mudar para GMSA como uma prática recomendada.  

Nesse cenário, você ainda pode atualizar os binários do agente e ignorar a configuração do GMSA usando as seguintes etapas: 

1. Faça logon como administrador no Windows Server que executa o agente de provisionamento do Azure AD Connect.
1. Execute o instalador do agente para instalar os novos binários de agente. Feche o assistente de configuração do agente que é aberto automaticamente após a instalação ser bem-sucedida. 
1. Use o item de menu *executar* para abrir o editor do registro (regedit.exe) 
1. Localize a pasta de chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique com o botão direito do mouse e selecione "novo > valor DWORD"
1. Forneça o nome: `UseCredentials`
1. Clique duas vezes no **nome do valor** e insira os dados do valor como `1` .  
    > [!div class="mx-imgBorder"]
    > ![Usar credenciais](media/how-to-manage-registry-options/use-credentials.png)
1. Reinicie o serviço de provisionamento de Azure AD Connect no console de *Serviços* .
1. Se você tiver implantado vários agentes de provisionamento, aplique essa alteração de registro a todos os agentes para fins de consistência.
1. No recorte curto da área de trabalho, execute o assistente de configuração do agente. O assistente irá ignorar a configuração do GMSA. 


> [!NOTE]
> Você pode confirmar se as opções de registro foram definidas habilitando o [log detalhado](how-to-troubleshoot.md#log-files). Os logs emitidos durante a inicialização do agente exibirão os valores de configuração selecionados do registro. 

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)

