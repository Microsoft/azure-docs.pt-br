---
title: "O que é o Azure Analysis Services | Microsoft Docs"
description: "Obtenha uma visão geral do Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2017
ms.author: owend
ms.openlocfilehash: 8b3a538fc0fe34d0b7af8a99510cfb19abd32d55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-analysis-services"></a>O que é o Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

O Azure Analysis Services fornece modelagem de dados de nível empresarial na nuvem. É uma plataforma totalmente gerenciada como um serviço (PaaS), integrada com os serviços de plataforma de dados do Azure. 

Com o Analysis Services, você pode realizar o mashup e combinar dados de várias fontes, definir métricas e proteger seus dados em um modelo de dados simples, semântico e confiável. O modelo de dados fornece uma maneira mais rápida e fácil para seus usuários procurarem grandes quantidades de dados com aplicativos cliente como o Power BI, Excel, Reporting Services, aplicativos de terceiros e personalizados.

![Fontes de dados](./media/analysis-services-overview/aas-overview-data-sources.png)

Assista a [este vídeo](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) para aprender como o Azure Analysis Services se adapta aos recursos gerais de BI da Microsoft e como você pode se beneficiar ao obter os modelos de dados na nuvem.

## <a name="built-on-sql-server-analysis-services"></a>Criado no SQL Server Analysis Services
O Azure Analysis Services é compatível com os mesmos recursos incríveis já presentes no SQL Server Analysis Services Enterprise Edition. O Azure Analysis Services dá suporte a modelos tabulares no [nível de compatibilidade](analysis-services-compat-level.md) 1200 e 1400. Partições, segurança em nível de linha, relações bidirecionais e traduções: todos têm suporte. Os modos na memória e DirectQuery significam consultas muito rápidas em conjuntos de dados grandes e complexos.

