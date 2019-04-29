---
title: Atributos de segurança para serviços do Azure
description: Uma lista de verificação de atributos de segurança comuns para avaliar o Microsoft Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610003"
---
# <a name="common-security-attributes-for-azure-services"></a>Atributos de segurança comuns para serviços do Azure

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo coleta os atributos de segurança comuns para serviços do Azure selecionados. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Serviço de Backup do Azure](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | Usar a criptografia do serviço de armazenamento para contas de armazenamento. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Não  | Usar HTTPS. |
| Manipulação de chaves de criptografia (CMK, BYOK, etc.)| Não  |  |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| Não  |  |
| Chamadas criptografadas à API| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Status do ponto de extremidade de serviço| Não  |  |
| Suporte à injeção de VNET| Não  |  |
| Isolamento de rede/Suporte a firewall| Sim | Há suporte para túnel forçado no backup de VM. Não há suporte para túnel forçado em cargas de trabalho em execução dentro de VMs. |
| Suporte para túnel forçado | Não  |  |

### <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Há suporte para o Log Analytics por meio dos logs de diagnóstico. Confira Monitorar cargas de trabalho protegidas do Backup do Azure usando o Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Gerenciamento de acesso - Autorização| Sim | São usadas regras de RBAC internas e criadas pelo cliente. Consulte controle de acesso baseado em função para gerenciar pontos de recuperação de Backup do Azure (/ azure/backup/rbac-rs-Cofre de backup) para obter mais informações. |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Todas as ações disparadas pelo cliente no portal do Azure são registradas em logs de atividades. |
| Registro em log e auditoria de plano de dados| Não  | O plano de dados do Backup do Azure não pode ser acessado diretamente.  |

### <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Cofre da Chave do Azure](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | Todos os objetos são criptografados. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Toda a comunicação é por meio de chamadas à API criptografadas |
| Manipulação de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente controla todas as chaves em seu Key Vault. Quando as chaves de HSM (módulo) com suporte de segurança de hardware são especificadas, um HSM do FIPS nível 2 protege a chave, o certificado ou o segredo. |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Usando HTTPS. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte para ponto de extremidade de serviço| Sim | Usando pontos de extremidade de serviço de Rede Virtual (VNet). |
| Suporte à injeção de vNET| Não  |  |
| Isolamento de rede/Suporte a firewall| Sim | Usando regras de firewall de rede virtual. |
| Suporte para túnel forçado | Não  |  |

### <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Usando o Log Analytics. |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Gerenciamento de acesso - Autorização| Sim | Usando a política de acesso ao Key Vault. |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Usando o Log Analytics. |
| Registro em log e auditoria de plano de dados| Sim | Usando o Log Analytics. |

### <a name="access-controls"></a>Controles de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Controles de acesso do plano de controle/gerenciamento | Sim | RBAC (Controle de Acesso Baseado em Função) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso ao Key Vault |

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | O cliente é proprietário do cluster e do conjunto de dimensionamento de VMs (máquinas virtuais) em que o cluster se baseia. Criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquina virtual. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim |  |
| Manipulação de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente é proprietário do cluster e do conjunto de dimensionamento de VMs (máquinas virtuais) em que o cluster se baseia. Criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquina virtual. |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Chamadas à API do Service Fabric são feitas por meio do Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte para ponto de extremidade de serviço| Sim |  |
| Suporte a injeção de VNET| Sim |  |
| Isolamento de rede/suporte a firewall| Sim | Usando NSGs (Grupos de Segurança de Rede). |
| Suporte para túnel forçado | Sim | A rede do Azure fornece túnel forçado. |

### <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Usando o suporte de terceiros e suporte de monitoramento do Azure. |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Gerenciamento de acesso - Autorização| Sim | IAM (Gerenciamento de identidade e de acesso) para chamadas via SFRP. Chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer uma dessas duas funções. |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Todas as operações de plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Registro em log e auditoria de plano de dados| N/D | O cliente é proprietário do cluster.  |

### <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | O controle de versão e a implantação da configuração de serviço são realizados pelo Azure Deploy. O controle de versão do código (aplicativo e tempo de execução) é realizado usando o Azure Build.
 |

## <a name="azure-storage"></a>Armazenamento do Azure

### <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim |  |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Suporte a mecanismos HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de ser transmitido para o serviço. |
| Manipulação de chaves de criptografia (CMK, BYOK, etc.)| Sim | Ver [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Criptografia de nível de coluna (Serviços de Dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim |  |

### <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte para ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNET| N/D |  |
| Isolamento de rede/Suporte a firewall| Sim | |
| Suporte para túnel forçado | N/D |  |

### <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Métricas do Azure Monitor disponível agora, registra a visualização inicial |

### <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | Azure Active Directory, chave compartilhada, o token de acesso compartilhado. |
| Gerenciamento de acesso - Autorização| Sim | Autorização de suporte por meio de RBAC, ACLs POSIX e Tokens de SAS |


### <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento | Sim | Log de atividades do Azure Resource Manager |
| Registro em log e auditoria de plano de dados| Sim | Os Logs de diagnóstico do serviço e registro em log do Azure Monitor visualização inicial  |

### <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Suporte a controle de versão do provedor de recursos por meio de APIs do Gerenciador de recursos do Azure |