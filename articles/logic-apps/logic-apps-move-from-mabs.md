---
title: Mover aplicativos dos Serviços BizTalk para os Aplicativos Lógicos do Azure | Microsoft Docs
description: Migrar de MABS (Serviços BizTalk do Azure) para os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f813cb5d8d5c442fc17f126c3a2ff6de7b0bdde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321001"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Migrar dos Serviços BizTalk para os Aplicativos Lógicos do Azure

O MABS (Serviços BizTalk do Microsoft Azure) está sendo desativado. Para mover suas soluções de integração do MABS para os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md), siga as orientações deste artigo. 

## <a name="introduction"></a>Introdução

Os Serviços BizTalk consistem em dois subserviços:

* Conexões Híbridas dos Serviços BizTalk da Microsoft
* Integração com base em ponte EAI e EDI

As [Conexões Híbridas do Serviço de Aplicativo do Azure](../app-service/app-service-hybrid-connections.md) substituem as Conexões Híbridas dos Serviços BizTalk. As Conexões Híbridas do Azure estão disponíveis com o Serviço de Aplicativo do Azure e por meio do Portal do Azure. Esse serviço fornecem um Gerenciador de Conexões Híbridas para que você possa gerenciar as conexões híbridas dos Serviços BizTalk existentes e também novas conexões híbridas que você criar no portal. 

[Aplicativos Lógicos](../logic-apps/logic-apps-overview.md) substitui a integração com base em ponte EAI e EDI por todos os mesmos recursos em Serviços BizTalk e muito mais. Esse serviço fornece um fluxo de trabalho baseado em consumo de escala de nuvem e recursos de orquestração para você criar soluções de integração complexas com rapidez e facilidade por meio de um navegador ou com o Visual Studio.

Esta tabela mapeia os recursos de Serviços BizTalk para Aplicativos Lógicos.

| Serviços do BizTalk   | Aplicativos Lógicos            | Finalidade                      |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Enviar e receber dados   |
| Ponte             | Aplicativo Lógico             | Processador de pipeline           |
| Estágio de validação     | Ação de Validação de XML | Validar um documento XML em relação a um esquema | 
| Estágio de enriquecimento       | Tokens de Dados           | Promover propriedades em mensagens ou para decisões de encaminhamento |
| Estágio de transformação    | Ação Transformar      | Converter mensagens XML de um formato em outro |
| Estágio de decodificação       | Ação de decodificação de arquivo simples | Converter de arquivo simples em XML |
| Estágio de codificação       | Ação de codificação de arquivo simples | Converter de XML em arquivos simples |
| Inspetor de Mensagens  | Azure Functions ou Aplicativos de API | Executar código personalizado em suas integrações |
| Ação de encaminhamento       | Condição ou Comutador | Encaminhar mensagens para um dos conectores especificados |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefatos de Serviços BizTalk

Os Serviços BizTalk têm vários tipos de artefatos. 

## <a name="connectors"></a>Conectores

Os conectores dos Serviços BizTalk ajudam as pontes a enviar e receber dados, incluindo pontes bidirecionais que habilitam interações de solicitação/resposta com base em HTTP. Os Aplicativos Lógicos usam a mesma terminologia e têm 180+ conectores com a mesma finalidade conectando-se a uma ampla variedade de tecnologias e serviços. Por exemplo, os conectores estão disponíveis para serviços PaaS e SaaS de nuvem, como o OneDrive, Office 365, Dynamics CRM e muito mais, além de sistemas locais por meio do Gateway de Dados Local, que substitui o Serviço de Adaptador BizTalk pelos Serviços BizTalk. As origens nos Serviços BizTalk são limitadas a FTP, SFTP e Fila do Barramento de Serviço ou assinatura de Tópico.

![](media/logic-apps-move-from-mabs/sources.png)

