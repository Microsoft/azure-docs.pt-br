---
title: "Notas de versão SDK do Azure para .NET 2.5.1"
description: "Notas de versão SDK do Azure para .NET 2.5.1"
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: 
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 357e58665f5cdf6ea9a3fcaee4a390f2b0d2045f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Notas de versão SDK do Azure para .NET 2.5.1
Este documento contém as notas de versão do SDK do Azure para a versão do .NET 2.5.1. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Notas de versão SDK do Azure para .NET 2.5.1
Estes são os novos recursos e atualizações no SDK do Azure para .NET 2.5.1.

* Novos recursos\cenários relacionados a **Extensões de Ferramentas da Web**. 
  
  * Sites do Azure foi renomeado para Serviço de Aplicativo do Azure. 
  * Suporte a Aplicativos de API do Azure (visualização) foi adicionado para que os clientes possam publicar projetos do ASP.NET como aplicativos de API e, em seguida, usar o gesto Adicionar > Cliente de Aplicativos de API do Azure em projetos C# para gerar código com base na estrutura do Aplicativo de API implantado. 
  * O nó Sites no Gerenciador de Servidores foi preterido em prol do nó do Serviço de Aplicativo do Azure, que contém suporte para agrupamento de Aplicativos de API do Azure, Aplicativos Móveis e Aplicativos Web com base em grupo de recursos.
  * Suporte a Aplicativos Móveis do Azure (visualização) foi adicionado para que os clientes possam criar novos projetos de Aplicativos Móveis, adicionar controladores de Aplicativos Móveis, publicar os projetos e depurar aplicativos remotamente.
  * O gesto Adicionar > Cliente de Aplicativo de API do Azure agora dá suporte a arquivos locais Swagger JSON, para que os desenvolvedores de API Web possam usar NuGets de terceiros, como Swashbuckle, para gerar Swagger ou criá-lo manualmente. Dessa forma, os desenvolvedores de cliente podem usar os recursos de geração de código para consumir qualquer ponto de extremidade Swagger em projetos C#. 
  * Caixas de diálogos de publicação de Aplicativo Web e Aplicativo de API foram aprimoradas para dar suporte ao conceito de Portal do Azure do agrupamento de recursos e seleção/criação de Grupos de Recursos do Azure e Planos de Serviço de Aplicativo são representadas na nova caixa de diálogo de provisionamento do Aplicativo Web e Aplicativo de API. 
  * Nós do Gerenciador de Servidores do Aplicativo de API do Azure fornecem links para o link profundo de Aplicativos de API no Portal do Azure, bem como outros recursos, como Streaming de Log e Depuração Remota.
    
    Para problemas conhecidos e limitações atuais no SDK do Azure .NET 2.5.1 [esta](app-service-release-notes.md#known_issues_2_5_1) seção abaixo.
* Novo recursos\cenários relacionados a **Ferramentas do HDInsight** no Visual Studio estão habilitados nesta versão. 
  
  * Validação local de scripts do hive. Clique no botão de script Validar na barra de ferramentas para ver se há erros no script. 
  * Depuração de trabalhos de Hive aprimorada. Você pode depurar trabalhos de Hive acessando os logs de Yarn no Visual Studio. Se seu aplicativo tiver problemas de desempenho, investigar os logs YARN fornecerá informações úteis.
  * (Visualização pública) Preenchimento automático de palavra-chave e suporte IntelliSense para Hive. Para ajudar a criar scripts do Hive, as ferramentas HDInsight para Visual Studio adicionaram suporte para preenchimento automático de palavra-chave e IntelliSense para Hive.
  * Suporte para Storm. Agora você pode usar as ferramentas HDInsight para Visual Studio para desenvolver topologias do Storm/Spouts/Bolts em C#. Você pode enviar a topologia desenvolvida para um cluster Storm e ver o status da topologia/bolt/spout. Você pode usar logs do sistema e logs de cliente para solucionar Bolts/Spouts/topologias Storm. Você também pode usar os ativos JAVA existentes no Storm no HDInsight.
    
    Para obter mais informações, consulte [Introdução ao uso das ferramentas do HDInsight Hadoop para Visual Studio](../hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Limitações e problemas conhecidos do SDK do Azure para .NET 2.5.1
* O recurso de Aplicativos de API do Azure está visível como um destino de implantação para Aplicativos Móveis. Aplicativos Web devem ser o único destino para Aplicativos Móveis até uma versão subsequente. 
* Provisionamento de Aplicativo de API do Azure pode resultar em êxito, mas falha intermitente em atualizar o andamento na janela de Atividade do Serviço de Aplicativo do Azure. A solução alternativa é verificar o status do novo Aplicativo de API do Azure no Portal do Azure. 
* A experiência Arquivo > Novo Projeto > Aplicativo de API > F5 resulta em um erro de HTTP porque não há nenhum default/index.html. A solução alternativa é navegar manualmente para a URL /api/values. 
* Intermitentemente, ícones do Gerenciador de Servidores são exibidos na forma bidimensional. Reiniciar o VS resolve esse problema. 
* Se uma exceção for é lançada durante o provisionamento de Aplicativo Web ou Aplicativo de API (como erros de cota excedida ou nome duplicado de gateway de Aplicativo de API do Azure), os erros mostram algum texto JSON bruto. 
* Problemas intermitentes de criação do projeto quando o Application Insights é verificado na hora da criação do projeto.
* Ocasionalmente, o código de Cliente de Aplicativo de API do Azure gerado está s em namespaces, e eles precisam ser incluídos manualmente (ou importado automaticamente por meio de dicas do Visual Studio) para que o código seja compilado. 
* Projetos de Aplicativo Móvel devem ser publicados em aplicativos Web, mas você deve escolher um site criado como Aplicativo móvel no Portal do Azure, já que projetos de Aplicativo Móvel exigem um banco de dados. 
* A página inicial para aplicativos móveis usa o termo "serviço móvel", em vez de "aplicativos móveis" 
* A criação do projeto de Aplicativo Móvel pode levar até um minuto para ser criada. 
* Durante o provisionamento do Aplicativo de API (em alguns casos), um erro é retornado da API do Azure refletindo que as permissões não puderam ser definidas corretamente, enquanto o Aplicativo de API foi configurado corretamente e está pronto para uso. Você pode definir manualmente as permissões usando o Portal do Azure.
* Não há suporte para o Application Insights em modelos do Aplicativo de API e Aplicativos Móveis.
* Projetos de Aplicativo de API não podem ser usados em conjunto com os projetos de Serviço de Nuvem.
* Modelos de projeto de Aplicativo de API só estão disponíveis em C#.
* Consumo do Aplicativo de API através do menu de contexto "Adicionar Cliente do Aplicativo de API do Azure" só têm suporte em C#.

