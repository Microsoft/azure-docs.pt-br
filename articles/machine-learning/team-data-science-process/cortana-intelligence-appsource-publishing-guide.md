---
title: "Guia de publicação do AppSource do Cortana Intelligence | Microsoft Docs"
description: "Como um Microsoft Partner, aqui estão todas as etapas necessárias a serem seguidas para publicar sua solução do Cortana Intelligence para AppSource."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: 9f867641b77c8148c1d6cbf2913da9c1f5ce5b71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Guia de publicação do AppSource do Cortana Intelligence

## <a name="overview"></a>Visão geral
O AppSource é o destino único para BDMs (Business Decision Makers) para descobrir e experimentar de forma transparente aplicativos/soluções de negócios criados pelos parceiros e avaliados pela Microsoft. Assista [este vídeo](https://youtu.be/hpq_Y9LuIB8) para saber como funciona o AppSource. 

Como um Microsoft Partner, realmente é possível obter benefícios da publicação no AppSource se:
- Desenvolver um aplicativo/solução inteligente utilizando o [Cortana Intelligence Suite](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable).
- Seu aplicativo ou sua solução aborda um problema de negócios específico.
- Desenvolver módulos ou propriedade intelectual que seus clientes possa reutilizar com relativa rapidez de maneira previsível.

Dê uma olhada nas [soluções Cortana Intelligence](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) que já estão publicadas no AppSource. 

Este artigo irá percorrer as etapas para obter a solução do Cortana Intelligence de um parceiro publicada na AppSource

## <a name="getting-started"></a>Introdução
1. No [Guia de Benefícios da Comunidade de Parceiros](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF), consulte a página 9 para ser listado como um parceiro de Análise Avançada.
1. Preencha o formulário [Enviar seu aplicativo](https://appsource.microsoft.com/en-us/partners/list-an-app).

    Para a pergunta *"Escolha os produtos para os quais seu aplicativo foi criado*", marque a caixa de seleção **Outros** e liste "Cortana Intelligence" no controle de edição.
1. Antes que um aplicativo do Cortana Intelligence possa ser integrado no AppSource, o aplicativo deverá ser certificado pela equipe de Tecnologia de Solução de Parceiro do Cortana Intelligence. Para que esse processo seja iniciado, compartilhe dos detalhes do seu aplicativo preenchendo o formulário de pesquisa em [Avaliação de soluções do Cortana Intelligence para publicação do AppSource](https://aka.ms/cisappsrceval). Esse site é protegido por senha e o site possui instruções sobre como obter a senha.

## <a name="solution-evaluation-criteria"></a>Critérios de avaliação da solução
Aqui está a lista de critérios que o aplicativo precisa atender
1. O aplicativo deve endereçar um problema de caso de uso comercial específico dentro de uma determinada área funcional de maneira repetida com configurações mínimas para proposições de valores predefinidas implementáveis dentro de um curto período de tempo.
1. A solução deve utulizar pelo menos um dos seguintes componentes:

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Serviços Cognitivos
    - Ambas as estruturas
    - Serviços de análise
    - Microsoft R Server autônomo
    - Serviços do R em SQL 2016 ou HDInsight Premium
1. A solução deve gerar pelo menos US$ 1000 por mês por cliente usando DPOR/CSP.
1. A solução deve usar logon único federado do Azure Active Directory (SSO federado AAD) com permissão habilitada para autenticação de usuários e controles de acesso a recursos. Será necessário mostrar à equipe de avaliação que sua solução é SSO federado AAD habilitado, antes que seu aplicativo possa ser integrado ao AppSource.

     Para ver o que significa ser SSO federado AAD habilitado, procure posicionar 02:35 em [A experiência de avaliação do AppSource passa pelo](https://aka.ms/trialexperienceforwebapps) vídeo. Se o seu aplicativo ainda não estiver habilitado com SSO federado AAD, segue aqui alguma documentação sobre isso
   1. [Entrar com um clique](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Integrar aplicativos com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application).
     
1. Utilize o Power BI na sua solução: opcional, mas altamente recomendado, uma vez que comprovadamente irá gerar maior número de clientes potenciais.

## <a name="devcenter-account-setup"></a>Configuração da conta do DevCenter
Este é o processo de registro de sua empresa para tornar-se um editor com a Microsoft. Se você já é um editor registrado com uma conta do DevCenter existente, compartilhe o ID do email associado à sua conta do DevCenter. Depois que a solicitação for aprovada para publicação, você obterá acesso à documentação completa sobre o [perfil do editor de Gerenciamento do Portal do Cloud](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)

Se você não tiver uma conta, a seguir são apresentadas as principais etapas para configurar uma conta do DevCenter.
1. Crie uma conta da Microsoft [aqui](https://signup.live.com/signup.aspx).
1. Vá para a [página de registro](http://go.microsoft.com/fwlink/?LinkId=615100) do Microsoft DevCenter e clique em "criar conta".
1. Quando solicitado, utilize o código fornecido. Se você não possuir um código, contate [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Selecione o [país/região](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) de sua residência ou onde sua empresa está localizada. **Essas informações não poderão ser alteradas posteriormente.**
1. Selecione o [tipo de conta de desenvolvedor](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) Para AppSource, selecione **Empresa**. Para uma conta de empresa, faça a revisão dessas [diretrizes](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Digite as informações de contato que deseja usar para sua conta de desenvolvedor.
Para obter instruções passo a passo detalhadas sobre como configurar a conta do DevCenter, consulte as instruções [aqui](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Forneça informações aos vendedores da Microsoft
Uma das principais proposições de valor do AppSource para parceiros é poder colaborar com os Vendedores da Microsoft no posicionamento de aplicativos de parceiros ao alcance de potenciais clientes.

Preencha as [Informações da Solução de Parceiro para Vendedores da Microsoft](https://aka.ms/aapartnerappinfo) e envie-o para [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Essa é uma etapa necessária para que um aplicativo do Cortana Intelligence seja aprovado para publicação.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Desenvolva um passo a passo atraente do cliente no AppSource
Primeiro, consulte a [Otimização de Inventário do Neal Analytics](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) no AppSource. Cada entrada de aplicativo no AppSource possui título, resumo (máximo de 100 caracteres), descrição (máximo de 1300 caracteres), imagens, vídeos (opcional), documentos em pdf, além do ponto de entrada para uma experiência de avaliação. Os parceiros devem aproveitar tudo isso para criar uma experiência atraente do cliente.

Os parceiros devem pensar sobre o conteúdo que incluíram no AppSource como uma orquestração de vendas de ponta a ponta. Os clientes leem o título e a descrição para compreender a proposição de valor e, em seguida, passam por imagens e vídeos para entender o que é a solução. Estudos de caso ajudam clientes potenciais a verem como os clientes existentes estão agregando valor. 

Tudo isso deve fazer com que o cliente sinta-se interessado e queira saber mais. Pense nisso como uma apresentação baseada em plataformas de lançamento, em que um competente profissional de vendas técnicas acompanharia os novos clientes. O formato sugerido da descrição é dividir o texto em sub-seções com base em proposições de valor, cada um destacado com um subtítulo. 

Geralmente, os visitantes verificam rapidamente o campo "Resumo da oferta" e os subtítulos para obter o conteúdo de endereço do aplicativo e o porquê eles devem considerar o aplicativo apenas diante de um rápido olhar. Portanto, é importante chamar a atenção do usuário e apresentar-lhe uma razão para ler e obter as especificidades.

Veja o que esses parceiros fizeram.
- [Otimização de Inventário do Neal Analytics](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Personalização de Varejo Plexure](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [Serviços ao Cidadão AvePoint](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

O ato final de vendas é mostrar uma demonstração do aplicativo/solução que apresente como a proposta de valor é entregue. É exatamente isso que espera-se de uma experiência de avaliação do cliente no AppSource. Uma boa demonstração irá fazer o seguinte:
- Resumir brevemente a proposição de valor do aplicativo e listar as pessoas dentro da empresa do cliente que podem interagir com a solução
- Contar uma história e definir o contexto sobre um cliente de exemplo lidando com problemas específicos
- Explique como a situação geralmente pode ser devolvida e impactar o cliente (antes) vs o quê aconteceria se a solução estiver em uso. Isso poderá ser mostrado utilizando painéis de controle do PowerBI, etc.
- Resumir como a solução é realizada utilizando qualquer algoritmo de Machine Learning específico, etc.

O conteúdo que está sendo adicionado no AppSource deve ser de alta qualidade e suficientemente reunido para habilitar o seguinte:
- Uma pessoa de vendas técnicas do parceiro deve utilizá-lo para sua orquestração de vendas. As equipes de vendas utilizando esses recursos significa o sinal positivo de que você pode esperar que as pessoas de vendas da MS também podem fazer o mesmo. Isso permitirá que o ponto de contato do cliente seja capaz de retransmitir consistentemente a mesma história a seus colegas de equipe e superiores para obter o orçamento e as aprovações, antes que um acordo de compra possa ser feito.
- Um cliente que visita o site de forma orgânica pode passar pelo conteúdo por si só e se sentir animado para responder de volta à comunicação do parceiro para avançar com os próximos passos.

É por isso que os parceiros devem pensar no conteúdo que colocam no AppSource como uma orquestração de vendas de ponta a ponta. Com base na linha da história e nos recursos a serem exibidos na experiência de avaliação, o tipo de oferta pode ser decidido.

## <a name="publish-your-app-on-the-publishing-portal"></a>Publicar seu aplicativo no portal de publicação
Nesse caso, avaliamos as etapas acima para sua aplicação, você terá acesso ao portal de publicação e poderá consultar [Como publicar uma oferta do Cortana Intelligence via Portal do Cloud Partner](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) para obter instruções detalhadas sobre as próximas etapas.

Se você precisar de informações sobre qualquer um dos campos, envie um email <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Meu aplicativo é publicado no AppSource - e agora?
Em primeiro lugar, parabéns por ter publicado o seu aplicativo.
O nível de retornos que você obtém ao publicar seu aplicativo no AppSource depende muito de como você influencia o público-alvo. Consulte [Hacking de crescimento de seu aplicativo Cortana Intelligence no AppSource ](http://aka.ms/aagrowthhackguide) para obter mais detalhes sobre como é possível maximizar os retornos.

Se você tiver quaisquer dúvidas ou sugestões, contate-nos em <appsourcecissupport@microsoft.com>.

