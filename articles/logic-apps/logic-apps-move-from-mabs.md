---
title: "Mover aplicativos dos Serviços BizTalk para os Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Mover ou migrar MABS dos Serviços BizTalk do Azure para Aplicativos Lógicos"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: e58c6950d1d9420f32fc98ca917216dc5fae4fc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>Mover dos Serviços BizTalk para Aplicativos Lógicos

O MABS (Serviços BizTalk do Microsoft Azure) está sendo desativado. Use este tópico para mover suas soluções de integração do MABS para os Aplicativos Lógicos do Azure. 

## <a name="overview"></a>Visão geral

Os Serviços BizTalk consistem em dois subserviços:

1.  Conexões Híbridas dos Serviços BizTalk da Microsoft
2.  Integração com base em ponte EAI e EDI

Se você quiser migrar conexões híbridas, [Conexões Híbridas do Serviço de Aplicativo do Azure](../app-service/app-service-hybrid-connections.md) descreve as alterações e os recursos desse serviço. As Conexões Híbridas do Azure substituem as Conexões Híbridas dos Serviços BizTalk. As Conexões Híbridas do Azure estão disponíveis com o Serviço de Aplicativo do Azure e são oferecidas no Portal do Azure. As Conexões Híbridas do Azure também fornecem um novo Gerenciador de Conexão Híbrida para gerenciar as conexões híbridas dos Serviços BizTalk existentes e novas conexões híbridas que você criar no portal. As Conexões Híbridas do Serviço de Aplicativo do Azure estão com disponibilidade geral (GA).

Para a integração baseada em ponte EAI e EDI, os Aplicativos Lógicos são o substituto. Os Aplicativos Lógicos fornecem os mesmos recursos do que os Serviços BizTalk e muito mais. Os Aplicativos Lógicos fornecem um fluxo de trabalho baseado em consumo de escala de nuvem e recursos de orquestração que permitem que você crie soluções de integração complexas com rapidez e facilidade usando um navegador ou as ferramentas no Visual Studio.

A tabela a seguir fornece um mapeamento de recursos dos Serviços BizTalk para Aplicativos Lógicos.

| Serviços do BizTalk   | Aplicativos Lógicos            | Finalidade                  |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Envio e recebimento de dados   |
| Ponte             | Aplicativo Lógico             | Processador de pipeline           |
| Estágio de validação     | Ação de Validação de XML      | Validar um documento XML em relação a um esquema             |
| Estágio de enriquecimento       | Tokens de Dados      | Promover propriedades em mensagens ou para decisões de encaminhamento             |
| Estágio de transformação    | Ação Transformar      | Converter mensagens XML de um formato em outro             |
| Estágio de decodificação       | Ação de decodificação de arquivo simples      | Converter de arquivo simples em XML             |
| Estágio de codificação       |  Ação de codificação de arquivo simples      | Converter de XML em arquivos simples             |
| Inspetor de Mensagens       |  Azure Functions ou Aplicativos de API      | Executar código personalizado em suas integrações             |
| Ação de encaminhamento      |  Condição ou Comutador      | Encaminhar mensagens para um dos conectores especificados             |

Há vários tipos diferentes de artefato nos Serviços BizTalk.

## <a name="connectors"></a>Conectores
Os conectores nos Serviços BizTalk permitem que pontes enviem e recebam dados, incluindo pontes bidirecionais que habilitaram interações de solicitação/resposta com base em HTTP. Nos Aplicativos Lógicos, a mesma terminologia é usada. Os conectores nos Aplicativos Lógicos têm a mesma finalidade e também incluem mais de 140 que podem se conectar a uma ampla gama de tecnologias e serviços, no local usando o Gateway de Dados local (substituindo o Serviço do Adaptador do BizTalk usado pelos Serviços BizTalk) e em serviços de nuvem SaaS e PaaS, como OneDrive, Office365, Dynamics CRM e muito mais.

As origens nos Serviços BizTalk são limitadas a FTP, SFTP e Fila do Barramento de Serviço ou assinatura de Tópico.

![](media/logic-apps-move-from-mabs/sources.png)

