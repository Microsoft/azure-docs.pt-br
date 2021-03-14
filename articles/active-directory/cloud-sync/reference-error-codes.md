---
title: Códigos e descrições de erro de sincronização de nuvem Azure AD Connect
description: artigo de referência para códigos de erro de sincronização de nuvem
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b1dbd9064e24327f129e8b8f957414d9162386
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650933"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Códigos e descrições de erro de sincronização de nuvem Azure AD Connect
A seguir está uma lista de códigos de erro e sua descrição


## <a name="error-codes"></a>Códigos do Erro

|Código do erro|Detalhes|Cenário|Resolução|
|-----|-----|-----|-----|
|TimeOut|Mensagem de erro: detectamos um erro de tempo limite de solicitação ao contatar o agente local e sincronizar sua configuração. Para obter problemas adicionais relacionados ao agente de sincronização de nuvem, consulte nossas diretrizes de solução de problemas.|A solicitação ao seu tempo limite foi atingido. O valor do tempo limite atual é de 10 minutos.|Consulte nossas [diretrizes de solução de problemas](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Mensagem de erro: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Detalhes adicionais: o processamento da solicitação HTTP resultou em uma exceção. |Não foi possível processar os parâmetros recebidos na solicitação SCIM para uma solicitação de pesquisa.|Consulte a resposta HTTP retornada pela propriedade ' Response ' dessa exceção para obter detalhes.|
|HybridIdentityServiceNoAgentsAssigned|Mensagem de erro: não é possível encontrar um agente ativo para o domínio que você está tentando sincronizar. Verifique se os agentes foram removidos. Nesse caso, reinstale o agente novamente.|Não há agentes em execução. Provavelmente, os agentes foram removidos. Registre um novo agente.|"Nesse caso, você não verá nenhum agente atribuído ao domínio no Portal.|
|HybridIdentityServiceNoActiveAgents|Mensagem de erro: não é possível encontrar um agente ativo para o domínio que você está tentando sincronizar. Verifique se o agente está em execução acessando o servidor, onde o agente está instalado e verifique se "Microsoft Azure AD agente de sincronização de nuvem" em serviços está em execução.|"Os agentes não estão ouvindo o ponto de extremidade do ServiceBus. [O agente está atrás de um firewall que não permite conexões com o barramento de serviço](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Mensagem de erro: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Detalhes adicionais: parece haver um problema com sua configuração de sincronização de nuvem. Registre novamente seu agente de sincronização de nuvem no domínio local do AD e reinicie a configuração do portal do Azure.|O nome do recurso deve ser definido para que ele saiba qual agente deve ser contatado.|Registre novamente seu agente de sincronização de nuvem no domínio local do AD e reinicie a configuração do portal do Azure.|
|HybridIdentityServiceAgentSignalingError|Mensagem de erro: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9. Detalhes adicionais: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração).|O barramento de serviço não pode enviar uma mensagem para o agente. Pode ser uma interrupção no barramento de serviço ou o agente não está respondendo.|Se esse problema persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração).|
|AzureDirectoryServiceServerBusy|Mensagem de erro: ocorreu um erro. Código de erro: 81. Descrição do erro: o Azure Active Directory está ocupado no momento. Esta operação será repetida automaticamente. Se esse problema persistir por mais de 24 horas, entre em contato com o suporte técnico. ID de rastreamento: nome do servidor 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f:|O Azure Active Directory está ocupado no momento.|Se esse problema persistir por mais de 24 horas, entre em contato com o suporte técnico.|
|AzureActiveDirectoryInvalidCredential|Mensagem de erro: encontramos um problema com a conta de serviço que é usada para executar Azure AD Connect sincronização de nuvem. Você pode reparar a conta de serviço de nuvem seguindo as instruções [aqui](./how-to-troubleshoot.md). Se o erro persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração). Detalhes adicionais do erro: CredentialsInvalid AADSTS50034: a conta de usuário {EmailHidden} não existe no diretório skydrive365.onmicrosoft.com. Para entrar nesse aplicativo, a conta deve ser adicionada ao diretório. ID de rastreamento: 14b63033-3bc9-4bd4-b871-5eb4b3500200 ID de correlação: 57d93ed1-be4d-483c-997c-a3b6f03deb00 timestamp: 2021-01-12 21:08:29Z |Esse erro é gerado quando a conta de serviço de sincronização ADToAADSyncServiceAccount não existe no locatário. Pode ser devido à exclusão acidental da conta.|Use [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) para corrigir a conta de serviço.|
|AzureActiveDirectoryExpiredCredentials|Mensagem de erro: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração). Detalhes adicionais do erro: CredentialsExpired AADSTS50055: a senha expirou. ID de rastreamento: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 ID de correlação: 1c69b196-1c3a-4381-9187-c84747807155 timestamp: 2021-01-12 20:59:31Z | O código de status de resposta não indica êxito: 401 (não autorizado).|AAD Sync credenciais de conta de serviço estão expiradas.|Você pode reparar a conta de serviço de nuvem seguindo as instruções em https://go.microsoft.com/fwlink/?linkid=2150988 . Se o erro persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração).  Detalhes adicionais do erro: suas credenciais de locatário de Azure Active Directory administrativo foram trocadas por um token OAuth que expirou desde então. "|
|AzureActiveDirectoryAuthenticationFailed|Mensagem de erro: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac. Detalhes adicionais: não foi possível processar esta solicitação neste ponto. Se esse problema persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração). Detalhes adicionais do erro: UnexpectedError.|Erro desconhecido.|Se esse problema persistir, entre em contato com o suporte com a ID do trabalho (do painel de status da sua configuração).|

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)