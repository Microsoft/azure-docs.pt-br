---
title: Recomendações de segurança para armazenamento de filas
titleSuffix: Azure Storage
description: Saiba mais sobre as recomendações de segurança para o armazenamento de filas. Implementar essas diretrizes ajudará você a atender suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592153"
---
# <a name="security-recommendations-for-queue-storage"></a>Recomendações de segurança para armazenamento de filas

Este artigo contém recomendações de segurança para o armazenamento de filas. Implementar essas recomendações ajudará a atender as obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades do provedor de serviços, consulte [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Algumas das recomendações incluídas neste artigo podem ser monitoradas automaticamente pela Central de Segurança do Azure. A Central de Segurança do Azure é a primeira linha de defesa na proteção dos recursos no Azure. Para obter informações sobre a central de segurança do Azure, consulte [o que é a central de segurança do Azure?](../../security-center/security-center-introduction.md).

A central de segurança do Azure analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ela fornece recomendações sobre como solucioná-las. Para obter mais informações sobre as recomendações da Central de Segurança do Azure, confira [Recomendações de segurança na Central de Segurança do Azure](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Usar o modelo de implantação Azure Resource Manager | Crie novas contas de armazenamento usando o modelo de implantação Azure Resource Manager para aprimoramentos de segurança importantes, incluindo controle de acesso baseado em função do Azure (RBAC do Azure) e auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso a Azure Key Vault para segredos e autenticação e autorização baseadas no Azure AD para acesso aos dados e recursos do armazenamento do Azure. Se possível, migre as contas de armazenamento existentes que usam o modelo de implantação clássico para usar Azure Resource Manager. Para obter mais informações sobre Azure Resource Manager, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/management/overview.md). | - |
| Habilitar a proteção avançada contra ameaças para todas as suas contas de armazenamento | A proteção avançada contra ameaças para o armazenamento do Azure fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de segurança são disparados na central de segurança do Azure quando ocorrem anomalias na atividade e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. Para obter mais informações, consulte [proteção avançada contra ameaças para o armazenamento do Azure](../common/azure-defender-storage-configure.md). | [Sim](../../security-center/security-center-remediate-recommendations.md) |
| Limitar tokens de SAS (assinatura de acesso compartilhado) a conexões HTTPS somente | Exigir HTTPS quando um cliente usa um token SAS para acessar os dados da fila ajuda a minimizar o risco de espionagem. Para obter mais informações, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando assinaturas de acesso compartilhado (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Usar Azure Active Directory (AD do Azure) para autorizar o acesso aos dados da fila | O Azure AD fornece segurança superior e facilidade de uso sobre a autorização de chave compartilhada para autorizar solicitações para o armazenamento de filas. Para obter mais informações, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md). | - |
| Tenha em mente a entidade de privilégio mínimo ao atribuir permissões a uma entidade de segurança do Azure AD por meio do RBAC do Azure | Ao atribuir uma função a um usuário, grupo ou aplicativo, conceda a essa entidade de segurança somente as permissões necessárias para que elas executem suas tarefas. Limitar o acesso a recursos ajuda a impedir o uso indevido intencional e mal-intencionado dos seus dados. | - |
| Proteger as chaves de acesso da sua conta com o Azure Key Vault | A Microsoft recomenda usar o Azure AD para autorizar solicitações ao armazenamento do Azure. No entanto, se você precisar usar a autorização de chave compartilhada, proteja as chaves de sua conta com Azure Key Vault. Você pode recuperar as chaves do cofre de chaves em tempo de execução, em vez de salvá-las com seu aplicativo. | - |
| Regenerar as chaves de conta periodicamente | Girar as chaves de conta periodicamente reduz o risco de expor seus dados para atores mal-intencionados. | - |
| Tenha em mente a entidade de privilégio mínimo ao atribuir permissões a uma SAS | Ao criar uma SAS, especifique somente as permissões exigidas pelo cliente para executar sua função. Limitar o acesso a recursos ajuda a impedir o uso indevido intencional e mal-intencionado dos seus dados. | - |
| Ter um plano de revogação em vigor para qualquer SAS que você emite para clientes | Se uma SAS for comprometida, você desejará revogar essa SAS assim que possível. Para revogar uma SAS de delegação de usuário, revogue a chave de delegação de usuário para invalidar rapidamente todas as assinaturas associadas a essa chave. Para revogar uma SAS de serviço associada a uma política de acesso armazenada, você pode excluir a política de acesso armazenada, renomear a política ou alterar sua hora de expiração para uma hora que esteja no passado. Para obter mais informações, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando assinaturas de acesso compartilhado (SAS)](../common/storage-sas-overview.md).  | - |
| Se uma SAS de serviço não estiver associada a uma política de acesso armazenada, defina a hora de expiração para uma hora ou menos | Uma SAS de serviço que não está associada a uma política de acesso armazenada não pode ser revogada. Por esse motivo, limitando o tempo de expiração para que a SAS seja válida por uma hora ou menos, é recomendável. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Configure a versão mínima necessária da TLS (segurança da camada de transporte) para uma conta de armazenamento.  | Exigir que os clientes usem uma versão mais segura do TLS para fazer solicitações em uma conta de armazenamento do Azure Configurando a versão mínima do TLS para essa conta. Para obter mais informações, consulte [Configurar a versão mínima necessária da TLS (segurança da camada de transporte) para uma conta de armazenamento](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| Habilitar a opção **transferência segura necessária** em todas as suas contas de armazenamento | Quando você habilita a opção **transferência segura necessária** , todas as solicitações feitas na conta de armazenamento devem ocorrer em conexões seguras. Todas as solicitações feitas por HTTP falharão. Para obter mais informações, consulte [exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Sim](../../security-center/security-center-remediate-recommendations.md) |
| Habilitar regras de firewall | Configure as regras de firewall para limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços IP ou intervalos especificados, ou de uma lista de sub-redes em uma VNet (rede virtual) do Azure. Para obter mais informações sobre como configurar regras de firewall, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Permitir que serviços da Microsoft confiáveis acessem a conta de armazenamento | A ativação de regras de firewall para sua conta de armazenamento bloqueia solicitações de entrada de dados por padrão, a menos que as solicitações sejam originadas de um serviço operando em uma VNet do Azure ou de endereços IP públicos permitidos. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante. Você pode permitir solicitações de outros serviços do Azure adicionando uma exceção para permitir que serviços confiáveis da Microsoft acessem a conta de armazenamento. Para obter mais informações sobre como adicionar uma exceção para serviços confiáveis da Microsoft, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Usar pontos de extremidade privados | Um ponto de extremidade privado atribui um endereço IP privado de sua VNet do Azure à conta de armazenamento. Ele protege todo o tráfego entre sua VNet e a conta de armazenamento sobre o link privado. Para obter mais informações sobre pontos de extremidade privados, consulte [conectar-se de forma privada a uma conta de armazenamento usando um ponto de extremidade privado do Azure](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Usar marcas de serviço de VNet | Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços. Para obter mais informações sobre marcas de serviço com suporte no armazenamento do Azure, consulte [visão geral das marcas de serviço do Azure](../../virtual-network/service-tags-overview.md). Para obter um tutorial que mostra como usar marcas de serviço para criar regras de rede de saída, consulte [restringir o acesso aos recursos de PaaS](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Limitar o acesso à rede para redes específicas | Limitar o acesso à rede para redes que hospedam clientes que exigem acesso reduz a exposição de seus recursos a ataques de rede. | [Sim](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Log e monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Controlar como as solicitações são autorizadas | Habilite o log de armazenamento do Azure para controlar como cada solicitação feita no armazenamento do Azure foi autorizada. Os logs indicam se uma solicitação foi feita anonimamente, usando um token OAuth 2,0, usando uma chave compartilhada ou usando uma SAS (assinatura de acesso compartilhado). Para obter mais informações, consulte [registro em log da análise de armazenamento do Azure](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Próximas etapas

- [Documentação de segurança do Azure](../../security/index.yml)
- [Documentação de desenvolvimento seguro](../../security/develop/index.yml).