Cada ponte tem um ponto de extremidade HTTP por padrão, que é configurado com o Endereço de Tempo de Execução e as propriedades de Endereço Relativo da ponte. Para obter o mesmo com Aplicativos Lógicos, use as ações de [Solicitação e Resposta](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>Pontes e processamento de XML
Uma ponte nos Serviços BizTalk é análoga a um pipeline de processamento. Uma ponte pode levar os dados recebidos de um conector, trabalhar com os dados e enviá-los a outro sistema. Os Aplicativos Lógicos fazem o mesmo dando suporte aos mesmos padrões de interação baseados em pipeline que os Serviços BizTalk, além de fornecer vários outros padrões de integração. A [Ponte de Resposta de Solicitação XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) nos Serviços BizTalk é conhecida como pipeline VETER, consistindo em estágios que podem:

* (V) Validar
* (E) Enriquecer
* (T) Transformar
* (E) Enriquecer
* (R) Encaminhar

Conforme mostrado na imagem a seguir, o processamento é dividido entre a solicitação e a resposta e permite o controle sobre a solicitação e os caminhos de resposta separadamente (por exemplo, usando mapas diferentes para cada um deles):

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, uma ponte Unidirecional XML adiciona estágios de Decodificação e Codificação no início e no fim do processamento, e a ponte de Passagem contém um único estágio de Enriquecimento.

### <a name="message-processing-and-decodingencoding"></a>Processamento de mensagens e codificação/decodificação
Nos Serviços BizTalk, você recebe mensagens XML de diferentes tipos e determina o esquema correspondente para a mensagem recebida. Isso é feito no estágio **Tipos de Mensagem** do pipeline de processamento de recebimento. Em seguida, o estágio de Decodificação usa o tipo de mensagem detectada para decodificá-lo usando o esquema fornecido. Se é um esquema flatfile, ele converte o flatfile de entrada em XML. 

Os Aplicativos Lógicos fornecem recursos semelhantes. Você recebeu um flatfile por vários protocolos diferentes usando gatilhos de conector diferentes (Sistema de Arquivos, FTP, HTTP e assim por diante) e usa a ação [Decodificar Arquivo Simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md) para converter os dados de entrada em XML. Você pode mover os esquemas de arquivo simples existentes diretamente para os aplicativos lógicos sem precisar de alterações e carregar os esquemas para sua Conta de Integração.

### <a name="validation"></a>Validação
Depois que os dados de entrada são convertidos em XML (ou se XML era o formato da mensagem recebida), a validação é executada para determinar se a mensagem adere ao esquema XSD. Para fazer isso nos Aplicativos Lógicos, use a ação [Validação de XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). Novamente, você pode usar os mesmos esquemas dos Serviços BizTalk sem qualquer alteração.

### <a name="transform-messages"></a>Transformar mensagens
Nos Serviços BizTalk, o estágio de transformação converte um formato de mensagens baseado em XML em outro. Isso é feito por meio da aplicação de um mapa, usando o mapeador baseado em TRFM. Nos Aplicativos Lógicos, o processo é semelhante. A ação Transformar executa um mapa de sua Conta de Integração. A principal diferença é que os mapas em Aplicativos Lógicos estão no formato XSLT. O XSLT inclui a capacidade de reutilizar o XSLT existente, incluindo mapas criados para o BizTalk Server que contêm functoids. 

### <a name="routing-rules"></a>Regras de roteamento
Os Serviços BizTalk tomam uma decisão de encaminhamento sobre a qual ponto de extremidade/conector enviar mensagens/dados de entrada. A capacidade de selecionar um dos vários pontos de extremidade pré-configurados é possível usando a opção de filtro de encaminhamento:

![](media/logic-apps-move-from-mabs/route-filter.png)

Os Aplicativos Lógicos fornecem recursos de lógica mais sofisticada com [Condição](../logic-apps/logic-apps-use-logic-app-features.md) e [Opção](../logic-apps/logic-apps-switch-case.md), habilitando o fluxo de controle e o encaminhamento avançados. A conversão de filtros de encaminhamento nos Serviços BizTalk é obtida da melhor forma usando uma **condição** *se* existem apenas duas opções. Se houver mais de duas, use uma **opção**.

### <a name="enrich"></a>Enriquecer
O estágio Enriquecer no processamento dos Serviços BizTalk adiciona propriedades ao contexto de mensagem associado aos dados recebidos. Por exemplo, promover uma propriedade a ser usada para encaminhamento (discutida abaixo) de uma pesquisa de banco de dados ou extraindo um valor usando uma expressão XPath. Os Aplicativos Lógicos fornecem acesso a todas as saídas de dados contextuais de ações anteriores, simplificando a replicação do mesmo comportamento. Por exemplo, usando a ação de conexão de SQL `Get Row`, você retorna dados de um banco de dados do SQL Server e usa os dados em uma ação de Decisão para encaminhamento. Da mesma forma, as propriedades nas mensagens de entrada na fila do Barramento de Serviço por um gatilho são endereçáveis, bem como o XPath usando a expressão de linguagem de definição de fluxo de trabalho xpath.

### <a name="use-custom-code"></a>Usar código personalizado
Os Serviços BizTalk fornecem a capacidade de [executar código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) carregado em seus próprios assemblies. Isso é implementado pela interface de [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx). Cada estágio na ponte inclui duas propriedades (On Enter Inspector e On Exit Inspector) que fornecem o tipo .Net criado que implementa essa interface. O código personalizado permite executar processamento mais complexo nos dados, bem como reutilizar o código existente em assemblies que executam a lógica de negócios comum. 

Os Aplicativos Lógicos fornecem duas maneiras principais de executar código personalizado: Azure Functions e Aplicativos de API. Azure Functions podem ser criadas e chamadas de aplicativos lógicos. Confira [Adicionar e executar código personalizado para aplicativos lógicos por meio de Azure Functions](../logic-apps/logic-apps-azure-functions.md). Use Aplicativos de API, parte do Serviço de Aplicativo do Azure, para criar seus próprios gatilhos e ações. Saiba mais sobre como [Criar uma API personalizada para usar com Aplicativos Lógicos](../logic-apps/logic-apps-create-api-app.md). 

Se tiver código personalizado em assemblies chamados dos Serviços BizTalk, você poderá mover esse código para Azure Functions ou criar APIs personalizadas com Aplicativos de API, dependendo do que estiver implementando. Por exemplo, se você tiver um código que encapsula outro serviço para o qual os Aplicativos Lógicos não têm um conector, crie um Aplicativo de API e use as ações que o aplicativo de API fornece dentro do aplicativo lógico. Se você tem funções auxiliares ou bibliotecas, as Azure Functions provavelmente são a melhor opção.

### <a name="edi-processing-and-trading-partner-management"></a>Gerenciamento de parceiros comerciais e processamento de EDI
Os Serviços BizTalk incluem processamento de EDI e B2B com suporte para AS2 (Applicability Statement 2), X12 e EDIFACT. Os Aplicativos Lógicos também. Nos Serviços BizTalk, você cria pontes EDI e cria/gerencia parceiros comerciais e contratos no portal de Gerenciamento e Rastreamento dedicado.

Nos Aplicativos Lógicos, essa funcionalidade é incluída no [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Isso consiste em ações da Conta de Integração e ações B2B para o processamento de EDI e B2B. A [Conta de Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) é usada para criar e gerenciar [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md). Depois de criar uma Conta de Integração, você pode associar um ou mais aplicativos lógicos à conta. Depois da associação, você pode usar as ações de B2B para acessar informações do parceiro comercial no aplicativo lógico. São fornecidas as seguintes ações:

* Codificar AS2
* Decodificar AS2
* Codificar X12
* Decodificar X12
* Codificação de EDIFACT
* Decodificação de EDIFACT

Diferentemente dos Serviços BizTalk, essas ações são separadas dos protocolos de transporte. Portanto, ao criar aplicativos lógicos, você tem mais flexibilidade quanto aos conectores que usa para enviar e receber dados. Por exemplo, é possível receber arquivos X12 como anexos de email e, depois, processar esses arquivos em um aplicativo lógico. 

## <a name="manage-and-monitor"></a>Gerenciar e monitorar
Assim como o gerenciamento de parceiros comerciais, o portal dedicado dos Serviços BizTalk fornecia recursos de rastreamento para monitorar e solucionar problemas. 

Os Aplicativos Lógicos proporcionam recursos mais avançados de rastreamento e monitoramento no [Portal do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) e com a [solução Operations Management Suite B2B](../logic-apps/logic-apps-monitor-b2b-message.md), inclusive um aplicativo móvel para manter o controle de tudo em qualquer lugar.

## <a name="high-availability"></a>Alta disponibilidade
Para obter HA (alta disponibilidade) nos Serviços BizTalk, você usa mais de uma instância em determinada região para compartilhar a carga de processamento. Com os Aplicativos Lógicos, a HA na região é interna e é fornecida sem custo adicional. Para recuperação de desastre fora de região para o processamento B2B nos Serviços BizTalk, é necessário um processo de backup e restauração. Nos Aplicativos Lógicos, é fornecido um [recurso de RD](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) ativo/passivo entre regiões, que permite a sincronização de dados B2B entre Contas de Integração em regiões diferentes para continuidade de negócios.

## <a name="next"></a>Avançar
* [O que são Aplicativos Lógicos](logic-apps-what-are-logic-apps.md)
* [Crie seu primeiro aplicativo lógico](logic-apps-create-a-logic-app.md) ou comece rapidamente usando um [modelo predefinido](logic-apps-use-logic-app-templates.md)  
* [Exibir todos os conectores disponíveis](../connectors/apis-list.md) você pode usar em um aplicativo lógico
