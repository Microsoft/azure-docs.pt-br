---
title: Saiba mais sobre as opções de suporte do Azure Service Fabric
description: Versões de cluster do Azure Service Fabric com suporte e links para transmitir tíquetes de suporte
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92316490"
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte do Azure Service Fabric

Criamos várias opções de solicitação de suporte para atender às necessidades de gerenciamento de seus clusters de Service Fabric e cargas de trabalho de aplicativo. Dependendo da urgência do suporte necessário e da gravidade do problema, você pode escolher a opção certa para você.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Relatar problemas de produção ou solicitar suporte pago para o Azure

Para relatar problemas relacionados ao cluster de Service Fabric em execução no Azure, abra um tíquete de suporte [no portal do Azure ou no portal de suporte da](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) [Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte da Microsoft para o Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> Clusters em execução em uma camada de confiabilidade bronze ou cluster de nó único permitirão que você execute somente cargas de trabalho de teste. No caso de problemas com um cluster em execução em confiabilidade bronze ou cluster de nó único, a equipe de suporte da Microsoft ajudará você a mitigar o problema, mas não executará uma análise da causa raiz. Para obter mais informações, consulte [as características de confiabilidade do cluster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Para saber mais sobre o que é necessário para um cluster pronto para produção, confira a [lista de verificação de preparação para produção](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Relatar problemas de produção ou solicitar o suporte pago para clusters independentes do Service Fabric

Para relatar problemas relacionados a clusters Service Fabric executados localmente ou em outras nuvens, você pode abrir um tíquete para suporte profissional no [portal de suporte da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Saiba mais sobre:

- [Suporte Profissional da Microsoft para o local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Relatar problemas do Azure Service Fabric

Organizamos um repositório GitHub para relatar problemas do Service Fabric.  Também estamos monitorando de forma ativa os fóruns a seguir.

### <a name="github-repo"></a>Repositório GitHub 

Relate problemas de Service Fabric do Azure no [GitHub Service Fabric](https://github.com/microsoft/service-fabric/issues). Este repositório destina-se a emitir relatórios e controlar problemas, bem como fazer pequenas solicitações de recursos relacionadas ao Service Fabric do Azure. Não **Use essa mídia para relatar problemas do site ativo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow e fóruns do MSDN

A [marca de Service Fabric em StackOverflow][stackoverflow] e o [Fórum de Service Fabric no MSDN][msdn-forum] são mais bem usadas para fazer perguntas gerais sobre como a plataforma funciona e como você pode usá-la para realizar determinadas tarefas.

### <a name="azure-feedback-forum"></a>Fórum de Comentários do Azure

O [Fórum de comentários do Azure para Service Fabric][uservoice-forum] é o melhor lugar para enviar ideias de recursos de produto significativas. Analisamos as solicitações mais populares e as preparamos para nosso planejamento de média a longo prazo. Incentivamos você a conseguir suporte para suas sugestões na comunidade.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões prévias do Service Fabric – sem suporte para uso em produção

Ocasionalmente, disponibilizamos versões de visualização especiais contendo alterações de recursos significativas para as quais gostaríamos de Pesquisar os comentários iniciais. Você só deve usar versões prévias em ambientes de teste isolados que não atendem a cargas de trabalho de produção. O cluster de produção sempre deve estar executando uma versão do Service Fabric estável e com suporte. Não oferecemos uma opção de suporte pago para essas versões de visualização.

Uma versão prévia sempre começa com um número de versão principal e secundária de 255. Por exemplo, se você vir uma Service Fabric versão 255.255.5703.949, essa versão está em visualização e destina-se apenas a ser usada em clusters de teste. Esses lançamentos de versões prévias também serão comunicados no [Blog da equipe do Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) e terão detalhes sobre os recursos incluídos. Use uma das opções listadas em [Relatar problemas do Azure Service Fabric](#report-azure-service-fabric-issues) para fazer perguntas ou fornecer comentários.

## <a name="next-steps"></a>Próximas etapas

[Versões do Service Fabric com suporte](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform