---
title: Atributos de segurança para Azure Cosmos DB
description: Uma lista de verificação de atributos de segurança para avaliar Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442547"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Atributos de segurança para Azure Cosmos DB

Este artigo documenta os atributos de segurança internos no Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | Todos os bancos de dados e backups do Cosmos DB são criptografados por padrão; consulte [criptografia de dados em Azure Cosmos DB](database-encryption-at-rest.md). Não há suporte para a criptografia do lado do servidor com chaves gerenciadas pelo cliente. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Todos os dados de Azure Cosmos DB são criptografados em trânsito. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Sim | Somente na API de tabelas Premium. Nem todas as APIs dão suporte a esse recurso. Consulte [introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md). |
| Chamadas criptografadas à API| Sim | Todas as conexões com Azure Cosmos DB dão suporte a HTTPS. O Azure Cosmos DB também dá suporte a conexões TLS 1,2, mas isso ainda não é imposto. Se os clientes desligarem o TLS de nível inferior em seu final, eles poderão se conectar ao Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim | Com o ponto de extremidade do serviço VNet, você pode configurar uma conta de Azure Cosmos DB para permitir o acesso somente de uma sub-rede específica de uma rede virtual (VNet). Você também pode combinar o acesso VNet com as regras de firewall.  Consulte [acessar Azure Cosmos DB de redes virtuais](VNet-service-endpoint.md). |
| Isolamento de rede e suporte de firewall| Sim | Com o suporte a firewall, você pode configurar sua conta do Azure Cosmos para permitir o acesso somente de um conjunto aprovado de endereços IP, um intervalo de endereços IP e/ou serviços de nuvem. Consulte [Configurar o firewall de IP no Azure Cosmos DB](how-to-configure-firewall.md).|
| Suporte a túnel forçado| Sim | Pode ser configurado no lado do cliente na VNet em que as máquinas virtuais estão localizadas.   |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Todas as solicitações enviadas para Azure Cosmos DB são registradas. Há suporte para o [monitoramento do Azure](../azure-monitor/overview.md), as métricas do Azure, o log de auditoria do Azure.  Você pode registrar informações correspondentes às solicitações de plano de dados, estatísticas de tempo de execução de consulta, texto de consulta, solicitações do MongoDB. Você também pode configurar alertas. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/não | Observações|
|---|---|--|
| Autenticação| Sim | Sim, no nível da conta do banco de dados; no nível do plano de dados, Cosmos DB usa tokens de recurso e acesso à chave. |
| Autorização| Sim | Com suporte na conta do Azure cosmos com chaves mestras (primária e secundária) e tokens de recurso. Você pode obter acesso de leitura/gravação ou somente leitura aos dados com chaves mestras. Tokens de recurso permitem acesso de tempo limitado a recursos como documentos e contêineres. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades do Azure para operações de nível de conta, como firewalls, VNets, acesso de chaves e IAM. |
| Log e auditoria do plano de dados | Sim | Diagnóstico monitoramento de log para operações em nível de contêiner, como criar contêiner, provisionar taxa de transferência, políticas de indexação e operações CRUD em documentos. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Não  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Atributos de segurança adicionais para Cosmos DB

| Atributo de segurança | Sim/não | Observações|
|---|---|--|
| CORS (compartilhamento de recursos entre origens) | Sim | Consulte [Configurar o CORS (compartilhamento de recursos entre origens)](how-to-configure-cross-origin-resource-sharing.md). |