Por padrão, cada ponte tem um ponto de extremidade HTTP, que é configurado com o Endereço de Tempo de Execução e as propriedades de Endereço Relativo da ponte. Para obter os mesmos resultados com Aplicativos Lógicos, use as ações de [Solicitação e Resposta](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>Pontes e processamento de XML

Nos Serviços BizTalk, uma ponte é análoga a um pipeline de processamento. Uma ponte pode levar os dados recebidos de um conector, trabalhar com os dados e enviar os resultados a outro sistema. Os Aplicativos Lógicos fazem o mesmo dando suporte aos mesmos padrões de interação baseados em pipeline que os Serviços BizTalk, além de fornecer outros padrões de integração. A [Ponte de Resposta de Solicitação XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) nos Serviços BizTalk é conhecida como pipeline VETER, o que consiste em estágios que podem realizar estas tarefas:

* (V) Validar
* (E) Enriquecer
* (T) Transformar
* (E) Enriquecer
* (R) Encaminhar

A imagem mostra como o processamento é dividido entre a solicitação e a resposta, o que permite o controle sobre a solicitação e os caminhos de resposta separadamente; por exemplo, usando mapas diferentes para cada caminho:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, uma ponte unidirecional XML adiciona estágios de Decodificação e Codificação estágios no início e no final do processamento. A ponte de Passagem contém um estágio de Enriquecimento único.

### <a name="message-processing-decoding-and-encoding"></a>Processamento de mensagens, decodificação e codificação

Nos Serviços BizTalk, você pode receber diferentes tipos de mensagens XML e determinar o esquema correspondente para a mensagem recebida. Esse trabalho é feito no estágio *Tipos de Mensagem* do pipeline de processamento de recebimento. O estágio de Decodificação usa o tipo de mensagem detectada para decodificar a mensagem usando o esquema fornecido. Se é um esquema de arquivo simples, esse estágio converte o arquivo simples de entrada em XML. 

Os Aplicativos Lógicos fornecem recursos semelhantes. Você recebeu um flatfile por protocolos diferentes usando gatilhos de conector diferentes (Sistema de Arquivos, FTP, HTTP e assim por diante) e usa a ação [Decodificar Arquivo Simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md) para converter os dados de entrada em XML. Você pode mover os esquemas de arquivo simples existentes diretamente para os Aplicativos Lógicos sem alterações e carregar os esquemas para sua Conta de Integração.

### <a name="validation"></a>Validação

Depois que os dados de entrada são convertidos em XML (ou se XML era o formato da mensagem recebida), a validação é executada para determinar se a mensagem adere ao esquema XSD. Para realizar essa tarefa nos Aplicativos Lógicos, use a ação [Validação de XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). Você pode usar os mesmos esquemas dos Serviços BizTalk sem qualquer alteração.

### <a name="transform-messages"></a>Transformar mensagens

Nos Serviços BizTalk, o estágio de transformação converte um formato de mensagens baseado em XML em outro. Esse trabalho é feito por meio da aplicação de um mapa, usando o mapeador baseado em TRFM. Nos Aplicativos Lógicos, o processo é semelhante. A ação Transformar executa um mapa de sua Conta de Integração. A principal diferença é que os mapas em Aplicativos Lógicos estão no formato XSLT. O XSLT inclui a capacidade de reutilizar o XSLT existente, incluindo mapas criados para o BizTalk Server que contêm functoids. 

### <a name="routing-rules"></a>Regras de roteamento

Os Serviços BizTalk tomam uma decisão de encaminhamento sobre a qual ponto de extremidade ou conector enviar mensagens ou dados de entrada. A capacidade de selecionar pontos de extremidade pré-configurados é possível usando a opção de filtro de encaminhamento:

![](media/logic-apps-move-from-mabs/route-filter.png)

Nos Serviços BizTalk, se só há duas opções, o uso de uma *condição* é a melhor maneira de converter filtros de roteamento nos Serviços Biztalk. Se houver mais de duas, use uma **opção**.

Os Aplicativos Lógicos fornecem recursos de lógica sofisticada além de fluxo de controle e direcionamento com [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [instruções switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Enriquecer

No processamento dos Serviços BizTalk, o estágio Enriquecer adiciona propriedades ao contexto de mensagem associado aos dados recebidos. Por exemplo, promover uma propriedade a ser usada para encaminhamento de uma pesquisa de banco de dados ou extraindo um valor usando uma expressão XPath. Os Aplicativos Lógicos fornecem acesso a todas as saídas de dados contextuais de ações anteriores, simplificando a replicação do mesmo comportamento. Por exemplo, usando a ação de conexão de SQL `Get Row`, você retorna dados de um banco de dados do SQL Server e usa os dados em uma ação de Decisão para encaminhamento. Da mesma forma, as propriedades nas mensagens de entrada na fila do Barramento de Serviço por um gatilho são endereçáveis, bem como o XPath usando a expressão de linguagem de definição de fluxo de trabalho xpath.

### <a name="run-custom-code"></a>Executar código personalizado

Os Serviços BizTalk permitem [executar código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) carregado em seus próprios assemblies. Essa funcionalidade é implementada pela interface de [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector). Cada estágio na ponte inclui duas propriedades (On Enter Inspector e On Exit Inspector) que fornecem o tipo .NET criado que implementa essa interface. O código personalizado permite executar processamento mais complexo nos dados e permite reutilizar o código existente em assemblies que executam a lógica de negócios comum. 

Os aplicativos lógicos fornecem duas maneiras principais de executar código personalizado: O Azure Functions e aplicativos de API. Azure Functions podem ser criadas e chamadas de aplicativos lógicos. Confira [Adicionar e executar código personalizado para aplicativos lógicos por meio de Azure Functions](../logic-apps/logic-apps-azure-functions.md). Use Aplicativos de API, parte do Serviço de Aplicativo do Azure, para criar seus próprios gatilhos e ações. Saiba mais sobre como [criar uma API personalizada para usar com Aplicativos Lógicos](../logic-apps/logic-apps-create-api-app.md). 

Se tiver código personalizado em assemblies chamados dos Serviços BizTalk, você poderá mover esse código para Azure Functions ou criar APIs personalizadas com Aplicativos de API, dependendo do que estiver implementando. Por exemplo, se você tiver um código que encapsula outro serviço para o qual os Aplicativos Lógicos não têm um conector, crie um Aplicativo de API e use as ações que o aplicativo de API fornece dentro do aplicativo lógico. Se você tem funções auxiliares ou bibliotecas, as Azure Functions provavelmente são a melhor opção.

### <a name="edi-processing-and-trading-partner-management"></a>Gerenciamento de parceiros comerciais e processamento de EDI

Os Serviços BizTalk e Aplicativos Lógicos incluem processamento de EDI e B2B com suporte para AS2 (Applicability Statement 2), X12 e EDIFACT. Nos Serviços BizTalk, você cria pontes EDI e cria ou gerencia parceiros comerciais e contratos no portal de Gerenciamento e Rastreamento dedicado.
Nos Aplicativos Lógicos, você obtém essa funcionalidade por meio do [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) (EIP). O EIP fornece ações da [Conta de Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e ações B2B para o processamento de EDI e B2B. Você também pode usar a Conta de Integração para criar e gerenciar [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md). Depois de criar uma Conta de Integração, você pode vincular um ou mais aplicativos lógicos à conta. Você pode usar as ações de B2B para acessar informações do parceiro comercial do aplicativo lógico. São fornecidas as seguintes ações:

* Codificar AS2
* Decodificar AS2
* Codificar X12
* Decodificar X12
* Codificação de EDIFACT
* Decodificação de EDIFACT

Diferentemente dos Serviços BizTalk, essas ações são separadas dos protocolos de transporte. Portanto, ao criar aplicativos lógicos, você tem mais flexibilidade quanto aos conectores que usa para enviar e receber dados. Por exemplo, você pode receber arquivos X12 como anexos de email e, depois, processar esses arquivos em um aplicativo lógico. 

## <a name="manage-and-monitor"></a>Gerenciar e monitorar

No Serviços Biztalk, um portal dedicado fornecia recursos de rastreamento para monitorar e solucionar problemas. Aplicativos Lógicos fornecem recursos de rastreamento e monitoramento avançados por meio do [Portal do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) e inclui um aplicativo móvel para manter o controle de tudo que estiver em movimento.

## <a name="high-availability"></a>Alta disponibilidade

Para HA (alta disponibilidade) nos Serviços BizTalk, você pode compartilhar a carga de processamento usando mais de uma instância em determinada região. Os Aplicativos Lógicos fornecem HA regional sem custo adicional. 

No Serviços BizTalk, a recuperação de desastre fora de região para o processamento B2B exige um processo de backup e restauração. Para continuidade de negócios, os Aplicativos Lógicos fornecem um [recurso de RD](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) ativo/passivo entre regiões, que permite a sincronização de dados B2B entre contas de integração em regiões diferentes.

## <a name="next-steps"></a>Próximas etapas

* [O que são Aplicativos Lógicos?](../logic-apps/logic-apps-overview.md)
* [Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou comece rapidamente usando um [modelo predefinido](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Exibir todos os conectores disponíveis](../connectors/apis-list.md) que você pode usar em aplicativos lógicos