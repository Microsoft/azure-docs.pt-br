---
title: "Notas de versão do SDK do Azure para .NET 2.9"
description: "Notas de versão do SDK do Azure para .NET 2.9"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 199f0906f73d693d7cd4b73c928f23ae83b99596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Notas de versão do SDK do Azure para .NET 2.9

Este tópico inclui as notas de versão para as versões 2.9 e 2.9.6 do SDK do Azure para .NET.

##<a name="azure-sdk-for-net-296-release-summary"></a>Resumo da versão do SDK do Azure para .NET 2.9.6

Data do lançamento: 16/11/2016
 
Nenhuma alteração significativa do Azure SDK 2.9 foi introduzida nesta versão. Também não há nenhum processo de atualização necessário para aproveitar esse SDK com projetos de Serviço de Nuvem existentes.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 versão Release Candidate

- No Visual Studio 2017 RC, esta versão do SDK do Azure para .NET é integrada à Carga de Trabalho do Azure. Todas as ferramentas que você precisa para fazer o desenvolvimento do Azure farão parte do Visual Studio 2017 RC no futuro. Para o Visual Studio 2015 e o Visual Studio 2013, o SDK ainda estará disponível por meio do WebPI. Suspenderemos o SDK do Azure para versões .NET para o Visual Studio 2013, quando o Visual Studio 2017 for lançado como um produto final. Siga este link para baixar o RC do Visual Studio 2017: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

- Foi alterado o comportamento para armazenar somente uma cadeia de conexão parcial somente com a chave substituída por um token de cadeia de conexão do armazenamento de diagnóstico dos Serviços de Nuvem. A chave de armazenamento real agora é armazenada na pasta de perfil do usuário para que seu acesso possa ser controlado. O Visual Studio lerá a chave de armazenamento da pasta de perfil de usuário para depuração local e processo de publicação. 
- Em resposta à alteração descrita acima, a equipe do Visual Studio Online aprimorou o modelo de tarefa de implantação dos Serviços de Nuvem do Azure para que os usuários possam especificar a chave de armazenamento para configurar a extensão de diagnóstico ao publicar no Azure na Implantação e Integração Contínua.
- Tornamos possível armazenar a cadeia de conexão segura e a geração de tokens para Diagnóstico do Azure (WAD) a fim de ajudá-lo a solucionar problemas de configuração em ambientes.
 
### <a name="windows-server-2016-virtual-machines"></a>Máquinas virtuais do Windows Server 2016

- O Visual Studio agora oferece suporte à implantação de Serviços de Nuvem nas máquinas virtuais da Família do SO 5 (Windows Server 2016). Para serviços de nuvem existentes, é possível alterar suas configurações para direcionar a nova Família de SO. Ao criar novos serviços de nuvem, se você optar por criar o serviço usando o .net 4.6 ou posterior, isso padronizará o serviço para usar a Família do SO 5.  Para obter mais informações, é possível examinar a [tabela de suporte da Família do SO Convidado](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Problemas conhecidos

- O SDK 2.9.6 para .NET do Azure introduziu uma restrição que bloqueia a implantação de projetos usando estruturas .NET sem suporte (como .NET 4.6) para qualquer Família de SO anterior a 5. Uma solução alternativa é fornecida [aqui](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Cache na função do Azure 

- O suporte para o Cache na Função do Azure termina em 30 de novembro de 2016. Para obter mais detalhes, clique [aqui](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Modelos do Azure Resource Manager para o Azure Stack

- Apresentamos o Azure Stack como um destino de implantação para seus Modelos do Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Resumo do SDK para .NET 2.9

## <a name="overview"></a>Visão geral
Este documento contém as notas de versão do SDK do Azure para a versão do .NET 2.9. 

Para obter informações detalhadas sobre atualizações nesta versão, consulte a [Postagem de anúncio do SDK do Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Atualização 2 do SDK do Azure 2.9 para Visual Studio 2015 e Visual Studio "15" Preview
Esta atualização inclui as seguintes correções de bug:

* Problema relacionado à API REST de Geração de Cliente no qual a cadeia de caracteres "Tipo Desconhecido" seria exibida como o nome da pasta de geração de código e/ou o nome do namespace descartado no código gerado.
* Problema relacionado a WebJobs Agendados no qual as informações de autenticação estavam falhando em serem passadas para o processo de provisionamento do Agendador.

Esta atualização inclui o novo recurso a seguir:

* Suporte para Serviços de Aplicativos secundários na guia "Serviços" da caixa de diálogo de provisionamento do Serviço de Aplicativo. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Atualização 2 das Ferramentas do Azure Data Lake para Visual Studio 2015
Essa atualização inclui o seguinte:

* **Ferramentas do Azure Data Lake** para Visual Studio agora estão mescladas ao SDK do Azure para .NET. A ferramenta é instalada automaticamente ao instalar o SDK do Azure. 
  
    A ferramenta é atualizada com frequência, acesse [aqui](http://aka.ms/datalaketool) para obter as atualizações.
* **Gerenciador de Servidores** agora permite exibir tudo e criar algumas entidades de metadados U-SQL. Para saber mais, confira [este](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.

## <a name="hdinsight-tools"></a>Ferramentas do HDInsight
**Ferramentas do HDInsight** para Visual Studio agora dão suporte ao HDInsight versão 3.3, incluindo a exibição de gráficos Tez e outras correções de linguagem.

## <a name="azure-resource-manager"></a>Gerenciador de Recursos do Azure
Essa versão adiciona o suporte para [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) para modelos do Resource Manager.

## <a name="see-also"></a>Consulte também
[Postagem de anúncio do SDK 2.9 do Azure](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

