---
title: Aprofundamento na sincronização do Azure AD Connect Cloud – como funciona
description: Este tópico fornece informações aprofundadas sobre como funciona a sincronização de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613060"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Aprofundamento da sincronização de nuvem-como funciona

## <a name="overview-of-components"></a>Visão geral dos componentes

![Como ele funciona](media/concept-how-it-works/how-1.png)

A sincronização de nuvem é criada sobre os serviços do Azure AD e tem dois componentes principais:

- **Agente de provisionamento**: o Azure ad Connect agente de provisionamento de nuvem é o mesmo agente que o workday de entrada e criado na mesma tecnologia do lado do servidor que o proxy de aplicativo e a autenticação de passagem. Ele requer somente conexão de saída e os agentes são atualizados automaticamente. 
- **Serviço de provisionamento**: o mesmo serviço de provisionamento como provisionamento de saída e provisionamento de entrada do workday que usa um modelo baseado em Agendador. No caso de sincronização de nuvem, as alterações são provisionadas a cada 2 minutos.


## <a name="initial-setup"></a>Instalação inicial
Durante a configuração inicial, são feitas algumas coisas que fazem a sincronização de nuvem acontecer.  Eles são: 

- **Durante a instalação do agente**: você configura o agente para os domínios do AD dos quais deseja provisionar.  Essa configuração registra os domínios no serviço de identidade híbrida e estabelece uma conexão de saída com o barramento de serviço escutando solicitações.
- **Ao habilitar o provisionamento**: você seleciona o domínio do AD e habilita o provisionamento, que é executado a cada 2 minutos. Opcionalmente, você pode anular a seleção da sincronização de hash de senha e definir o email de notificação. Você também pode gerenciar a transformação de atributo usando APIs Microsoft Graph.


## <a name="agent-installation"></a>Instalação do agente
Veja a seguir um passo a passo do que ocorre quando o agente de provisionamento de nuvem é instalado.

- Primeiro, o instalador instala os binários de agente e o serviço do Agent em execução na conta de serviço virtual (NETWORK SERVICE\AADProvisioningAgent).  Uma conta de serviço virtual é um tipo especial de conta que não tem uma senha e é gerenciada pelo Windows.
- Em seguida, o instalador inicia o assistente.
- O assistente solicitará as credenciais do Azure AD, será autenticado e recuperará um token.
- O assistente solicita as credenciais de administradores de domínio do computador atual.
- Usando essas credenciais, a conta de serviço gerenciado geral do agente (GMSA) para esse domínio será criada ou localizada e reutilizada se já existir.
- O serviço Agent agora é reconfigurado para ser executado no GMSA.
- O assistente agora solicita a configuração de domínio junto com a conta Admin (DA) do administrador corporativo para cada domínio que você deseja que o agente seja atendido.
- A conta GMSA é então atualizada com permissões que permitem o acesso a cada domínio inserido acima.
- Em seguida, o assistente aciona o registro do agente
- O agente cria um certificado e usando o token do Azure AD, registra-se e o certificado com o serviço de registro do serviço de identidade híbrida (seu)
- O assistente dispara uma chamada AgentResourceGrouping. Essa chamada para seu serviço de administração é atribuir o agente a um ou mais domínios do AD na configuração dele.
- O assistente agora reinicia o serviço do Agent.
- O agente chama um serviço de inicialização na reinicialização (e a cada 10 minutos depois) para verificar se há atualizações de configuração.  O serviço de inicialização valida a identidade do agente.  Ele também atualiza a hora da última inicialização.  Isso é importante porque, se os agentes não são inicializados, eles não estão obtendo pontos de extremidade do barramento de serviço atualizados e podem não ser capazes de receber solicitações. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é o SCIM (Sistema de Gerenciamento de Usuários entre Domínios)?

A [especificação scim](https://tools.ietf.org/html/draft-scim-core-schema-01) é um padrão usado para automatizar a troca de informações de identidade de usuário ou grupo entre domínios de identidade, como o Azure AD. O SCIM está se tornando o padrão verdadeiro para provisionamento e, quando usado em conjunto com padrões de federação como SAML ou OpenID Connect, ele fornece aos administradores uma solução de ponta a ponta baseada em padrões para o gerenciamento de acesso.

O agente de provisionamento de nuvem Azure AD Connect usa o SCIM com o Azure AD para provisionar e desprovisionar usuários e grupos.

## <a name="synchronization-flow"></a>Fluxo de sincronização
![Provisionando ](media/concept-how-it-works/provisioning-4.png) depois de instalar o agente e habilitar o provisionamento, ocorrerá o seguinte fluxo.

1.  Uma vez configurado, o serviço de provisionamento do Azure AD chama o serviço híbrido do Azure AD para adicionar uma solicitação ao barramento de serviço. O agente mantém constantemente uma conexão de saída com o barramento de serviço escutando solicitações e pega o sistema para a solicitação de SCIM (gerenciamento de identidade entre domínios) imediatamente. 
2.  O agente divide a solicitação em consultas separadas com base no tipo de objeto. 
3.  O AD retorna o resultado para o agente e o agente filtra esses dados antes de enviá-los ao Azure AD.  
4.  Agent retorna a resposta de SCIM para o Azure AD.  Essas respostas se baseiam na filtragem que ocorreu no agente.  O agente usa o escopo para filtrar os resultados. 
5.  O serviço de provisionamento grava as alterações no Azure AD.
6. Se essa for uma sincronização Delta em oposição a uma sincronização completa, o cookie/marca d' água será usado. Novas consultas receberão alterações desse cookie/marca d' água em diante.

## <a name="supported-scenarios"></a>Cenários com suporte:
Os cenários a seguir têm suporte para sincronização de nuvem.


- **Cliente híbrido existente com uma nova floresta**: Azure ad Connect sincronização é usada para florestas primárias. A sincronização de nuvem é usada para provisionamento de uma floresta do AD (incluindo desconectada). Para obter mais informações, consulte o tutorial [aqui](tutorial-existing-forest.md).

 ![Híbrido existente](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Novo cliente híbrido**: a sincronização de Azure ad Connect não é usada. A sincronização de nuvem é usada para provisionamento de uma floresta do AD.  Para obter mais informações, consulte o tutorial [aqui](tutorial-single-forest.md).
 
 ![Novos clientes](media/tutorial-single-forest/diagram-2.png)

- **Cliente híbrido existente**: Azure ad Connect sincronização é usada para florestas primárias. A sincronização de nuvem é piloto para um pequeno conjunto de usuários nas florestas principais [aqui](tutorial-existing-forest.md).

 ![Piloto existente](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Para obter mais informações, consulte [topologias com suporte](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect a sincronização de nuvem?](what-is-cloud-sync.md)
