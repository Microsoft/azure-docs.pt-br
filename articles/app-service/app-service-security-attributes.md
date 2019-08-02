---
title: Atributos de segurança para o serviço Azure App
description: Uma lista de verificação de atributos de segurança para avaliar Azure App serviço
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442263"
---
# <a name="security-attributes-for-azure-app-service"></a>Atributos de segurança para o serviço Azure App

Este artigo documenta os atributos de segurança internos do serviço Azure App.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | O conteúdo do arquivo do site é armazenado no armazenamento do Azure, que criptografa automaticamente o conteúdo em repouso. Consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).<br><br>Os segredos fornecidos pelo cliente são criptografados em repouso. Os segredos são criptografados em repouso enquanto estão armazenados nos bancos de dados de configuração do serviço de aplicativo.<br><br>Os discos anexados localmente podem, opcionalmente, ser usados como armazenamento temporário por sites (D:\Local e% TMP%). Os discos anexados localmente não são criptografados em repouso. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Os clientes podem configurar sites para exigir e usar HTTPS para tráfego de entrada. Consulte a postagem no blog [como tornar um serviço Azure app somente HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | Os clientes podem optar por armazenar os segredos do aplicativo no Key Vault e recuperá-los em tempo de execução. Consulte [usar referências de Key Vault para o serviço de aplicativo e Azure Functions (versão prévia)](app-service-key-vault-references.md).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | As chamadas de gerenciamento para configurar o serviço de aplicativo ocorrem por meio de chamadas [Azure Resource Manager](../azure-resource-manager/index.yml) por HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Disponível atualmente na visualização do serviço de aplicativo. Consulte [Azure app restrições de acesso de serviço](app-service-ip-restrictions.md). |
| Suporte à injeção de VNet| Sim | Os ambientes de serviço de aplicativo são implementações privadas do serviço de aplicativo dedicado a um único cliente injetado na rede virtual de um cliente. Consulte [introdução aos ambientes do serviço de aplicativo](environment/intro.md). |
| Isolamento de rede e suporte de firewall| Sim | Para a variação pública de vários locatários do serviço de aplicativo, os clientes podem configurar ACLs de rede (restrições de IP) para bloquear o tráfego de entrada permitido.  Consulte [Azure app restrições de acesso de serviço](app-service-ip-restrictions.md).  Os ambientes do serviço de aplicativo são implantados diretamente em redes virtuais e, portanto, podem ser protegidos com o NSGs. |
| Suporte a túnel forçado| Sim | Os ambientes do serviço de aplicativo podem ser implantados na rede virtual do cliente em que o túnel forçado está configurado. Os clientes precisam seguir as instruções em [configurar seu ambiente do serviço de aplicativo com túnel forçado](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | O serviço de aplicativo integra-se com Application Insights para idiomas que dão suporte a Application Insights (completo .NET Framework, .NET Core, Java e node. JS).  Consulte [monitorar o desempenho do serviço Azure app](../azure-monitor/app/azure-web-apps.md). O serviço de aplicativo também envia métricas de aplicativo para Azure Monitor. Consulte [monitorar aplicativos no serviço Azure app](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Os clientes podem criar aplicativos no serviço de aplicativo que se integram automaticamente com o [Azure Active Directory (AD do Azure)](../active-directory/index.yml) , bem como outros provedores de identidade compatíveis com OAuth; consulte [autenticação e autorização no serviço de Azure app](overview-authentication-authorization.md). Para o acesso de gerenciamento aos ativos do serviço de aplicativo, todo o acesso é controlado por uma combinação de entidade de segurança autenticada do Azure AD e Azure Resource Manager funções RBAC. |
| Autorização| Sim | Para o acesso de gerenciamento aos ativos do serviço de aplicativo, todo o acesso é controlado por uma combinação de entidade de segurança autenticada do Azure AD e Azure Resource Manager funções RBAC.  |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações de gerenciamento executadas nos objetos do serviço de aplicativo ocorrem por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). Os logs históricos dessas operações estão disponíveis no portal e por meio da CLI; consulte [Azure Resource Manager operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [AZ monitor Activity-log](/cli/azure/monitor/activity-log). |
| Log e auditoria do plano de dados | Não | O plano de dados para o serviço de aplicativo é um compartilhamento de arquivo remoto que contém o conteúdo do site implantado de um cliente.  Não há auditoria do compartilhamento de arquivos remoto. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de serviço de aplicativo pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões dinâmicas diferentes de um aplicativo usando o recurso de Slots de implantação do serviço de aplicativo. | 