Os modelos de tabela oferecem desenvolvimento rápido e são altamente personalizáveis. Para desenvolvedores, os modelos de tabela incluem o TOM (Modelo de Objeto de Tabela) para descrever objetos de modelo. O TOM é exposto em JSON por meio de [TMSL (Linguagem de Scripts de Modelo de Tabela)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) e da linguagem de definição de dados AMO por meio do namespace [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Melhor com o Azure
O Azure Analysis Services integra-se com vários serviços do Azure, permitindo que você crie soluções sofisticadas de análise. A integração com o [Azure Active Directory](../active-directory/active-directory-whatis.md) fornece acesso seguro e baseado em função para seus dados críticos. Integre com pipelines do [Azure Data Factory](../data-factory/introduction.md), incluindo uma atividade que carrega dados no modelo. A [Automação do Azure](../automation/automation-intro.md) e o [Azure Functions](../azure-functions/functions-overview.md) podem ser usados para coordenação leve de modelos usando código personalizado.

## <a name="get-up-and-running-quickly"></a>Entre rapidamente em funcionamento
No Portal do Azure, você pode [criar um servidor](analysis-services-create-server.md) em questão de minutos. E, com [modelos](../azure-resource-manager/resource-manager-create-first-template.md) do Azure Resource Manager e o PowerShell, você pode provisionar servidores usando um modelo declarativo. Com um único modelo, você pode implantar vários serviços com outros componentes do Azure, como contas de armazenamento e o Azure Functions. 

Depois que você tiver um servidor criado, é possível criar um modelo de tabela diretamente no portal do Azure. Com o novo (versão prévia) [recurso de designer da Web](analysis-services-create-model-portal.md) você pode se conectar a um Banco de Dados SQL, fonte de dados do SQL Data Warehouse do Azure, ou importar um arquivo do Power BI Desktop .pbix. As relações entre tabelas são criadas automaticamente, e você pode criar medidas ou editar o arquivo model.bim no formato json direitamente em seu navegador.

## <a name="scale-to-your-needs"></a>Escalar de acordo com suas necessidades
O Azure Analysis Services está disponível nas camadas de Desenvolvedor, Básica e Standard. Em cada nível, os custos do plano variam de acordo com a potência do processamento, QPUs e quantidade de memória. Quando você cria um servidor, é possível selecionar um plano de dentro de uma camada. Você pode alterar planos para cima ou para baixo na mesma camada ou atualizar para uma camada superior; mas você não pode fazer o downgrade de um nível mais alto para um nível inferior.

Escalar verticalmente, reduzir verticalmente ou pausar o servidor. Use o portal do Azure ou tenha controle total usando o PowerShell. Você paga apenas pelo que usa. Para saber mais sobre os diferentes planos e camadas e usar a calculadora de preços para determinar o plano certo para você, confira [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="keep-your-data-close"></a>Manter seus dados próximos a você
Servidores do Azure Analysis Services podem ser criados nas seguintes [regiões do Azure](https://azure.microsoft.com/regions/):

| Américas | Europa | Pacífico Asiático |
|----------|--------|--------------|
|  Sul do Brasil<br> Canadá Central<br> Leste dos EUA 2<br> Centro-Norte dos EUA<br> Centro-Sul dos Estados Unidos<br> Centro-Oeste dos EUA<br> Oeste dos EUA | Norte da Europa<br> Sul do Reino Unido<br> Europa Ocidental |   Sudeste da Austrália<br> Leste do Japão<br> Sudeste Asiático<br> Índia Ocidental  |

Novas regiões estão sendo adicionadas o tempo todo. Portanto, essa lista pode estar incompleta. Escolha um local ao criar o servidor no portal do Azure ou usando modelos do Azure Resource Manager. Para obter o melhor desempenho, escolha um local mais próximo da sua maior base de usuários. Garanta uma [alta disponibilidade](analysis-services-bcdr.md) ao implantar seus modelos em servidores redundantes em várias regiões.

## <a name="migrate-your-existing-tabular-models"></a>Migrar seus modelos de tabela existentes
Se você já tiver as soluções existentes de modelo do SQL Server Analysis Services local, é possível migrar para o Azure Analysis Services sem alterações significativas. Para migrar, você pode usar o SSDT para implantar o modelo para seu servidor. Ou, no SSMS, você pode usar o backup e a restauração ou TMSL.

Se você tiver fontes de dados local, é necessário instalar e configurar um [Gateway de dados no local](analysis-services-gateway.md). Se você tiver funções e membros de função já configurados, suas funções são migradas, mas você precisa adicionar novamente os membros de função usando o SSMS ou o PowerShell.

## <a name="connect-to-popular-data-sources"></a>Conectar-se a fontes de dados populares
O Azure Analysis Services oferece suporte à [conexão às fontes](analysis-services-datasource.md) de dados na nuvem e locais em sua organização. Combine dados de fontes de dados locais e na nuvem para uma solução de híbrida. 

Novos modelos de tabela 1400 usam o recurso moderno Obter Dados no SSDT, com base na linguagem da consulta da fórmula M. Com o recurso Obter Dados, você tem mais de transformação de dados e recursos de mashup e a capacidade de criar e editar suas próprias consultas de linguagem de fórmula avançadas M. Por exemplo, com modelos de tabela 1400, você pode modelar arquivos de dados no Armazenamento de Blobs do Azure.

## <a name="use-the-tools-you-already-know"></a>Use as ferramentas que você já conhece

![Ferramentas de desenvolvedor BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) para Visual Studio
Desenvolva e implante modelos com o [SQL Server Data Tools (SSDT) para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) gratuito. O SSDT inclui modelos de projeto do Analysis Services que o deixa pronto rapidamente. O SSDT agora inclui a moderna consulta de fonte de dados Obter Dados e a funcionalidade mashup para modelos de tabela 1400. Se você estiver familiarizado com o Obter Dados no Power BI Desktop e Excel 2016, já sabe como é fácil criar consultas de fonte de dados altamente personalizadas.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Gerencie seus servidores e bancos de dados modelo usando o [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Conecte-se aos servidores na nuvem. Execute scripts TMSL direitamente na janela de consulta XMLA e automatize as tarefas usando scripts TMSL. Novos recursos e funcionalidades acontecem rapidamente. O SSMS é atualizado mensalmente.

#### <a name="powershell"></a>PowerShell
As tarefas de gerenciamento de recurso de servidor, como criar servidores, suspender ou retomar operações de servidor ou alterar o nível de serviço (camada), usam cmdlets do AzureRM (Azure Resource Manager). Outras tarefas de gerenciamento de bancos de dados, como adição ou remoção de membros da função, processamento ou execução de scripts TMSL usam os cmdlets incluídos no módulo SqlServer. Os módulos AzureRM e SQL Server estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Seus dados estão seguros
![Visualizações de dados](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Autenticação
A autenticação de usuário para o Azure Analysis Services é feita pelo [AAD (Azure Active Directory)](../active-directory/active-directory-whatis.md). Quando tentam entrar em um banco de dados do Azure Analysis Services, os usuários usam uma identidade de conta da organização com acesso ao banco de dados que estão tentando acessar. Essas identidades de usuário devem ser membros do Azure Active Directory padrão da assinatura em que reside o servidor do Azure Analysis Services. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md).

#### <a name="data-security"></a>Segurança de dados
O Azure Analysis Services utiliza o Armazenamento de Blobs do Azure para persistir o armazenamento e os metadados em bancos de dados do Analysis Services. Os arquivos de dados no blob são criptografados usando Azure SSE (criptografia do servidor de blobs). Ao usar o modo Consulta Direta, apenas os metadados serão armazenados. Os dados reais são acessados na fonte de dados no momento da consulta.

#### <a name="on-premises-data-sources"></a>Fontes de dados locais
O acesso protegido aos dados que residem localmente na organização é feito com a instalação e a configuração de um [Gateway de dados local](analysis-services-gateway.md). Os gateways fornecem acesso a dados nos modos Consulta Direta e de memória interna. Quando um modelo do Azure Analysis Services se conecta a uma fonte de dados local, uma consulta é criada junto com as credenciais criptografadas da fonte de dados local. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para um Barramento de Serviço do Azure. O gateway local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados para a execução. Os resultados são enviados da fonte de dados para o gateway e, em seguida, para o banco de dados do Azure Analysis Services.

O Azure Analysis Services é regido pelo [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e pela [política de privacidade do Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para saber mais sobre a Segurança do Azure, confira a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="supports-the-latest-client-tools"></a>Dá suporte às ferramentas de cliente mais recentes
![Visualizações de dados](./media/analysis-services-overview/aas-overview-clients.png)

Ferramentas modernas de exploração de dados e visualização como o Power BI, Excel e ferramentas de terceiros fornecem aos usuários ideias altamente interativas e visualmente avançadas sobre seus dados de modelo.

Os clientes usam [bibliotecas de cliente](analysis-services-data-providers.md) MSOLAP, AMO ou ADOMD para se conectar aos servidores do Analysis Services. Os aplicativos cliente da Microsoft, como o Power BI Desktop e o Excel, instalam as três bibliotecas de cliente. Mas tenha em mente que, dependendo da versão ou da frequência de atualizações, as bibliotecas de cliente podem não ser as versões mais recentes exigidas pelo Azure Analysis Services. O mesmo se aplica a aplicativos personalizados ou outras interfaces, como AsCmd, TOM, ADOMD.NET. Esses aplicativos normalmente exigem a instalação manual das bibliotecas como parte de um pacote.


## <a name="get-help"></a>Obter ajuda

#### <a name="documentation"></a>Documentação
O Azure Analysis Services é simples de configurar e gerenciar. Você pode encontrar todas as informações necessárias para criar e gerenciar seus serviços de servidor aqui. A criação de um modelo de dados para implantar no seu servidor é muito semelhante à criação de um modelo de dados para implantar em um servidor local. Existe uma ampla biblioteca de artigos conceituais, procedimentais, tutoriais e de referência em [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### <a name="videos"></a>Vídeos
Confira alguns vídeos úteis em [Azure Analysis Services no Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogs
As coisas estão mudando rapidamente. Você sempre pode obter as informações mais recentes no [blog da equipe do Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) e no [blog do Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Comunidade
O Analysis Services tem uma comunidade de usuários vibrante. Participe das conversas no [Fórum do Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentários
Quer fazer sugestões ou solicitações de recursos? Deixe seus comentários nos [Comentários sobre o Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

Quer fazer sugestões sobre a documentação? Você pode adicionar comentários usando o Livefyre na parte inferior de cada artigo.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe mais sobre o Azure Analysis Services, é hora de começar. Saiba como [criar um servidor](analysis-services-create-server.md) no Azure. Quando o servidor estiver pronto, percorra o [Tutorial do Adventure Works](tutorials/aas-adventure-works-tutorial.md) para aprender a criar um modelo de tabela totalmente funcional e implantá-lo em seu servidor.
