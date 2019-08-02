---
title: Solução de problemas-QnA Maker
titleSuffix: Azure Cognitive Services
description: A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/31/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 85c6ff59b5f73e88c82ee8b788bd9f35d18bc0ed
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697961"
---
# <a name="troubleshooting-for-qna-maker"></a>Solução de problemas para QnA Maker

A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Usar o bot de ajuda no portal de QnA Maker

QnA Maker fornece um bot de **ajuda** dentro do portal de QnA Maker para ajudá-lo. O bot da ajuda está disponível em todas as páginas da Web. O bot usa QnA Maker para fornecer respostas e fornece o [ C# projeto de código do bot Framework](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) ao bot para que você possa entrar em funcionamento rapidamente com seu próprio bot de resposta. 

![! [QnA Maker fornece um bot * * Help * * no portal QnA Maker para ajudá-lo.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Gerenciar a base de conhecimento

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Eu apaguei acidentalmente uma parte do meu QnA Maker, o que devo fazer? 

Todas as exclusões são permanentes, incluindo pares de perguntas e respostas, arquivos, URLs, perguntas e respostas personalizadas, bases de conhecimento ou recursos do Azure. Certifique-se de exportar sua base de conhecimento da página **Configurações** antes de excluir qualquer parte de sua base de conhecimento. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?

É possível que o QnA Maker não possa extrair automaticamente algum conteúdo de QnA (perguntas e respostas) de URLs de perguntas frequentes válidas. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar conteúdo de modo editorial à sua base de dados de conhecimento no [Portal do QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Qual o tamanho da base de dados de conhecimento que posso criar?

O tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolhe ao criar o serviço QnA Maker. Leia [este texto](./Tutorials/choosing-capacity-qnamaker-deployment.md) para obter mais detalhes.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Por que eu não vejo nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?

Você ainda não criou serviços de QnA Maker no Azure. Leia [este artigo](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Como posso compartilhar uma base de dados de conhecimento com outras pessoas?

O compartilhamento funciona no nível do serviço QnA Maker, ou seja, todas as bases de dados de conhecimento nos serviços serão compartilhadas. Leia [este texto](./How-To/collaborate-knowledge-base.md) sobre como colaborar em uma base de dados de conhecimento.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>É possível compartilhar uma base de conhecimento com um colaborador que não está no mesmo locatário do AAD, para modificar uma base de Conhecimento? 

O compartilhamento é baseado no RBAC (controle de acesso baseado em função) do Azure. Se pode compartilhar _qualquer_ recurso do Azure com outro usuário, você também pode compartilhar o QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Digamos que você tenha um Plano do Serviço de Aplicativo com 5 bases de dados de conhecimento do QnAMaker. É possível atribuir direitos de leitura/gravação para 5 usuários diferentes para que cada uma deles possa acessar apenas uma base de dados de conhecimento do QnAMaker?

Você pode compartilhar todo o serviço do QnAMaker, não bases de dados de conhecimento individuais.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Como alterar a mensagem padrão quando nenhuma correspondência boa for encontrada?

A mensagem padrão faz parte das configurações em seu serviço de Aplicativo.
- Vá até o recurso do seu Serviço de Aplicativo no portal do Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **Configurações**

![qnamaker appservice settings](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da configuração **DefaultAnswer**
- Reinicie o serviço Aplicativo

![qnamaker appservice restart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Por que meu link do SharePoint não está sendo extraído?

Confira [Locais da fonte de dados](./Concepts/data-sources-supported.md#data-source-locations) para obter mais informações.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>As atualizações que eu fiz em minha base de dados de conhecimento não são refletidas na publicação. Por que não?

Todas as operações de edição, seja na atualização, teste ou configuração de uma tabela, precisam ser salvas antes de serem publicadas. Clique no botão **Salvar e treinar** após cada operação de edição.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?

A base de dados de conhecimento dá suporte ao Markdown. No entanto, a extração automática das URLs limitou a capacidade de conversão de HTML para Markdown. Se quiser usar o Markdown completo, você poderá modificar seu conteúdo diretamente na tabela ou fazer o upload de uma base de dados de conhecimento com o conteúdo avançado.

Não há suporte para multimídia, como imagens e vídeos, no momento.

### <a name="does-qna-maker-support-non-english-languages"></a>O QnA Maker dá suporte para idiomas além do inglês?

Veja mais detalhes sobre os [idiomas com suporte](./Overview/languages-supported.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

## <a name="manage-service"></a>Gerenciar serviço

### <a name="when-should-i-restart-my-app-service"></a>Quando devo reiniciar o serviço de aplicativo? 

Quando o ícone de cuidado está ao lado do valor para a base de dados de Conhecimento na versão de atualização do serviço de aplicativo a **chaves de ponto de extremidade** de tabela na **configurações do usuário** [página](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando devo atualizar minhas chaves de ponto de extremidade?

Atualize suas chaves de ponto de extremidade se suspeitar que elas foram comprometidas.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Posso usar o mesmo recurso do Azure Search para bases de dados de conhecimento que usam vários idiomas?

Para usar vários idiomas e várias bases de dados de conhecimento, o usuário precisa criar um recurso do QnA Maker para cada idioma. Isso criará um serviço do Azure Search separado por idioma. Misturar bases de dados de conhecimento de idiomas diferentes em um único serviço de pesquisa do Azure resultará em resultados com relevância degradada.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Como alterar o nome do recurso do Azure Search usado pelo QnA Maker?

O nome do recurso do Azure Search é o nome do recurso do QnA Maker com algumas letras aleatórias acrescentadas ao final. Isso torna difícil distinguir entre vários recursos de pesquisa do QnA Maker. Criar um serviço de Azure Search separado (nomeando-o a maneira que desejar) e conectá-lo ao seu QnA Service. As etapas são semelhantes às etapas que você precisa fazer para [upgrade de um Azure Search](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Quando QnA Maker retorna `Runtime core is not initialized,` como corrigi-lo?

O espaço em disco para o serviço de aplicativo pode estar cheio. Etapas para corrigir o espaço em disco:

1. Na [portal do Azure](https://portal.azure.com), selecione o serviço de aplicativo do QnA Maker e interrompa o serviço.
1. Enquanto ainda estiver no serviço de aplicativo, selecione **ferramentas de desenvolvimento**, depois **ferramentas avançadas**e, em seguida, **vá**. Isso abre uma nova janela do navegador.
1. Selecione **console de depuração**e, em seguida, **cmd** para abrir uma ferramenta de linha de comando. 
1. Navegue até o diretório _site/wwwroot/data/QnAMaker/_ .
1. Remova todas as pastas cujo nome começa com `rd`. 

    **Não exclua** o seguinte:

    * Arquivo KbIdToRankerMappings. txt
    * Arquivo EndpointSettings. JSON
    * Pasta EndpointKeys 

1. Inicie o serviço de aplicativo.
1. Acesse sua base de dados de conhecimento para verificar se ela funciona agora. 


## <a name="integrate-with-other-services-including-bots"></a>Integrar com outros serviços, incluindo Bots

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Preciso usar o Bot Framework para usar o QnA Maker?

Não, você não precisa usar o [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com QnA Maker. No entanto, QnA Maker é oferecida como um dos vários modelos no [serviço de bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft e é executado em um ambiente sem servidor.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Como criar um bot com QnA Maker?

Siga as instruções [nesta](./Tutorials/create-qna-bot.md) documentação para criar seu Bot com Serviço de Bot do Azure.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Como posso inserir o serviço QnA Maker no meu site?

Execute as etapas para inserir o serviço QnA Maker como um controle de webchat em seu site:

1. Crie seu bot de perguntas frequentes seguindo as instruções [aqui](./Tutorials/create-qna-bot.md).
2. Habilite o chat da Web executando [estas](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) etapas

## <a name="data-storage"></a>Armazenamento de dados

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Quais dados são armazenados e onde ele é armazenado? 

Quando você cria seu serviço QnA Maker, você selecionou uma região do Azure. Suas bases de conhecimento e arquivos de log são armazenados nessa região. 
