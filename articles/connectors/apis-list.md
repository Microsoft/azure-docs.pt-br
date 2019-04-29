---
title: Conectores de Aplicativos Lógicos do Azure
description: Automatizar fluxos de trabalho com conectores para os Aplicativos Lógicos do Azure, incluindo conectores internos, locais, de conta de integração e conectores empresariais
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 221bcbfb2517efae41005641321a651dfdf8e39f
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759433"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores de Aplicativos Lógicos do Azure

Conectores fornecem acesso rápido de aplicativos lógicos do Azure, eventos, dados e ações em outros aplicativos, serviços, sistemas, protocolos e plataformas. Usando conectores em seus aplicativos lógicos, é possível expandir os recursos para seus aplicativos de nuvem e locais executar tarefas com os dados que você crie e já tiver.

Embora os Aplicativos Lógicos ofereçam [mais de 200 conectores](https://docs.microsoft.com/connectors), este arquivo descreve conectores mais comuns que são usados com sucesso por milhares de aplicativos e milhões de execuções para processar dados e informações. Para encontrar a lista completa de conectores e informações de referência do cada conector, como gatilhos, ações e limites, examine o conector faz referência a páginas sob [visão geral dos conectores](https://docs.microsoft.com/connectors). Além disso, saiba mais sobre [gatilhos e ações](#triggers-actions).

> [!NOTE]
> Para integrar um serviço ou uma API que não tem o conector, você pode diretamente chamar o serviço por um protocolo como HTTP ou criar uma [conector personalizado](#custom).

Conectores estão disponíveis como ações e gatilhos internos ou conectores gerenciados:

* [**Internos**](#built-ins): Essas ações internas e gatilhos são "nativos" para aplicativos lógicos do Azure e ajudam a que criar aplicativos lógicos que são executados em agendas personalizadas, se comunicar com outros pontos de extremidade, receberam e respondem a solicitações e chamam funções do Azure, aplicativos de API do Azure (aplicativos Web), suas próprias APIs gerenciado e publicado com o gerenciamento de API e aplicativos lógicos aninhados que podem receber solicitações. Também é possível usar ações internas que ajudam você a organizar e controlar o fluxo de trabalho do aplicativo lógico e trabalhar com dados.

* **Conectores gerenciados**: Implantado e gerenciado pela Microsoft, esses conectores fornecem gatilhos e ações para acessar outros serviços e sistemas, como o Office 365, o armazenamento de BLOBs do Azure, SQL Server, Salesforce e muito mais. Alguns conectores exigem que você primeiro crie conexões, que são gerenciadas pelo aplicativo lógico do Azure. Conectores gerenciados são organizados nestes grupos:

  |   |   |
  |---|---|
  | [**Conectores de API Gerenciada**](#managed-api-connectors) | Crie aplicativos lógicos que usam serviços como Armazenamento de Blobs do Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e muito mais. | 
  | [**Conectores locais**](#on-premises-connectors) | Depois de instalar e configurar o [gateway de dados locais][gateway-doc], esses conectores ajudam seus aplicativos lógicos a acessar sistemas locais, como SQL Server, SharePoint Server, Oracle DB, compartilhamentos de arquivos e outros. | 
  | [**Conectores da conta de integração**](#integration-account-connectors) | Disponível quando você cria e paga por uma conta de integração, esses conectores transformam e validam XML, codificam e decodificam arquivos simples e processam mensagens B2B (business-to-business) com protocolos AS2, EDIFACT e X12. | 
  | [**Conectores empresariais**](#enterprise-connectors) | Fornece acesso a sistemas empresariais como SAP e IBM MQ por um custo adicional. |
  ||| 

  Por exemplo, se você estiver usando o Microsoft BizTalk Server, os aplicativos lógicos poderão se conectar e se comunicar com o BizTalk Server usando o [conector do BizTalk Server](#on-premises-connectors). 
  Você pode, então, estender ou executar operações semelhantes ao BizTalk em seus aplicativos lógicos usando os [conectores de conta de integração](#integration-account-connectors). 

> [!NOTE]
> Para obter a lista completa de conectores e informações de referência de cada conector, tais como ações e quaisquer gatilhos, que são definidos por uma descrição do Swagger, bem como quaisquer limites, você pode encontrar a lista completa na [Visão geral de conectores](/connectors/). Para obter informações sobre preço, veja [Detalhes de preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/) e [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Internos

Os Aplicativos Lógicos fornecem gatilhos e ações internos para que você possa criar fluxos de trabalho baseados em agendamento, ajudam seus aplicativos lógicos a se comunicarem com outros aplicativos e serviços, controlam o fluxo de trabalho por meio de seus aplicativos lógicos e gerenciam ou manipulam dados. 

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone da API][schedule-icon]<br/>**Agenda**][recurrence-doc] | - Execute seu aplicativo lógico em um agendamento especificado, que varia de recorrências básicas a complexas, com o gatilho **Recorrência**. <p>- Pause o aplicativo lógico por um período especificado com a ação **Atrasar**. <p>- Pause o aplicativo lógico até uma data especificada com a ação **Atrasar até**. | [![Ícone da API][http-icon]<br/>**HTTP**][http-doc] | Comunique-se com qualquer ponto de extremidade por HTTP com gatilhos e ações para HTTP, HTTP + Swagger e HTTP + Webhook. | 
| [![Ícone da API][http-request-icon]<br/>**Solicitação**][http-request-doc] | - Faça com que seu aplicativo lógico possa ser chamado de outros aplicativos ou serviços, gatilho em eventos de recursos da Grade de Eventos ou gatilho em respostas aos alertas da Central de Segurança do Azure com o gatilho **Solicitar**. <p>- Envie respostas para um aplicativo ou serviço com a ação **Resposta**. | [![Ícone da API][batch-icon]<br/>**Em Lotes**][batch-doc] | - Processe mensagens em lotes com o gatilho **Mensagens em lote**. <p>- Chame aplicativos lógicos que possuem gatilhos de lote existentes com a ação **Enviar mensagens para lote**. | 
| [![Ícone da API][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Chame funções do Azure que executam snippets de códigos personalizados (C# ou Node.js) de seus aplicativos lógicos. | [![Ícone da API][azure-api-management-icon]</br>**Gerenciamento de API do Azure**][azure-api-management-doc] | Chame os gatilhos e ações definidos por suas próprias APIs que você gerencia e publica com o Gerenciamento de API do Azure. | 
| [![Ícone da API][azure-app-services-icon]<br/>**Serviços de Aplicativo do Azure**][azure-app-services-doc] | Chame Aplicativos de API do Azure ou Aplicativos Web, hospedado no Serviço de Aplicativo do Azure. Os gatilhos e ações definidos por esses aplicativos são exibidos como quaisquer outros gatilhos e ações de primeira classe quando o Swagger é incluído. | [![Ícone da API][azure-logic-apps-icon]<br/>**Azure<br/>Aplicativos Lógicos**][nested-logic-app-doc] | Chame outros aplicativos lógicos que começam com um gatilho de Solicitação. | 
||||| 

### <a name="control-workflow"></a>Controlar o fluxo de trabalho

Aqui estão as ações internas para estruturar e controlar as ações no fluxo de trabalho do aplicativo lógico:

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone Interno][condition-icon]<br/>**Condição**][condition-doc] | Avalie uma condição e execute ações diferentes com base em se a condição é verdadeira ou falsa. | [![Ícone Interno][for-each-icon]</br>**Para cada**][for-each-doc] | Execute as mesmas ações em cada item em uma matriz. | 
| [![Ícone Interno][scope-icon]<br/>**Escopo**][scope-doc] | Agrupe ações em *Escopos*, que obtém seus próprios status após as ações no escopo concluírem a execução. | [![Ícone Interno][switch-icon]</br>**Switch**][switch-doc] | Agrupe ações em *Casos*, que recebem valores exclusivos, exceto para o caso padrão. Execute somente esse caso cujo valor atribuído coincide com o resultado de uma expressão, objeto ou token. Se nenhuma correspondência existir, execute o caso padrão. | 
| [![Ícone Interno][terminate-icon]<br/>**Encerrar**][terminate-doc] | Pare um fluxo de trabalho de aplicativo lógico ativamente em execução. | [![Ícone Interno][until-icon]<br/>**Até**][until-doc] | Repita ações até que a condição especificada seja verdadeira ou algum estado seja alterado. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Gerenciar ou manipular dados

Aqui estão as ações internas para trabalhar com saídas de dados e seus formatos:  

|   |   | 
|---|---| 
| [![Ícone incorporado][data-operations-icon]<br/>**Operações de dados**][data-operations-doc] | Execute operações com dados: <p>- **Compor**: Crie uma única saída de várias entradas com vários tipos. <br>- **Criar tabela CSV**: Crie uma tabela de valores separados por vírgulas (CSV) de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: Crie uma tabela HTML de uma matriz com objetos JSON. <br>- **Matriz de filtro**: Crie uma matriz de itens na outra matriz que atendem aos seus critérios. <br>- **Junte-se**: Criar uma cadeia de caracteres de todos os itens em uma matriz e separe esses itens com o delimitador especificado. <br>- **Analisar o JSON**: Crie tokens amigáveis de propriedades e seus valores no conteúdo do JSON, você pode usar essas propriedades no fluxo de trabalho. <br>- **Selecione**: Crie uma matriz com objetos JSON transformando itens ou valores em outra matriz e mapeando esses itens para propriedades especificadas. | 
| ![Ícone Interno][date-time-icon]<br/>**Data/Hora** | Execute operações com carimbos de data/hora: <p>- **Adicionar à hora**: Adicione o número especificado de unidades para um carimbo de hora. <br>- **Converter o fuso horário**: Converter um carimbo de data/hora do fuso horário de origem no fuso horário de destino. <br>- **Hora atual**: Retornar o carimbo de data/hora atual como uma cadeia de caracteres. <br>- **Obter horário futuro**: Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. <br>- **Obter horário passado**: Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas. <br>- **Subtrair da hora**: Subtrair um número de unidades de tempo de um carimbo de data/hora. |
| [![Ícone Interno][variables-icon]<br/>**Variáveis**][variables-doc] | Execute operações com variáveis: <p>- **Acrescentar à variável de matriz**: Inserir um valor como o último item em uma matriz armazenado por uma variável. <br>- **Acrescentar à variável de cadeia de caracteres**: Inserir um valor como o último caractere em uma cadeia de caracteres armazenada por uma variável. <br>- **Diminuir variável**: Diminua a uma variável em um valor constante. <br>- **Incrementar variável**: Aumente uma variável com um valor constante. <br>- **Inicializar variável**: Crie uma variável e declare seu tipo de dados e o valor inicial. <br>- **Defina a variável**: Atribua um valor diferente em uma variável existente. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Conectores de API Gerenciada

Aqui estão os conectores mais populares para automatizar tarefas, processos e fluxos de trabalho com esses serviços ou sistemas:

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone da API][azure-service-bus-icon]<br/>**Barramento de Serviço do Azure**][azure-service-bus-doc] | Gerencie mensagens assíncronas, sessões e assinaturas de tópico com o conector mais comumente usado em Aplicativos Lógicos. | [![Ícone da API][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | Conecte-se ao SQL Server no local ou a um Banco de Dados SQL do Azure na nuvem para que você possa gerenciar registros, executar procedimentos armazenados ou executar consultas. | 
| [![Ícone da API][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Conecte-se à sua conta de email do Office 365 para que possa criar e gerenciar emails, tarefas, eventos de calendário e reuniões, contatos, solicitações e muito mais. | [![Ícone da API][azure-blob-storage-icon]<br/>**Armazenamento<br/>de Blobs do Azure**][azure-blob-storage-doc] | Conecte-se à sua conta de armazenamento para que você possa criar e gerenciar o conteúdo do blob. | 
| [![Ícone da API][sftp-icon]<br/>**SFTP**][sftp-doc] | Conecte-se aos servidores SFTP que você pode acessar da Internet para que possa trabalhar com seus arquivos e pastas. | [![Ícone da API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Conecte-se ao SharePoint Online para que você possa gerenciar arquivos, anexos, pastas e muito mais. | 
| [![Ícone da API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Conecte-se à sua conta do Dynamics 365 para que você possa criar e gerenciar registros, itens e muito mais. | [![Ícone da API][ftp-icon]<br/>**FTP**][ftp-doc] | Conecte-se aos servidores FTP que você pode acessar da Internet para que possa trabalhar com seus arquivos e pastas. | 
| [![Ícone da API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Conecte-se à sua conta do Salesforce para que você possa criar e gerenciar itens como registros, trabalhos, objetos e muito mais. | [![Ícone da API][twitter-icon]<br/>**Twitter**][twitter-doc] | Conecte-se à sua conta do Twitter para que você possa gerenciar tweets, seguidores, sua linha do tempo e muito mais. Salve seus tweets em SQL, o Excel ou SharePoint. | 
| [![Ícone da API][azure-event-hubs-icon]<br/>**Hubs de Eventos do Azure**][azure-event-hubs-doc] | Como consumir e publicar eventos por meio de um Hub de Eventos. Por exemplo, obtenha uma saída do seu aplicativo lógico com os Hubs de Eventos e, em seguida, envie essa saída para um provedor de análise em tempo real. | [![Ícone da API][azure-event-grid-icon]<br/>**Grade de Eventos**</br>**do Azure**][azure-event-grid-doc] | Monitore eventos publicados por uma grade de eventos, por exemplo, quando recursos do Azure ou recursos de terceiros são alterados. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores locais 

Aqui estão alguns conectores comumente usados que fornecem acesso a dados e recursos em sistemas locais. Antes de criar uma conexão com um sistema local, você deve primeiro [fazer o download, instalar e configurar um gateway de dados local][gateway-doc]. Esse gateway fornece um canal de comunicação seguro sem ter que configurar a infraestrutura de rede necessária. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ícone de API][biztalk-server-icon]<br/>**BizTalk**</br> **Servidor** | [![Ícone da API][file-system-icon]<br/>**Sistema</br> de Arquivos**][file-system-doc] | [![Ícone da API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Ícone da API][ibm-informix-icon]<br/>**IBM**</br> **Informix**][ibm-informix-doc] | ![Ícone de API][mysql-icon]<br/>**MySQL** | 
| [![Ícone da API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Ícone de API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ícone da API][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![Ícone da API][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![Ícone de API][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores da conta de integração 

Aqui estão os conectores para criar soluções de B2B (business-to-business) com seus aplicativos lógicos quando você cria e paga por uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), o que está disponível por meio do Enterprise Integration Pack (EIP) no Azure. Com essa conta, você pode criar e armazenar os artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e assim por diante. Para usar esses artefatos, associe seus aplicativos lógicos com sua conta de integração. Se você usa o BizTalk Server atualmente, esses conectores poderão parecer familiares.

|   |   |   |   | 
|---|---|---|---| 
| [![Ícone da API][as2-icon]<br/>**Decodificação</br> de AS2**][as2-doc] | [![Ícone da API][as2-icon]<br/>**Codificação</br> de AS2**][as2-doc] | [![Ícone da API][edifact-icon]<br/>**Decodificação</br> de EDIFACT**][edifact-decode-doc] | [![Ícone da API][edifact-icon]<br/>**Codificação</br> de EDIFACT**][edifact-encode-doc] | 
| [![Ícone da API][flat-file-decode-icon]<br/>**Decodificação</br> de arquivo simples**][flat-file-decode-doc] | [![Ícone da API][flat-file-encode-icon]<br/>**Codificação</br> de arquivo simples**][flat-file-encode-doc] | [![Ícone da API][integration-account-icon]<br/>**Conta<br/>de integração**][integration-account-doc] | [![Ícone da API][liquid-icon]<br/>**Transformações**</br>**líquidas**][json-liquid-transform-doc] | 
| [![Ícone da API][x12-icon]<br/>**Decodificação</br> de X12**][x12-decode-doc] | [![Ícone da API][x12-icon]<br/>**Codificação</br> de X12**][x12-encode-doc] | [![Ícone da API][xml-transform-icon]<br/>**Transformações de** </br>**XML**][xml-transform-doc] | [![Ícone da API][xml-validate-icon]<br/>**Validação de <br/>XML**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

Seus aplicativos lógicos podem acessar sistemas corporativos, como SAP e IBM MQ:

|   |   | 
|---|---| 
| [![Ícone da API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Ícone da API][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Gatilhos e ações - obter mais informações

Conectores podem fornecer *disparadores*, *ações*, ou ambos. Um *disparador* é a primeira etapa em qualquer aplicativo lógico, normalmente, especificando o evento que aciona o gatilho e começa a executar seu aplicativo lógico. Por exemplo, o conector FTP tem um gatilho que inicia o aplicativo lógico "quando um arquivo é adicionado ou modificado". Alguns gatilhos verificam regularmente o evento especificado ou os dados e, em seguida, acionam quando detectam o evento especificado ou os dados. Outros disparadores esperar mas disparar instantaneamente quando ocorre um evento específico ou quando novos dados estão disponíveis. Gatilhos também passam os dados necessários para seu aplicativo lógico. Seu aplicativo lógico pode ler e usar esses dados em todo o fluxo de trabalho.
Por exemplo, o conector do Twitter tem um gatilho "Quando um novo tweet é postado", que passa o tweet do conteúdo no fluxo de trabalho do aplicativo lógico. 

Depois que um gatilho é acionado, os aplicativos lógicos do Azure cria uma instância do aplicativo lógico e inicia a execução de *ações* no fluxo de trabalho do aplicativo lógico. Ações são as etapas que siga o gatilho e executam tarefas no fluxo de trabalho do aplicativo lógico. Por exemplo, você pode criar um aplicativo lógico que obtém os dados do cliente do banco de dados SQL e processar os dados em ações posteriores. 

Aqui estão os tipos gerais de gatilhos que aplicativos lógicos do Azure fornece:

* *Gatilho de recorrência*: Esse gatilho é executado em uma agenda especificada e não está totalmente associado a um determinado serviço ou sistema.

* *Gatilho de sondagem*: Esse gatilho sonda regularmente um serviço específico ou um sistema com base no agendamento especificado, a verificação de novos dados ou se um evento específico aconteceu. Se novos dados estão disponíveis ou o evento ocorreu, o gatilho cria e executa uma nova instância do aplicativo lógico, que agora pode usar os dados que são passados como entrada.

* *Gatilho de push*: Esse gatilho aguarda e escuta novos dados ou para um evento ocorrer. Quando novos dados estão disponíveis ou quando o evento ocorrer, o gatilho cria e executa a nova instância do aplicativo lógico, que agora pode usar os dados que são passados como entrada.

<a name="custom"></a>

## <a name="connector-configuration"></a>Configuração do conector

Gatilhos e ações de cada conector fornecem suas próprias propriedades para a configuração. Muitos conectores também exigem que você primeiro crie uma *conexão* para o serviço de destino ou o sistema e fornecer as credenciais de autenticação ou outros detalhes de configuração antes de usar um gatilho ou ação em seu aplicativo lógico. Por exemplo, você deve autorizar uma conexão para uma conta do Twitter para acessar os dados ou para postar em seu nome. 

Para os conectores que usam o OAuth, criar uma conexão significa entrar no serviço, como Office 365, Salesforce ou GitHub, no qual o token de acesso é criptografado e seguramente armazenado em um repositório secreto do Azure. Outros conectores, como FTP e SQL, requerem uma conexão que tem detalhes de configuração, como o endereço do servidor, nome de usuário e senha. Esse detalhes de configuração de conexão são também criptografadas e armazenadas com segurança. 

As conexões podem acessar o serviço de destino ou o sistema para desde que o serviço ou sistema permite. Para serviços que usam conexões OAuth do Azure Active Directory (AD), como o Office 365 e Dynamics, aplicativos lógicos do Azure atualiza os tokens de acesso indefinidamente. Outros serviços podem estabelecer limites quanto a aplicativos lógicos do Azure pode usar um token sem atualizar. Em geral, algumas ações invalidam todos os tokens de acesso, como alterar sua senha.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Conectores e APIs personalizados

Para chamar as APIs que executam o código personalizado ou não estão disponíveis como conectores, você pode estender a plataforma dos Aplicativos Lógicos [criando Aplicativos de API personalizados](../logic-apps/logic-apps-create-api-app.md). Você também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *qualquer* API REST ou baseadas em SOAP, o que torna essas APIs disponíveis para qualquer aplicativo lógico em sua assinatura do Azure.
Para tornar Aplicativos de API personalizado ou conectores público disponíveis para qualquer pessoa para uso no Azure, você pode [enviar conectores para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para enviar ou votar em ideias para os Aplicativos Lógicos do Azure e conectores, visite o [site de comentários do usuário dos Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Encontrar a [lista completa de conectores](https://docs.microsoft.com/connectors)
* [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para aplicativos lógicos](https://docs.microsoft.com/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Conectar-se a fontes de dados locais de aplicativos lógicos com o gateway de dados local"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integre aplicativos lógicos com Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Criar uma instância de serviço de Gerenciamento de API do Azure para gerenciar e publicar suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicativos lógicos a Aplicativos de API do Serviço de Aplicativo"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envia mensagens de tópicos e filas do barramento de serviço e recebe mensagens de assinaturas e filas do barramento de serviço"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e execute ações diferentes com base em se a condição é verdadeira ou falsa"
[delay-doc]: ./connectors-native-delay.md "Executar ações atrasadas"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Executar as mesmas ações em cada item em uma matriz"
[http-doc]: ./connectors-native-http.md "Fazer chamadas HTTP com o conector HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Adicionar ações para solicitações e respostas HTTP aos seus aplicativos lógicos"
[http-response-doc]: ./connectors-native-reqres.md "Adicionar ações para solicitações e respostas HTTP aos seus aplicativos lógicos"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Fazer chamadas HTTP com o conector HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Adicionar ações e gatilhos de webhook HTTP para seus aplicativos lógicos"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrar aplicativos lógicos a fluxos de trabalho aninhados"
[query-doc]: ./connectors-native-query.md "Selecionar e filtrar matrizes com a ação Consultar"
[recurrence-doc]:  ./connectors-native-recurrence.md "Disparar ações recorrentes para aplicativos lógicos"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizar ações em grupos, o que obtém seu próprio status após a conclusão da execução das ações" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizar ações em casos, que recebem valores exclusivos. Execute somente o caso cujo valor coincide com o resultado de uma expressão, objeto ou token. Se nenhuma correspondência existir, execute o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Parar ou cancelar um fluxo de trabalho em execução ativamente para seu aplicativo lógico"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repita ações até que a condição especificada seja verdadeira ou algum estado seja alterado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Executar as operações de dados, como filtragem de matrizes ou criação de tabelas HTML e de CSV"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, como inicializar, configurar, aumentar ou diminuir e acrescentar à variável de cadeia de caracteres ou matriz"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerenciar arquivos em seu contêiner de blob com o conector de Armazenamento de Blobs do Azure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Monitorar eventos publicados por uma Grade de Eventos, por exemplo, quando recursos do Azure ou recursos de terceiros são alterados"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Conectar-se aos Hubs de Eventos do Azure. Receber e enviar eventos entre aplicativos lógicos e Hubs de Eventos"
[box-doc]: ./connectors-create-api-box.md "Conectar-se ao Box. Carregar, obter, excluir, listar arquivos e muito mais"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Conectar-se ao Dropbox. Carregar, obter, excluir, listar arquivos e muito mais"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Conectar-se ao Dynamics CRM Online para que você possa trabalhar com os dados do CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Conectar-se ao Facebook. Postar em uma linha do tempo, receber um feed de página e muito mais"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Conectar-se a um sistema de arquivos local"
[ftp-doc]: ./connectors-create-api-ftp.md "Conectar-se a um servidor FTP / FTPS e para tarefas FTP, incluindo carregar, obter, excluir arquivos e muito mais"
[github-doc]: ./connectors-create-api-github.md "Conectar-se ao GitHub e acompanhar os problemas"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Conecta-se ao Google Agenda e pode gerenciar o calendário."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Conectar-se ao GoogleDrive para interagir com os dados."
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Conectar-se ao Google Planilhas para poder pode modificar suas planilhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Conectar-se ao Google Tasks para gerenciar suas tarefas."
[ibm-db2-doc]: ./connectors-create-api-db2.md "Conectar-se ao IBM DB2 na nuvem ou no local. Atualizar uma linha, obter uma tabela e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Conectar-se ao Informix na nuvem ou no local. Ler uma linha, listar as tabelas e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Conectar-se a locais do IBM MQ ou ao Azure para enviar e receber mensagens"
[instagram-doc]: ./connectors-create-api-instagram.md "Conectar-se ao Instagram. Disparar ou agir nos eventos"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Conectar-se à sua conta do MailChimp. Gerenciar e automatizar emails"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Conectar-se ao Mandrill para comunicação"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Conectar-se ao Microsoft Translator. Traduzir textos, detectar idiomas e muito mais" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Conectar-se à sua conta do Office 365. Enviar e receber emails, gerenciar seu calendário e contatos e muito mais"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Obtenha informações de vídeo, listas e canais de vídeo e URLs de reprodução de vídeos do Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Conectar-se ao seu Microsoft OneDrive pessoal. Carregar, excluir, listar arquivos e muito mais"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Conectar-se ao seu Microsoft OneDrive corporativo. Carregar, excluir, listar arquivos e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Conectar-se ao banco de dados Oracle para adicionar, inserir e excluir linhas e muito mais"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Conectar-se à sua caixa de correio do Outlook. Gerenciar seu email, calendários, contatos e muito mais"
[project-online-doc]: ./connectors-create-api-projectonline.md "Conectar-se ao Microsoft Project Online. Gerenciar seus projetos, tarefas, recursos e muito mais"
[rss-doc]: ./connectors-create-api-rss.md "Publicar e recuperar itens de feed, disparar operações quando um novo item for publicado em um feed RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Conectar-se à sua conta do Salesforce. Gerenciar contas, clientes potenciais, oportunidades e muito mais"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Conectar-se a um sistema SAP local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Conectar-se ao SendGrid. Enviar email e gerenciar listas de destinatários"
[sftp-doc]: ./connectors-create-api-sftp.md "Conectar-se à sua conta SFTP. Carregar, obter, excluir arquivos e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Conecte-se com o servidor local do SharePoint. Gerenciar documentos, listar itens e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Conectar-se ao SharePoint Online. Gerenciar documentos, listar itens e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Conectar-se ao Slack e postar mensagens em canais do Slack."
[smtp-doc]: ./connectors-create-api-smtp.md "Conectar-se a um servidor SMTP e enviar emails com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Conectar-se ao SparkPost para comunicação."
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Conectar-se ao Banco de Dados SQL do Azure ou SQL Server. Criar, atualizar, obter e excluir entradas em uma tabela do Banco de Dados SQL."
[trello-doc]: ./connectors-create-api-trello.md "Conectar-se ao Trello. Gerenciar seus projetos e organizar o que quiser com qualquer pessoa"
[twilio-doc]: ./connectors-create-api-twilio.md "Conectar-se ao Twilio. Enviar e receber mensagens, obter os números disponíveis, gerenciar números de telefone de entrada e muito mais"
[twitter-doc]: ./connectors-create-api-twitter.md "Conectar-se ao Twitter. Obter cronogramas, postar tweets e muito mais"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Conectar-se ao Wunderlist. Gerenciar suas tarefas e listas de tarefas, manter sua vida sincronizada e muito mais"
[yammer-doc]: ./connectors-create-api-yammer.md "Conectar-se ao Yammer. Postar mensagens, receber novas mensagens e muito mais"
[youtube-doc]: ./connectors-create-api-youtube.md "Conectar-se ao YouTube. Gerenciar seus vídeos e canais"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Saiba mais sobre a integração corporativa do AS2."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Saiba mais sobre a decodificação EDIFACT de integração corporativa"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Saiba mais sobre a codificação EDIFACT de integração corporativa"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo simples de integração corporativa."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo simples de integração corporativa."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Pesquisa esquemas, mapas, parceiros e muito mais em sua conta de integração"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Saiba mais sobre as transformações de JSON com Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Saiba mais sobre a integração corporativa do X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Saiba mais sobre a decodificação X12 de integração corporativa"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Saiba mais sobre a codificação X12 de integração corporativa"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Saiba mais sobre as transformações de integração corporativa."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Saiba mais sobre a validação de XML de integração corporativa."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
