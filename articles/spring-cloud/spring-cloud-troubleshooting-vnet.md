---
title: Solução de problemas do Azure Spring Cloud na rede virtual
description: Guia de solução de problemas da rede virtual do Azure Spring Cloud.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 8491eb02fd61f291904d57990b3785ce93239964
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935270"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Solução de problemas do Azure Spring Cloud em redes virtuais

Este documento ajudará você a resolver vários problemas que podem surgir ao usar o Azure Spring Cloud em redes virtuais.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Eu encontrei um problema ao criar uma instância do serviço de nuvem do Azure Spring

Para criar uma instância do Azure Spring Cloud, você deve ter permissão suficiente para implantar a instância na rede virtual.  A instância do serviço Spring Cloud deve, em si, [conceder a permissão de serviço de nuvem Spring do Azure para a rede virtual](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Se você usar o portal do Azure para configurar a instância do serviço de nuvem do Azure Spring, o portal do Azure validará as permissões.

Para configurar a instância do serviço de nuvem do Azure Spring usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), verifique se:

- A assinatura está ativa.
- O local é suportado pelo Azure Spring Cloud.
- O grupo de recursos da instância já foi criado.
- O nome do recurso está em conformidade com a regra de nomenclatura. Ele deve conter apenas letras minúsculas, números e hifens. O primeiro caractere deve ser uma letra. O último caractere deve ser uma letra ou um número. O valor deve conter de 2 a 32 caracteres.

Para configurar a instância do serviço de nuvem do Azure Spring usando o modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

### <a name="common-creation-issues"></a>Problemas comuns de criação

| Mensagem de erro | Como corrigir |
|------|------|
| Os recursos criados pelo Azure Spring Cloud não foram permitidos pela política. | Os recursos de rede serão criados durante a implantação do Azure Spring Cloud em sua própria rede virtual. Verifique se você tem [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) definido para bloquear a criação. Os recursos que não puderam ser criados podem ser encontrados na mensagem de erro. |
| As sub-redes fornecidas foram associadas a tabelas de rotas, desassocie-as. | Atualmente, não há suporte para implantar o Azure Spring Cloud na sub-rede associada às tabelas de rotas existentes, dissocia-as e tente novamente. |
| O tráfego necessário não é allowlisted. | Consulte as [responsabilidades do cliente para executar o Azure Spring Cloud na VNET](spring-cloud-vnet-customer-responsibilities.md) para garantir que o tráfego necessário seja allowlisted. |

## <a name="my-application-cant-be-registered"></a>Meu aplicativo não pode ser registrado

Esse problema ocorre se sua rede virtual estiver configurada com configurações de DNS personalizadas. Nesse caso, a zona DNS privada usada pelo Azure Spring Cloud é ineficaz. Adicione o 168.63.129.16 de IP do DNS do Azure como o servidor DNS upstream no servidor DNS personalizado.

## <a name="other-issues"></a>Outros problemas

[Solucionar problemas comuns do Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-troubleshoot).