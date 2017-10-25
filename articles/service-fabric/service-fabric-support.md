---
title: "Saiba mais sobre as opções de Suporte do Azure Service Fabric | Microsoft Docs"
description: "Versões de cluster do Azure Service Fabric com suporte e links para transmitir tíquetes de suporte"
services: service-fabric
documentationcenter: .net
author: pkc
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: pkc
ms.openlocfilehash: cd01d79c83749b38cc72a468e167ade5e264e1c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte do Azure Service Fabric

Para fornecer o suporte apropriado para os clusters do Service Fabric nos quais suas cargas de trabalho do aplicativo são executadas, organizamos várias opções para você. Dependendo do nível de suporte necessário e da severidade do problema, escolha as opções ideais. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Relatar problemas de produção ou solicitar suporte pago para o Azure

Para relatar problemas no seu cluster do Service Fabric implantado no Azure, abra um tíquete de suporte [no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou no [portal de suporte da Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte da Microsoft para o Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Relatar problemas de produção ou solicitar o suporte pago para clusters independentes do Service Fabric

Para relatar problemas no seu cluster do Service Fabric implantado localmente ou em outras nuvens, abra um tíquete para obter suporte profissional no [portal de suporte da Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:

- [Suporte Profissional da Microsoft para o local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Relatar problemas do Azure Service Fabric 
Organizamos um repositório GitHub para relatar problemas do Service Fabric.  Também estamos monitorando de forma ativa os fóruns a seguir.

### <a name="github-repo"></a>Repositório GitHub 
Relate problemas do Azure Service Fabric no [repositório Git de problemas do Service Fabric](https://github.com/Azure/service-fabric-issues). Esse repositório destina-se a relatar e acompanhar problemas com o Azure Service Fabric e a ser usado para fazer pequenas solicitações de recursos. **Não use o repositório para relatar problemas de site ativo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow e fóruns do MSDN
A [marcação Service Fabric no StackOverflow][stackoverflow] e o [fórum do Service Fabric no MSDN][msdn-forum] devem ser usados para fazer perguntas sobre o funcionamento da plataforma e como determinadas tarefas podem ser realizadas com ela.

### <a name="azure-feedback-forum"></a>Fórum de Comentários do Azure
O [Fórum de Comentários do Azure sobre o Service Fabric][uservoice-forum] é o melhor local para enviar grandes ideias de recursos para o produto, pois examinamos as solicitações mais populares como parte de nosso planejamento de médio a longo prazo. Incentivamos você a conseguir suporte para suas sugestões na comunidade.


## <a name="supported-service-fabric-versions"></a>Versões do Service Fabric com suporte.

Verifique se o cluster sempre executa uma versão do Service Fabric com suporte. Quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir da data do comunicado. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os documentos a seguir para obter detalhes sobre como manter o cluster executando uma versão do Service Fabric com suporte.

- [Atualizar a versão do Service Fabric em um cluster do Azure ](service-fabric-cluster-upgrade.md)
- [Atualizar a versão do Service Fabric em um cluster de servidores independente do Windows Server ](service-fabric-cluster-upgrade-windows-server.md)
 
Veja a seguir a lista das versões do Service Fabric com suporte e as datas de fim de suporte.

| **Cluster de tempo de execução do Service Fabric** | **Compatível com SDK/versões do pacote NuGet** | **Data de fim do suporte** |
| --- | --- | --- |
| Todas as versões de cluster anteriores a 5.3.121 |Menor ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* |Menor ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* |Menor ou igual à versão 2.4 |10 de maio de 2017     |
| 5.5.* |Menor ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* |Menor ou igual à versão 2.6 |13 de outubro de 2017    |
| 5.7.* |Menor ou igual à versão 2.7 |Versão atual e, portanto, sem data de término

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões prévias do Service Fabric – sem suporte para uso em produção.
De tempos em tempos, lançamos versões com recursos significativos sobre os quais queremos comentários e que são lançadas como versões prévias. Essas versões prévias devem ser usadas apenas para fins de teste. O cluster de produção sempre deve estar executando uma versão do Service Fabric estável e com suporte. Uma versão prévia sempre começa com um número de versão principal e secundária de 255. Por exemplo, se você vir uma versão 255.255.5703.949 do Service Fabric, essa versão só deverá ser usada em clusters de teste e é uma versão prévia. Esses lançamentos de versões prévias também serão comunicados no [Blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e terão detalhes sobre os recursos incluídos.

Não há nenhuma opção de suporte pago para essas versões prévias. Use uma das opções listadas em [Relatar problemas do Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) para fazer perguntas ou fornecer comentários.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar a versão do Service Fabric em um cluster do Azure ](service-fabric-cluster-upgrade.md)
- [Atualizar a versão do Service Fabric em um cluster de servidores independente do Windows Server ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
