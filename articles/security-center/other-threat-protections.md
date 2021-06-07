---
title: Proteção adicional contra ameaças na Central de Segurança do Azure
description: Saiba mais sobre a proteção contra ameaças disponível na Central de Segurança do Azure além do Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 5b9e9f5cc5184a083f45999eaeb031eb83b17c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754327"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Proteções contra ameaças adicionais na Central de Segurança do Azure
Assim como as [proteções internas do Azure Defender](azure-defender.md), a Central de Segurança do Azure também oferece as funcionalidades de proteção contra ameaças a seguir.

> [!TIP]
> Para habilitar as funcionalidades de proteção contra ameaças da Central de Segurança, você precisará habilitar o Azure Defender na assinatura que contém as cargas de trabalho aplicáveis.
>
> Habilite a proteção contra ameaças do **Banco de Dados do Azure para MariaDB/MySQL/PostgreSQL** apenas no nível do recurso.


## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para a camada de rede do Azure <a name="network-layer"></a>
Os recursos de análise da camada de rede da Central de Segurança são baseados em [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) de amostra, que são os cabeçalhos de pacote coletados pelos roteadores principais do Azure. Com base nesse feed de dados, a Central de Segurança usa modelos de aprendizado de máquina para identificar e sinalizar atividades de tráfego mal-intencionadas. A Central de Segurança também usa o banco de dados de inteligência contra ameaças da Microsoft para aprimorar os endereços IP.

Algumas configurações de rede impedem que a Central de Segurança gere alertas sobre atividades de rede suspeitas. Para que a Central de Segurança gere alertas de rede, verifique se:
- Sua máquina virtual tem um endereço IP público (ou está em um balanceador de carga com um endereço IP público).
- O tráfego de saída da rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

Para obter uma lista dos alertas da camada de rede do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Proteção contra ameaças do Azure Resource Manager (versão prévia)<a name ="management-layer"></a>
A camada de proteção da Central de Segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A Central de Segurança oferece uma camada adicional de proteção usando eventos do Azure Resource Manager, que é considerado o plano de controle do Azure. Ao analisar os registros de Azure Resource Manager, a Central de Segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

Para obter uma lista dos alertas do Azure Defender para Resource Manager, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-resourcemanager).


>[!NOTE]
> Várias das análises anteriores são alimentadas pelo Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença do Cloud App Security. Se você tiver uma licença do Cloud App Security, esses alertas serão habilitados por padrão. Para desabilitar os alertas:
>
> 1. No menu da Central de Segurança, selecione **Preço e configurações**.
> 1. Selecione a assinatura que deseja alterar.
> 1. Selecione **Detecção de ameaças**.
> 1. Desmarque a opção **Permitir que o Microsoft Cloud App Security acesse meus dados** e selecione **Salvar**.


>[!NOTE]
>A Central de Segurança armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a Central de Segurança na área geográfica do recurso, ela armazenará os dados no Estados Unidos. Quando o Cloud App Security está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica do Cloud App Security. Para saber mais, confira [Armazenamento de dados para serviços não regionais](https://azuredatacentermap.azurewebsites.net/).

1. Defina o workspace no qual você está instalando o agente. Verifique se que o workspace está na mesma assinatura usada na Central de Segurança e se você tem permissões de leitura/gravação no workspace.

1. Habilite **Azure Defender** e selecione **Salvar**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Proteção contra ameaças do Azure Cosmos DB (versão prévia)<a name="cosmos-db"></a>

Os alertas do Azure Cosmos DB são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos DB.

Para obter mais informações, consulte:

* [Proteção avançada contra ameaças do Azure Cosmos DB (versão prévia)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para o Azure Cosmos DB (versão prévia)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra Ameaças para o Azure WAF <a name="azure-waf"></a>

O Gateway de Aplicativo do Azure oferece um WAF (firewall do aplicativo Web) que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O WAF do Gateway de Aplicativo é baseado Conjunto de Regras Principais 3.0 ou 2.2.9 do Open Web Application Security Project. O WAF é atualizado automaticamente para proteger você contra novas vulnerabilidades. 

Se você tiver uma licença para o Azure WAF, seus alertas do WAF serão transmitidos para a Central de Segurança sem precisar de configurações adicionais. Para saber mais sobre alertas gerados pelo WAF, confira [Regras e grupos de regras CRS do firewall do aplicativo Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra Ameaças para a Proteção contra DDoS <a name="azure-ddos"></a>

Ataques distribuídos de negação de serviço (DDoS) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você estiver movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Os ataques de DDoS podem ter como alvo qualquer ponto de extremidade que possa ser acessado pela Internet.

Para se defender contra ataques de DDoS, compre uma licença para a Proteção contra DDoS do Azure e certifique-se de seguir as práticas recomendadas de design de aplicativo. A Proteção contra DDoS fornece diferentes camadas de serviço. Para saber mais, confira [Visão geral da Proteção contra DDoS do Azure](../ddos-protection/ddos-protection-overview.md).

Para obter uma lista dos alertas da Proteção contra DDoS do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os alertas de segurança desses recursos de proteção contra ameaças, confira os seguintes artigos:

* [Tabela de referência para todos os alertas da Central de Segurança do Azure](alerts-reference.md)
* [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Exportar continuamente os dados da Central de Segurança](continuous-export.md)