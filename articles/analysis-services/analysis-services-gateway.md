---
title: Gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Um gateway local será necessário se o servidor do Analysis Services no Azure for se conectar a fontes de dados locais.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739596"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Conectando-se a fontes de dados locais com o gateway de dados local

O gateway de dados local fornece transferência de dados segura entre fontes de dados locais e seus servidores do Azure Analysis Services na nuvem. Além de trabalhar com vários servidores de Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com aplicativos lógicos do Azure, Power BI, Power apps e automação de energia. Embora o gateway que você instalar seja o mesmo em todos esses serviços, Azure Analysis Services e aplicativos lógicos têm algumas etapas adicionais.

As informações fornecidas aqui são específicas de como Azure Analysis Services funciona com o gateway de dados local. Para saber mais sobre o gateway em geral e como ele funciona com outros serviços, consulte [o que é um gateway de dados local?](/data-integration/gateway/service-gateway-onprem).

Por Azure Analysis Services, obter a configuração com o gateway na primeira vez é um processo de quatro partes:

- **Baixar e executar a instalação** – esta etapa instala um serviço de gateway em um computador em sua organização. Você também pode entrar no Azure usando uma conta no Azure AD do seu [locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Não há suporte para contas B2B (convidadas) do Azure.

- **Registrar seu gateway** – nesta etapa, você especifica um nome e uma chave de recuperação para o gateway e seleciona uma região, registrando o gateway no Serviço de Nuvem do Gateway. O recurso de gateway pode ser registrado em qualquer região, mas é recomendável que ele esteja na mesma região que seus servidores de Analysis Services. 

- **Criar um recurso de gateway no Azure** – nesta etapa, você cria um recurso de gateway no Azure.

- **Conectar o recurso de gateway aos servidores** -assim que tiver um recurso de gateway, você poderá começar a conectar servidores a ele. Você pode conectar vários servidores e outros recursos fornecidos que estão na mesma região.

## <a name="installing"></a>Instalando

Ao instalar o para um ambiente de Azure Analysis Services, é importante seguir as etapas descritas em [instalar e configurar o gateway de dados local para Azure Analysis Services](analysis-services-gateway-install.md). Este artigo é específico para Azure Analysis Services. Ele inclui etapas adicionais necessárias para configurar um recurso de gateway de dados local no Azure e conectar seu servidor de Azure Analysis Services ao recurso.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Conectando-se a um recurso de gateway em uma assinatura diferente

É recomendável que você crie o recurso de gateway do Azure na mesma assinatura que o servidor. No entanto, você pode configurar seus servidores para se conectar a um recurso de gateway em outra assinatura. Não há suporte para a conexão com um recurso de gateway em outra assinatura ao definir configurações de servidor existentes ou para criar um novo servidor no portal, mas pode ser configurado usando o PowerShell. Para saber mais, consulte [conectar o recurso de gateway ao servidor](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma conexão de saída para o Barramento de Serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354.  O gateway não exige portas de entrada.

Talvez seja necessário incluir endereços IP para sua região de dados em seu firewall. Baixe a [lista de IPs de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=56519). A lista é atualizada semanalmente. Os Endereços IP listados na lista de IP de Datacenter do Azure estão em notação CIDR. Para saber mais, consulte [Roteamento entre Domínios sem Classificação](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Estes são os nomes de domínio totalmente qualificados usados pelo Gateway.

| Nomes de domínios | Portas de saída | Descrição |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usado para baixar o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Usado para testar a conectividade com a Internet se o gateway não poder ser acessado pelo serviço do Power BI. |
| *.microsoftonline p.com |443 |Usado para autenticação, dependendo da configuração. |
| dc.services.visualstudio.com    |443 |Usado pelo AppInsights para coletar telemetria. |

## <a name="next-steps"></a>Próximas etapas 

Os artigos a seguir estão incluídos no conteúdo geral do gateway de dados local que se aplica a todos os serviços aos quais o gateway dá suporte:

* [Perguntas frequentes de gateway de dados no local](/data-integration/gateway/service-gateway-onprem-faq)   
* [Usar o aplicativo de gateway de dados local](/data-integration/gateway/service-gateway-app)   
* [Administração de nível de locatário](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Definir configurações de proxy](/data-integration/gateway/service-gateway-proxy)   
* [Ajustar as configurações de comunicação](/data-integration/gateway/service-gateway-communication)   
* [Configurar arquivos de log](/data-integration/gateway/service-gateway-log-files)   
* [Solucionar problemas](/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](/data-integration/gateway/service-gateway-performance)
