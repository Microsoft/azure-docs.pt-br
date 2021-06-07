---
title: Visão geral do HSM Gerenciado do Azure – HSM Gerenciado do Azure | Microsoft Docs
description: O HSM Gerenciado do Azure é um serviço de nuvem que protege suas chaves de criptografia para aplicativos de nuvem.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: b56031d131743a3dc8c97bcd3e85d4653cdd2833
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484176"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>O que é o HSM Gerenciado do Azure Key Vault?

O HSM Gerenciado do Azure Key Vault é um serviço de nuvem em conformidade com os padrões de um único locatário, altamente disponível e totalmente gerenciado que permite proteger chaves criptográficas para seus aplicativos de nuvem usando HSMs validados **FIPS 140-2 Nível 3**.  

## <a name="why-use-managed-hsm"></a>Por que usar o HSM Gerenciado?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>HSM totalmente gerenciado, altamente disponível e de locatário único como serviço

- **Totalmente gerenciado**: o provisionamento, a configuração, a aplicação de patch e a manutenção do HSM são processados pelo serviço. 
- **Altamente disponíveis e com resiliência de zona** (em que há suporte para zonas de disponibilidade): cada cluster do HSM consiste em várias partições do HSM que abrangem pelo menos duas zonas de disponibilidade. Se o hardware falhar, as partições de membro para o cluster do HSM serão migradas automaticamente para nós íntegros.
- **Locatário único**: cada instância do HSM Gerenciado é dedicada a um único cliente e consiste em um cluster de várias partições do HSM. Cada cluster HSM usa um domínio de segurança específico de cliente separado que isola criptograficamente o cluster do HSM de cada cliente.


### <a name="access-control-enhanced-data-protection--compliance"></a>Controle de acesso, proteção avançada de dados e conformidade

- **Gerenciamento centralizado de chaves**: gerencie chaves críticas e de alto valor em uma organização em um único lugar. Com as permissões granulares por chave, controle o acesso a cada chave conforme o princípio de "acesso com privilégios mínimos".
- **Controle de acesso isolado**: o modelo de controle de acesso "RBAC local" do HSM Gerenciado permite que os administradores de cluster do HSM designados tenham controle total sobre os HSMs que nem mesmo administradores de grupo de recursos, assinatura ou grupo de gerenciamento podem substituir.
- **HSMs validados para FIPS 140-2 Nível 3**: proteja seus dados e cumpra os requisitos de conformidade com HSMs validados para FIPS (Federal Information Protection Standard) 140-2 Nível 3. Os HSMs gerenciados usam adaptadores HSM Marvell LiquidSecurity.
- **Monitorar e auditar**: totalmente integrado ao Azure Monitor. Obtenha logs completos de todas as atividades por meio do Azure Monitor. Use o Log Analytics do Azure para análise e alertas.
- **Residência de dados**: o HSM gerenciado não armazena/processa dados do cliente fora da região em que o cliente implanta a instância do HSM.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrado ao Azure e aos serviços de PaaS/SaaS da Microsoft 

- Gere (ou importe usando [BYOK](hsm-protected-keys-byok.md)) chaves e use-las para criptografar seus dados em repouso em serviços do Azure, como [Armazenamento do Azure](../../storage/common/customer-managed-keys-overview.md), [SQL do Azure](../../azure-sql/database/transparent-data-encryption-byok-overview.md) e [Proteção de Informações do Azure](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Usa as mesmas interfaces de API e de gerenciamento que o Key Vault

- Migre facilmente seus aplicativos existentes que usam um cofre (multilocatário) para usar HSMs gerenciados.
- Use os mesmos padrões de desenvolvimento e implantação de aplicativos para todos os aplicativos, não importa a solução de gerenciamento de chaves em uso: cofres multilocatários ou HSMs gerenciados de locatário único

### <a name="import-keys-from-your-on-premise-hsms"></a>Importar chaves de seus HSMs locais

- Gerar chaves protegidas por HSM em seu HSM local e importá-las com segurança para o HSM Gerenciado

## <a name="next-steps"></a>Próximas etapas
- Confira [Início Rápido: Provisionar e ativar um HSM gerenciado usando a CLI do Azure](quick-create-cli.md) para criar e ativar um HSM gerenciado
- Veja as [Melhores práticas usando o HSM gerenciado do Azure Key Vault](best-practices.md)
