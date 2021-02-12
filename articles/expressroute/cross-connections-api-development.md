---
title: Desenvolvimento e integração da API CrossConnnections do Azure ExpressRoute
description: Este artigo fornece uma visão geral detalhada para parceiros do ExpressRoute sobre o tipo de recurso expressRouteCrossConnections.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011975"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Desenvolvimento e integração de API do ExpressRoute CrossConnnections

A API do Gerenciador de recursos de parceiros do ExpressRoute permite que os parceiros do ExpressRoute gerenciem a configuração de camada 2 e de camada 3 dos circuitos do ExpressRoute do cliente. A API do Gerenciador de recursos de parceiro do ExpressRoute introduz um novo tipo de recurso, **expressRouteCrossConnections**. Os parceiros usam esse recurso para gerenciar os circuitos de ExpressRoute do cliente.

## <a name="workflow"></a>Fluxo de trabalho

O recurso expressRouteCrossConnections é um recurso de sombra para o circuito do ExpressRoute. Quando um cliente do Azure cria um circuito do ExpressRoute e seleciona um parceiro do ExpressRoute específico, a Microsoft cria um recurso expressRouteCrossConnections na assinatura de gerenciamento do Azure ExpressRoute do parceiro. Ao fazer isso, a Microsoft define um grupo de recursos para criar o recurso expressRouteCrossConnections no. O padrão de nomenclatura para o grupo de recursos é **CrossConnection-* PeeringLocation * * *; em que PeeringLocation = o local do ExpressRoute. Por exemplo, se um cliente criar um circuito de ExpressRoute em Denver, o CrossConnection será criado na assinatura do Azure do parceiro no seguinte grupo de recursos: **CrossConnnection-Denver**.

Os parceiros do ExpressRoute gerenciam a configuração de camada 2 e de camada 3 emitindo operações REST no recurso expressRouteCrossConnections.

## <a name="benefits"></a>Benefícios

Benefícios da mudança para o recurso expressRouteCrossConnections:

* Quaisquer aprimoramentos futuros para parceiros do ExpressRoute serão disponibilizados no recurso ExpressRouteCrossConnection.

* Os parceiros podem aplicar o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) ao recurso expressRouteCrossConnection. Esses controles podem definir permissões para as quais as contas de usuários podem modificar o recurso expressRouteCrossConnection e adicionar/atualizar/excluir configurações de emparelhamento.

* O recurso expressRouteCrossConnection expõe APIs que podem ser úteis para solucionar problemas de conexões do ExpressRoute. Isso inclui a tabela ARP, o resumo da tabela de rotas BGP e os detalhes da tabela de rotas BGP. Essa funcionalidade não tem suporte nas APIs de implantação clássicas.

* Os parceiros também podem procurar as comunidades anunciadas no emparelhamento da Microsoft usando o recurso *RouteFilter* .

## <a name="api-development-and-integration-steps"></a>Etapas de desenvolvimento e integração de API

Para desenvolver com a API de parceiro, os parceiros de ExpressRoute utilizam uma configuração de cliente de teste e de parceiro de teste. A configuração de cliente de teste será usada para criar circuitos de ExpressRoute em locais de emparelhamento de teste que são mapeados para dispositivos e portas fictícios. A configuração do parceiro de teste é usada para gerenciar os circuitos do ExpressRoute criados no local de emparelhamento de teste.

### <a name="1-enlist-subscriptions"></a>1. inscrever assinaturas

Para solicitar o parceiro de teste e testar a configuração do cliente, inscreva duas assinaturas do Azure pagas conforme o uso em seu contato de engenharia do ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Esta assinatura será usada para gerenciar os circuitos de ExpressRoute criados nos locais de emparelhamento de teste em dispositivos e portas fictícios.

* **ExpressRoute_API_Dev_Customer_Sub:** Esta assinatura será usada para criar circuitos do ExpressRoute em locais de emparelhamento de teste que são mapeados para dispositivos e portas fictícios.

Os locais de emparelhamento de teste: dispositivos e portas fictícios não são expostos aos clientes de produção por padrão. Para criar circuitos de ExpressRoute que são mapeados para a configuração de teste, um sinalizador de recurso de assinatura precisa ser habilitado.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registre a assinatura do Dev_Provider para acessar a API do expressRouteCrossConnections

Para acessar a API do expressRouteCrossConnections, a assinatura do parceiro precisa ser inscrita no **provedor de recursos Microsoft. Network**. Siga as etapas no artigo [provedores de recursos e tipos do Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) para concluir o processo de registro.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. configurar a autenticação para Azure Resource Manager chamadas à API REST

A maioria dos serviços do Azure exige que o código do cliente seja autenticado com o Resource Manager, usando credenciais válidas, antes de chamar APIs de serviço. A autenticação é coordenada entre os vários atores pelo Azure AD e fornece ao cliente um token de acesso como prova de autenticação.

O processo de autenticação envolve duas etapas principais:

1. [Registrar o cliente](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Crie a solicitação de acesso](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. fornecer permissão de colaborador de rede para o aplicativo cliente

Depois que a autenticação tiver sido configurada com êxito, você precisará conceder acesso de colaborador de rede ao seu aplicativo cliente, sob a Dev_Provider_Sub. Para conceder permissão, entre no [portal do Azure](https://ms.portal.azure.com/#home) e conclua as seguintes etapas:

1. Navegue até assinaturas e selecione o Dev_Provider_Sub
2. Navegar para o controle de acesso (IAM)
3. Adicionar atribuição de função
4. Selecione a função de colaborador de rede
5. Atribuir acesso ao usuário, grupo ou entidade de serviço do Azure AD
6. Selecione seu aplicativo cliente
7. Salvar alterações

### <a name="5-develop"></a>5. desenvolver

Desenvolva em relação à [API do expressRouteCrossConnections](/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>API REST

Consulte a documentação da [API REST do ExpressRoute CrossConnections](/rest/api/expressroute/expressroutecrossconnections) para a API REST.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre todas as APIs REST do ExpressRoute, consulte [APIs REST do expressroute](/rest/api/expressroute/).