---
title: Benchmark de segurança do Azure v2-proteção de dados
description: Azure Security benchmark v2 de proteção de dados
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 687c344aefc70729c85fb37d615ec0a272ff4fde
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368861"
---
# <a name="security-control-v2-data-protection"></a>Controle de segurança v2: proteção de dados

A proteção de dados aborda o controle de proteção de dados em repouso, em trânsito e por meio de mecanismos de acesso autorizados. Isso inclui descobrir, classificar, proteger e monitorar ativos de dados confidenciais usando o controle de acesso, a criptografia e o registro em log no Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: descobrir, classificar e rotular dados confidenciais

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Descubra, classifique e rotule seus dados confidenciais para que você possa criar os controles apropriados para garantir que as informações confidenciais sejam armazenadas, processadas e transmitidas com segurança pelos sistemas de tecnologia da organização. 

Use a Proteção de Informações do Azure (e a ferramenta de verificação associada) para obter informações confidenciais em documentos do Office no Azure, no local, no Office 365 e em outras localizações. 

Use a Proteção de Informações do SQL do Azure para auxiliar na classificação e na rotulagem das informações armazenadas em Bancos de Dados SQL do Azure.

- [Marcar informações confidenciais usando a Proteção de Informações do Azure](/azure/information-protection/what-is-information-protection) 

- [Como implementar a descoberta de dados do SQL do Azure](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Segurança de dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Proteja dados confidenciais restringindo o acesso usando o controle de acesso baseado em função do Azure (RBAC do Azure), controles de acesso baseados em rede e controles específicos nos serviços do Azure (como criptografia em SQL e outros bancos de dados). 

Para garantir um controle de acesso consistente, todos os tipos de controle de acesso devem ser alinhados à sua estratégia de segmentação corporativa. A estratégia de segmentação corporativa também deve ser informada pela localização de sistemas e dados confidenciais e comercialmente críticos.

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e fornece proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou funcionalidades e controles padrão de proteção de dados.

- [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md)

- [Entender a proteção de dados do cliente no Azure](../fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: monitorar a transferência não autorizada de dados confidenciais

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Monitore a transferência não autorizada de dados para locais fora da visibilidade e controle corporativos. Isso normalmente envolve o monitoramento de atividades anormais (transferências grandes ou incomuns) que podem indicar exfiltração não autorizada dos dados. 

O ATP (Proteção Avançada contra Ameaças) do Armazenamento do Azure e o ATP do SQL do Azure podem alertar você sobre a transferência anormal de informações, que podem indicar transferências não autorizadas de informações confidenciais. 

A AIP (Proteção de Informações do Azure) fornece funcionalidades de monitoramento para informações que foram classificadas e rotuladas. 

Se necessário, para a conformidade de DLP (prevenção contra perda de dados), use uma solução de DLP baseada em host para impor controles de detecção e/ou prevenção a fim de evitar a exfiltração dos dados.

- [Habilitar o ATP do SQL do Azure](../../azure-sql/database/threat-detection-overview.md)

- [Habilitar o ATP do Armazenamento do Azure](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Para complementar os controles de acesso, os dados em trânsito devem ser protegidos contra ataques "fora da banda" (por exemplo, captura de tráfego) usando a criptografia para garantir que os invasores não possam ler ou modificar os dados facilmente. 

Embora isso seja opcional para o tráfego em redes privadas, isso é essencial para o tráfego em redes externas e públicas. Para o tráfego HTTP, verifique se os clientes que se conectam aos recursos do Azure podem negociar TLS v 1.2 ou superior. Para gerenciamento remoto, use SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não criptografado. As versões e os protocolos de SSL, TLS e SSH obsoletos e as codificações fracas devem ser desabilitadas.  

Por padrão, o Azure fornece criptografia para dados em trânsito entre os data centers do Azure. 

- [Entender a criptografia em trânsito com o Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informações sobre a Segurança do TLS](/security/engineering/solving-tls1-problem)

- [Criptografia dupla para dados do Azure em trânsito](../fundamentals/double-encryption.md#data-in-transit)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: criptografar dados confidenciais em repouso

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Para complementar os controles de acesso, os dados em repouso devem ser protegidos contra ataques "fora de banda" (como o acesso ao armazenamento subjacente) usando a criptografia. Isso ajuda a garantir que os invasores não possam ler ou modificar os dados facilmente. 

Por padrão, o Azure fornece criptografia para dados em repouso. Para dados altamente confidenciais, você tem opções para implementar criptografia adicional em repouso em todos os recursos do Azure, quando disponível. O Azure gerencia suas chaves de criptografia por padrão, mas o Azure fornece opções para gerenciar suas próprias chaves (chaves gerenciadas pelo cliente) para determinados serviços do Azure.

- [Noções básicas sobre a criptografia em repouso do Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Modelo de criptografia e tabela de gerenciamento de chaves](../fundamentals/encryption-models.md)

- [Criptografia dupla de dados em repouso no Azure](../fundamentals/double-encryption.md#data-at-rest)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Segurança de dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)