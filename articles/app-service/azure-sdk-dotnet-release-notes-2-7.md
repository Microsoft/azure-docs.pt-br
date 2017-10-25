---
title: "Notas de versão do SDK do Azure para .NET 2.7 e .NET 2.7.1"
description: "Notas de versão do SDK do Azure para .NET 2.7 e .NET 2.7.1"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notas de versão do SDK do Azure para .NET 2.7 e .NET 2.7.1
## <a name="overview"></a>Visão geral
Este documento contém as notas de versão do SDK do Azure para .NET 2.7. 

Este documento contém as notas de versão do SDK do Azure para a versão .NET 2.7.1.

O SDK 2.7 do Azure tem suporte apenas no Visual Studio 2015 e Visual Studio 2013. [O SDK 2.6 do Azure](https://azure.microsoft.com/downloads/) é o último SDK com suporte para Visual Studio 2012.

Para obter informações detalhadas sobre esta versão, confira a [postagem de anúncio do SDK 2.7 do Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) e a [postagem de anúncio do SDK 2.7.1 do Azure](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>SDK do Azure para .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Aprimoramentos de inscrição do Visual Studio 2015
O SDK 2.7 do Azure para Visual Studio 2015 dá suporte aos novos recursos de gerenciamento de identidade do Visual Studio 2015.  Isso inclui suporte para contas que acessam o Azure por meio do controle de acesso com base em função, provedores de soluções de nuvem, DreamSpark e outros tipos de conta e assinatura.

Os aprimoramentos de inscrição incluídos com o Azure SDK 2.7 só estão disponíveis no Visual Studio 2015. O suporte para Visual Studio 2013 está incluído no SDK do Azure 2.7.1.

### <a name="mobile-sdk"></a>SDK móvel
Modelos de **aplicativos móveis** atualizados para refletir o [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e o processo de instalação mais recentes.

### <a name="service-bus"></a>Barramento de Serviço
Correções de bugs e aprimoramentos gerais. Para obter detalhes sobre atualizações e recursos, consulte as notas de versão do [NuGet do Barramento de Serviço](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)mais recente.

### <a name="hdinsight-tools"></a>Ferramentas do HDInsight
Nesta versão, as seguintes atualizações foram feitas. Essas atualizações estão no modo de visualização. Para saber mais, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

* Gráficos de hive para Hive em trabalhos Tez
* Suporte total para IntelliSense do DML do Hive
* Suporte de modelo do pig
* Modelos de Storm para serviços do Azure

#### <a name="breaking-changes"></a>Alterações de última hora
* O antigo projeto do **Storm** deve ser atualizado ao usar esta versão das ferramentas. Para saber mais, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).
* Não há mais suporte para o Visual Studio Web Express. Para saber mais, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Ferramentas do Serviço de Aplicativo do Azure
Nesta versão, as seguintes atualizações foram feitas às extensões de ferramentas da Web. Para saber mais, confira [este](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

* Suporte para contas do DreamSpark adicionado
* Total de alterações das ferramentas do Azure feitas para oferecer suporte às novas APIs de gerenciamento de recursos do Azure
* Adição de suporte para o Serviço de Aplicativo do Azure ao [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Problemas conhecidos
Nós de slot de implantação de aplicativo Web não aparecem sob o nó de Slots no Gerenciador de Servidores, e nós filho de slots de implantação de aplicativo Web não carregaram no Gerenciador de nuvem. Esse problema foi resolvido e preparado para a próxima versão do SDK. 

### <a name="cloud_explorer"></a>Gerenciador de nuvem para Visual Studio 2015
O SDK 2.7 do Azure inclui o Gerenciador de Nuvem para Visual Studio 2015, que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações chave do desenvolvedor de dentro do Visual Studio. 

O Gerenciador de Nuvem dá suporte a:

* Exibições de grupo de recursos e tipo de recurso de seus recursos do Azure 
* Pesquisar recursos por nome (disponível no modo de exibição do tipo de recurso)
* Suporte para assinaturas e recursos que têm o controle de acesso com base em função (RBAC) aplicado 
* Painel de ação integrada que mostra ações voltadas para desenvolvedores específicas para os recursos selecionados. Por exemplo: anexe o depurador remoto para máquinas virtuais criadas usando a pilha do Gerenciador de Recursos, exiba dados de diagnóstico para máquinas virtuais, etc.
* Painel de propriedades integradas que mostra as propriedades voltadas para desenvolvedores comumente necessárias durante o desenvolvimento e teste 
* A alternância rápida da conta a ser usada ao enumerar recursos (use o comando Configurações na barra de ferramentas) 
* Filtragem de assinaturas a serem usadas ao enumerar recursos (use o comando Configurações na barra de ferramentas) 
* Links profundos para o portal do Azure para gerenciamento de recursos e grupos de recursos 

### <a name="azure-resource-manager-tools"></a>Ferramentas do gerenciador de recursos do Azure
As ferramentas do Gerenciador de Recursos do Azure foram atualizadas para funcionar com controle de acesso com base em função (RBAC) e os novos tipos de assinatura.  A capacidade de usar novas contas de armazenamento, além do armazenamento clássico, para armazenar os artefatos durante a implantação está incluída nessas alterações.  

Se você estiver usando um projeto do grupo de recursos do Azure de uma versão anterior do SDK com o SDK 2.7, um novo script de implantação é necessário para implantar usando uma nova conta de armazenamento em vez do armazenamento clássico.  Você será avisado antes que as alterações sejam feitas ao seu projeto para adicionar o novo script.  O script antigo será renomeado e você precisará fazer modificações ao novo script manualmente.

### <a name="storage-explorer-tools"></a>Ferramentas do Gerenciador de Armazenamento
* Suporte à exibição de Blobs acrescentados. Mais informações disponíveis [nesta postagem do blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Suporte à exibição de contas de armazenamento Premium por meio do Gerenciador de Servidores. O Gerenciador de Servidores só exibe os blobs de página para contas de armazenamento premium, uma vez que eles são o único tipo com suporte para contas de armazenamento premium.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Ferramentas da Azure Data Factory para o Visual Studio
Introdução às **Ferramentas da Azure Data Factory** para o Visual Studio. Abaixo estão os recursos habilitados. Para obter mais informações, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=617530) .

* **Criação com base em modelo**: Selecione modelos com base em caso de uso, modelos de movimentação de dados ou modelos de processamento de dados para implantar uma solução de integração de dados de ponta a ponta e começar rapidamente a utilizar Data Factory. 
* **Integração com o Gerenciador de Soluções para criar e implantar as entidades de Data Factory**: crie e implante pipelines e entidades relacionadas como projetos do Visual Studio. 
* **Integração com o modo de exibição de diagrama de interação visual durante a criação**: crie visualmente pipelines e conjuntos de dados com o auxílio da exibição de diagrama. 
* **Integração com o Gerenciador de Servidores para navegação e interação com entidades já implantadas**: use o Gerenciador de Servidores para procurar Data factories já implantados e entidades correspondentes. Importe um Data Factory implantado ou qualquer entidade (Pipeline, serviço vinculado, conjuntos de dados) no seu projeto. 
* **Edição de JSON com validação de esquema e IntelliSense avançado**: configure de forma eficiente e edite documentos JSON de entidades de Data Factory com IntelliSense avançado e validação de esquema 
* **Publicação em vários ambientes**: publique pipelines criados em ambientes de desenvolvimento, teste ou produção criando arquivos de configuração separados para cada ambiente.
* **Suporte ao processamento de dados com base em Pig, Hive e .Net**: suporte para scripts Pig e Hive no projeto de Data Factory. Suporte para referenciar o Projeto C# para gerenciar atividade do .Net.

## <a name="azure-sdk-for-net-271"></a>SDK do Azure para .NET 2.7.1
A seção a seguir contém atualizações que foram introduzidas com o SDK do Azure para a versão .NET 2.7.1.

### <a name="hdinsight-tools"></a>Ferramentas do HDInsight
Para obter mais explicações sobre atualizações de ferramentas de HDInsight, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

* Exibição de Operador de Trabalho do Hive (um novo recurso)
  
    Para ajudá-lo a compreender melhor sua consulta de Hive, o recurso de Exibição de Operador Hive foi adicionado. Para ver todos os operadores dentro de um vértice, clique duas vezes nos vértices do gráfico do trabalho. Para exibir mais detalhes de um operador específico, passe o mouse sobre esse operador.
* Marcador de Erro do Hive (um novo recurso)
  
    Para que você possa exibir os erros de gramática instantaneamente, o recurso de Marcador de Erro do Hive foi adicionado. Além disso, as mensagens de erro foram aprimoradas e agora você pode ver erros gramaticais detalhados instantaneamente (até nesta versão, era necessário enviar um script do Hive ao cluster e aguardar algum tempo antes de obter detalhes sobre a mensagem de erro).  
* Gráfico de topologia Storm (um novo recurso)
  
    Visualizar é muito importante quando você deseja verificar se a topologia está funcionando conforme o esperado. Nesta versão, adicionamos visualização para gráficos Storm. Você pode visualizar as métricas importantes para a sua topologia (por exemplo, uma cor indica se um determinado Bolt está "ocupado" ou não). Você pode também clicar duas vezes o Bolt/Spout para ver mais detalhes.
* Suporte para clusters de HDInsight que foram criados no Portal do Azure (uma correção de bug)
  
    Agora você pode usar o Visual Studio para exibir e enviar trabalhos para todos os clusters de HDInsight, independentemente de onde o cluster foi criado.
* Suporte ao IntelliSense mais e mais rápido metadados Hive carregar (uma melhoria)
  
    O IntelliSense foi aprimorado, adicionando sugestões mais amigáveis ao usuário. Por exemplo, o alias de tabela pode agora também ser sugerido no IntelliSense para que você possa escrever sua consulta mais facilmente. Além disso, melhoramos o carregamento dos metadados do Hive para que o mesmo demore apenas alguns segundos para listar todos os bancos de dados, tabelas e colunas de sua metastore Hive.

Para obter mais explicações sobre atualizações de ferramentas de HDInsight, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Aprimoramentos no Visual Studio 2013
* SDK do Azure 2.7.1 permite ao Visual Studio 2013 acessar contas do Azure e assinaturas através do Dreamspark, provedores de soluções de nuvem e controle de acesso com base em função.
* Com o SDK do Azure 2.7.1, a nova janela da ferramenta Gerenciador de nuvem agora também está disponível no Visual Studio 2013.

### <a name="known-issues"></a>Problemas conhecidos
Instalando o Azure SDK 2.6 ou 2.7.1 para Visual Studio Community 2013 em um sistema operacional que não está em inglês exibirá um aviso de que os recursos em que estão em inglês e que não estão em inglês do Visual Studio podem ser incompatíveis. Esse aviso pode ser descartado com segurança. Ele só ocorrerá se a máquina não tinha uma instalação anterior do Visual Studio Community 2013 e você estiver instalando o SDK em um sistema operacional que não está em inglês. O aviso é exibido depois que o pacote de idiomas aplica os recursos do RTM para o Visual Studio, mas antes de aplicar a Atualização 4. Ignorar o aviso permitirá que o pacote de idiomas continue e conclua a aplicação da versão de Atualização 4 do conteúdo do pacote de idioma.

Projetos do LightSwitch não são compatíveis com esta versão. Esse problema será resolvido na próxima versão do SDK.

## <a name="also-see"></a>Consulte também
[Postagem de anúncio do SDK 2.7.1 do Azure](http://go.microsoft.com/fwlink/?LinkId=623850)

[Postagem de anúncio do SDK 2.7 do Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Informações de suporte e de desativação do SDK do Azure para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

