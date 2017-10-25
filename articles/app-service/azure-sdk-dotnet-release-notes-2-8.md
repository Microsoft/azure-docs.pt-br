---
title: "Notas de versão SDK do Azure para .NET 2.8"
description: "Notas de versão SDK do Azure para .NET 2.8"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>SDK do Azure para .NET 2.8, 2.8.1 e 2.8.2
## <a name="overview"></a>Visão geral
Esse artigo contém as notas de versão (que inclui problemas conhecidos e alterações recentes) do SDK do Azure para .NET versões 2.8, 2.8.1 e 2.8.2. 

Para ter acesso à lista completa dos novos recursos e atualizações desta versão, consulte a notificação do [SDK 2.8 do Azure para Visual Studio 2013 e Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

## <a name="azure-sdk-for-net-28"></a>SDK do Azure para .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Baixar o SDK do Azure para .NET 2.8
[SDK do Azure para .NET 2.8 para Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[SDK do Azure para .NET 2.8 para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Suporte do .NET 4.5.2
#### <a name="known-issues"></a>Problemas conhecidos
O SDK .NET 2.8 do Azure permite criar pacotes de Serviço de Nuvem do .NET 4.5.2. No entanto, a estrutura do .NET 4.5.2 só será instalada nas imagens do SO convidado padrão na versão do SO convidado de janeiro de 2016. Até lá, a estrutura do .NET 4.5.2 estará disponível por meio de uma versão distinta do SO convidado, de 2 de novembro de 2015. Confira a página [Matriz de Compatibilidade de Versões de SO Convidado e do SDK do Azure](../cloud-services/cloud-services-guestos-update-matrix.md) para acompanhar quando a imagem será lançada.  Quando a imagem de 2 de novembro de 2015 for lançada, você poderá optar por usar essa imagem atualizando seu arquivo de configuração do Serviço de Nuvem (.cscfg). No arquivo de configuração de serviço, defina o atributo osVersion do elemento ServiceConfiguration para a cadeia de caracteres "WA-GUEST-OS-4.26_201511-02". Se você optar por aceitar usar essa imagem, você não receberá mais atualizações automáticas para o SO convidado. Para obter as atualizações automáticas, a osVersion deve ser definida como "*" e o .NET 4.5.2 só estará disponível por meio de atualizações automáticas em janeiro de 2016.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure
#### <a name="known-issues"></a>Problemas conhecidos
Durante a criação de um projeto de **Modelo da Fábrica de Dados** envolvendo amostras de dados, o script do Azure PowerShell pode falhar se a versão do Azure PowerShell instalada na máquina for superior a 0.9.8.

Para criar esse tipo de projeto com êxito, você deve instalar a [versão 0.9.8 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Ferramentas do gerenciador de recursos do Azure
#### <a name="breaking-changes"></a>Alterações de última hora
O script do PowerShell fornecido pelo Grupo de Recursos do Azure foi atualizado nesta versão para funcionar com os novos cmdlets do Azure PowerShell versão 1.0.  Esse novo script não funcionará com o Visual Studio, ao usar uma versão do SDK anterior à 2.8.  

Os scripts de projetos criados em versões anteriores do SDK não serão executados do Visual Studio, quando estiverem usando o SDK 2,8.  Todos os scripts continuarão a funcionar fora do Visual Studio com a versão apropriada dos cmdlets do Azure PowerShell.  

O SDK 2.8 requer a versão 1.0 dos cmdlets do Azure PowerShell.  Todas as outras versões do SDK exigem a versão 0.9.8 dos cmdlets do Azure PowerShell.  Para saber mais, confira [este blog](http://go.microsoft.com/fwlink/?LinkID=623011) .

### <a name="web-tools-extensions"></a>Extensões de Ferramentas da Web
#### <a name="known-issues"></a>Problemas conhecidos
Os seguintes problemas conhecidos serão abordados na versão seguinte.

* O Serviço de Aplicativo relacionado aos gestos do Gerenciador de Servidores e Nuvem para ambientes de não produção (como clientes do Azure China ou Azure Stack) não funciona. Para os clientes nessas áreas afetadas, baixar o perfil de publicação do portal do Azure habilitará a capacidade de publicação. Uma versão futura irá reparar gestos como “Anexar Depurador” e “Exibir Logs de Streaming” para clientes do Azure China e Azure Stack. 
* Os clientes poderão ver erros durante a criação do Serviço de Aplicativo quando a instância do App Insights à qual eles estão implementando estiver em uma região que não seja o Leste dos EUA. Nesses cenários, criar um Serviço de Aplicativo no portal e baixar o perfil de publicação habilitará cenários de publicação. 

### <a name="azure-hdinsight-tools"></a>Ferramentas do Azure HDInsight
#### <a name="new-updates"></a>Novas atualizações
* Você pode executar a consulta do Hive no cluster usando o HiveServer2 com quase nenhuma sobrecarga e ver os logs de trabalho em tempo real.
* Usando o novo Modo de Exibição de Execução de Tarefa do Hive, você pode examinar seu trabalho mais detalhadamente, encontrar mais detalhes e identificar possíveis problemas.

Para saber mais, confira [SDK 2.8 do Azure para Visual Studio 2013 e Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>SDK do Azure para .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemas conhecidos do Visual Studio 2013 e do Visual Studio 2015
1. Os WebJobs disparados são publicados em slots aparecerão, causarão falha e não farão o agendamento, mas enviarão o WebJob por push para o Azure. Os clientes que precisarem de um trabalho agendado poderão usar o Portal do Azure para configurar o agendamento do trabalho Web. 
2. Os clientes do Python podem enfrentar problemas de depuração. A equipe de serviço está criando uma correção para isso, mas se os clientes forem afetados, informe à Microsoft nos fóruns ou nas seções de comentários do blog de comunicado ou das notas de versão. 
3. Os clientes em determinadas regiões (por exemplo, Sul da Índia) devem passar por erros de provisionamento do Serviço de Aplicativo. Isso é consistente com o portal, e os clientes que tiverem esse problema podem usar o portal do Azure para solicitar acesso a fim de publicar nessas regiões geográficas. Depois que eles solicitarem o acesso a essas regiões usando o portal do Azure, o provisionamento deve funcionar. 

## <a name="azure-sdk-for-net-282"></a>SDK do Azure para .NET 2.8.2
Após a instalação das ferramentas 2.8.2, os clientes podem enfrentar o problema a seguir.         

* Se você estiver usando o Windows 10 e não tiver instalado o Internet Explorer, poderá receber um erro de "Internet Explorer não encontrado".
  Para resolver o problema, instale o Internet Explorer usando a caixa de diálogo Adicionar/Remover Componentes do Windows.

Se você observar esse problema, use o recurso Enviar um Smiley para relatá-lo.

Para obter mais informações, confira [esta](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) postagem.

## <a name="other-updates"></a>Outras atualizações
Para obter outras atualizações, confira a [postagem de comunicado do SDK do Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Consulte também
[Postagem de anúncio do SDK 2.8 do Azure](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Informações de suporte e de desativação do SDK do Azure para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx)

