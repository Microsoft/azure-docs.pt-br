---
title: Configurar o Cloud App Discovery no Azure Active Directory | Microsoft Docs
description: "Fornece informações sobre a localização e o gerenciamento de aplicativos com o Cloud App Discovery, quais são seus benefícios e como ele funciona."
services: active-directory
keywords: cloud app discovery, gerenciamento de aplicativos
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: af54b77dc985f2ca6abeab29165278dfa598f5e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Configurar o Cloud App Discovery no Azure AD

Os novos aprimoramentos do Cloud App Discovery no Azure AD agora estão disponível com uma licença do Azure Active Directory Premium P1. Esses aprimoramentos são baseados na integração com o Microsoft Cloud App Security. O Cloud App Discovery compara os logs de tráfego ao catálogo de mais de 15.000 aplicativos de nuvem do Cloud App Security para fornecer informações em tempo real sobre o uso de nuvem e TI sombra. 

## <a name="prerequisites"></a>Pré-requisitos

Sua organização deve ter uma licença Azure AD Premium P1 para usar o produto. Para obter mais informações, consulte [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para configurar o Cloud App Discovery, você deve ser um Administrador Global ou um Leitor de Segurança no Azure Active Directory. Um usuário a quem uma função de administrador é atribuída tem as mesmas permissões em todos os serviços de nuvem assinados pela sua organização.

## <a name="setup-steps"></a>Etapas de instalação

1. [Configurar relatórios de instantâneo](cloudappdiscovery-set-up-snapshots.md) para conferir seu formato de log e verificar se os logs fornecem informações úteis ao Cloud App Discovery. Eles também podem fornecer visibilidade ad hoc nos logs de tráfego que você carrega manualmente dos seus firewalls e servidores proxy.

2. [Configure relatórios contínuos](https://docs.microsoft.com/cloud-app-security/discovery-docker) para analisar todos os logs que são encaminhados da sua rede usando o coletor de logs do Cloud App Security. Você pode usá-los para identificar tendências de uso e aplicativos novos.

3. Se os logs não têm suporte no momento, [configure um analisador de logs personalizado](https://docs.microsoft.com/en-us/cloud-app-security/custom-log-parser) para que o Cloud App Discovery possa analisá-los.
  
## <a name="log-processing-flow"></a>Fluxo de processamento de log

A geração de relatórios pode levar de alguns minutos a várias horas, dependendo da quantidade de dados. Eis o que é analisado:

* **Carregar** Logs de tráfego da Web da sua rede são carregados no portal.
* **Analisar** O Cloud App Security analisa e extrai dados de tráfego dos logs de tráfego com um analisador dedicado para cada fonte de dados.
* **Analisar** Os dados de tráfego são analisados frente ao catálogo do Cloud App Security para identificar mais de 15.000 aplicativos de nuvem. Usuários ativos e endereços IP também são identificados como parte da análise.
* **Gerar relatório** O Cloud App Security gera um relatório dos dados extraídos dos arquivos de log e torna-o disponível para o Cloud App Discovery.

> [!NOTE]
> * Dados de relatório contínuos são analisados duas vezes por dia.
> * O coletor de logs compacta os dados antes de eles serem carregados. O tráfego de saída no coletor de logs é de cerca de 10% do tamanho dos logs de tráfego recebidos.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Usando logs de tráfego para o Cloud App Discovery

O Cloud App Discovery usa os dados em seus logs de tráfego. Quanto mais detalhes você puder adicionar em seu registro, melhor será a visibilidade obtida. O Cloud App Discovery requer dados de tráfego da Web com os seguintes atributos:

* Data da transação
* Endereço IP de origem
* Usuário de origem **recomendado**
* Endereço IP de destino
* URL de destino **recomendado** (para detecção de aplicativos de nuvem, URLs oferecem mais precisão do que endereços IP)
* Quantidade total de dados
* Quantidade de dados carregados ou baixados para informações sobre padrões de uso de aplicativos de nuvem
* Ação realizada (permitido/bloqueado)

O Cloud App Discovery não pode mostrar ou analisar atributos que não estão incluídos nos logs. Por exemplo, o formato de log padrão **Firewall do Cisco ASA** não contém a **Quantidade de bytes carregados por transação**, o **Nome de usuário** nem a **URL de destino**, mas apenas o endereço IP de destino. Assim, você terá menos visibilidade sobre os aplicativos de nuvem dessa fonte de dados. Para firewalls do Cisco ASA, defina o nível de informações para 6.1.

Para gerar um relatório do Cloud App Discovery com êxito, seus logs de tráfego devem atender às seguintes condições:

1.  A fonte de dados é [um servidor proxy ou firewall com suporte](#supported-firewalls-and-proxies).
2.  O formato do log corresponde ao formato padrão esperado. Isso é verificado no momento do upload. Para otimizar o formato de log, consulte [Criar relatórios de instantâneo do Cloud App Discovery](cloudappdiscovery-set-up-snapshots.md).
3.  Os eventos não têm mais de 90 dias.
4.  O arquivo de log é válido e inclui informações do tráfego de saída.

## <a name="supported-firewalls-and-proxy-servers"></a>Firewalls e servidores proxy com suporte

* Barracuda – firewall do aplicativo Web (W3C)
* Blue Coat Proxy SG – log de acesso (W3C)
* Ponto de Verificação
* Firewall do Cisco ASA (para firewalls do Cisco ASA, defina o nível de informações para 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – log de URLs
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Firewall da série Palo Alto
* Sophos SG
* Sophos Cyberoam
* Squid (comum)
* Squid (nativo)
* Websense – soluções de segurança da Web – relatório de detalhes de investigação (CSV)
* Websense – soluções de segurança da Web – log de atividades de Internet (CEF)
* Zscaler

> [!NOTE]
> O Cloud App Discovery dá suporte a endereços IPv4 e IPv6.

Se não há suporte para o seu log, selecione **Outro** como a **Fonte de dados** e especifique o dispositivo e o log que você está tentando carregar. O log é examinado pela equipe de analistas de nuvem do Cloud App Security. Quando o suporte para o tipo de log for adicionado nós lhe notificaremos, mas em vez disso, você pode definir um analisador personalizado que corresponda ao seu formato de log. Para obter mais informações, consulte [Usar um analisador de log personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Atributos de dados (de acordo com a documentação do fornecedor)

| Fonte de dados         | URL do aplicativo de destino | Endereço IP do aplicativo de destino | Nome de Usuário | Endereço IP de origem | Tráfego total | Bytes carregados |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Sim**        | **Sim**       | **Sim**  | **Sim**   | Não            | Não             |
| Blue Coat                               | **Sim**        | Não            | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Ponto de verificação                              | Não             | **Sim**       | Não       | **Sim**   | Não            | Não             |
| Cisco ASA                               | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | Não             |
| Cisco FWSM                              | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | Não             |
| Cisco Ironport WSA                      | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Cisco Meraki                            | **Sim**        | **Sim**       | Não       | **Sim**   | Não            | Não             |
| Clavister NGFW (Syslog)                 | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Dell SonicWall                          | **Sim**        | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| FortiGate                               | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| Juniper SRX                             | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| Juniper SSG                             | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| McAfee SWG                              | **Sim**        | Não            | Não       | **Sim**   | **Sim**       | **Sim**        |
| MS TMG                                  | **Sim**        | Não            | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Redes Palo Alto                      | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Sophos                                  | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | Não             |
| Squid (comum)                          | **Sim**        | Não            | **Sim**  | **Sim**   | Não            | **Sim**        |
| Squid (nativo)                          | **Sim**        | Não            | **Sim**  | **Sim**   | Não            | **Sim**        |
| Websense – relatório de investigação (CSV)   | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Websense – log de atividades de Internet (CEF)  | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Zscaler                                 | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |


## <a name="next-steps"></a>Próximas etapas
Use os links a seguir para continuar a configurar o Cloud App Discovery no Azure AD.

* [Criar relatórios de instantâneo](cloudappdiscovery-set-up-snapshots.md)
* [Configurar relatório contínuo](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Usar um analisador de log personalizado](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)