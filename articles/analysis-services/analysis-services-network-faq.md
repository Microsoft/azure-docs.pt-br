---
title: Perguntas frequentes sobre Analysis Services conectividade de rede | Microsoft Docs
description: Este artigo fornece respostas para algumas das perguntas mais comuns sobre Analysis Services conectividade de rede.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82838494"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Perguntas frequentes sobre a conectividade de rede do Analysis Services

Este artigo fornece respostas a perguntas comuns sobre como se conectar a contas de armazenamento, fontes de dados, firewalls e endereços IP.

## <a name="backup-and-restore"></a>Backup e restauração

**Pergunta** -como fazer backup e restaurar usando uma conta de armazenamento que está atrás de um firewall?   
A **resposta** Azure Analysis Services não usa endereços IP fixos ou marcas de serviço. O intervalo de endereços IP que seus servidores de Analysis Services usam pode ser qualquer coisa no intervalo de endereços IP para a *região do Azure*. Como os endereços IP do servidor são variáveis e podem mudar ao longo do tempo, suas regras de firewall precisam permitir todo o intervalo de endereços IP de região do Azure no qual seu servidor está.

**Pergunta** – minha conta de armazenamento do Azure está em uma região diferente do meu servidor de Analysis Services. Como fazer definir as configurações de firewall da conta de armazenamento?   
**Resposta** – se a conta de armazenamento estiver em uma região diferente, defina as configurações de firewall da conta de armazenamento para permitir o acesso de **redes selecionadas**. Em **intervalo de endereços**do firewall, especifique o intervalo de endereços IP para a região em que o servidor Analysis Services está. Para obter os intervalos de IP para regiões do Azure, consulte [intervalos de IP e marcas de serviço do Azure – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). A definição de configurações de firewall da conta de armazenamento para permitir o acesso de todas as redes tem suporte, no entanto, escolher redes selecionadas e especificar um intervalo de endereços IP é preferencial. 

**Pergunta** – minha conta de armazenamento do Azure está na mesma região que o meu servidor de Analysis Services. Como posso definir as configurações de firewall da conta de armazenamento?   
**Resposta** – como o servidor Analysis Services e a conta de armazenamento estão na mesma região, as comunicações entre eles usam intervalos de endereços IP internos, portanto, não há suporte para a configuração de um firewall para usar redes selecionadas e especificar um intervalo de endereços IP. Se as políticas da organização exigirem um firewall, ele deverá ser configurado para permitir o acesso de todas as redes.


## <a name="data-source-connections"></a>Conexões de fonte de dados

**Pergunta** – tenho uma VNET para meu sistema de fonte de dados. Como permitir que meus servidores de Analysis Services acessem o banco de dados da VNET?   
O Azure Analysis Services de **resposta** não pode ingressar em uma VNET. A melhor solução aqui é instalar e configurar um gateway de dados local na VNET e, em seguida, configurar seus servidores de Analysis Services com a propriedade de servidor **AlwaysUseGateway** . Para saber mais, confira [usar o gateway para fontes de dados em uma VNet (rede virtual) do Azure](analysis-services-vnet-gateway.md).

**Pergunta** – tenho um banco de dados de origem por trás de um firewall. Como posso configurar o firewall para permitir que meu servidor de Analysis Services o acesse?   
A **resposta** Azure Analysis Services não usa endereços IP fixos ou marcas de serviço. O intervalo de endereços IP que seus servidores de Analysis Services usam pode ser qualquer coisa no intervalo de endereços IP para a *região do Azure*. Você precisa fornecer o *intervalo completo* de endereços IP para a região do Azure do seu servidor nas regras de firewall do banco de dados de origem. Outra alternativa, e possivelmente mais segura, é configurar um gateway de dados local. Você pode configurar seus servidores de Analysis Services com a [propriedade de servidor AlwaysUseGateway](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)e, em seguida, garantir que o gateway de dados local tenha um endereço IP permitido pelas regras de firewall da fonte de dados.

## <a name="azure-apps-with-ip-address"></a>Aplicativos do Azure com endereço IP

**Pergunta** -eu uso um aplicativo baseado no Azure (por exemplo, Azure Functions, Azure data Factory) com um endereço IP que muda de forma dinâmica. Como posso gerenciar as regras de firewall Azure Analysis Services para permitir dinamicamente o endereço IP em que meu aplicativo está sendo executado?   
O Azure Analysis Services de **resposta** não dá suporte a links privados, VNETs ou marcas de serviço. Há algumas soluções de código-fonte aberto (por exemplo, https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) que detectam o endereço IP do aplicativo cliente e atualizam as regras de firewall automaticamente e temporariamente.


## <a name="next-steps"></a>Próximas etapas

[Instalar e configurar um gateway de dados local](analysis-services-gateway-install.md)   
[Conectando-se a fontes de dados locais com o gateway de dados local](analysis-services-gateway.md)   
[Usar gateway para fontes de dados em uma VNet (Rede Virtual) do Azure](analysis-services-vnet-gateway.md)
