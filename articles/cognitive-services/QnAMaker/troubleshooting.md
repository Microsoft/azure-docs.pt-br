---
title: Solução de problemas – QnA Maker
description: A lista das perguntas mais frequentes sobre o serviço do QnA Maker ajudará você a adotar o serviço com mais rapidez e com melhores resultados.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612687"
---
# <a name="troubleshooting-for-qna-maker"></a>Solução de problemas do QnA Maker

A lista das perguntas mais frequentes sobre o serviço do QnA Maker ajudará você a adotar o serviço com mais rapidez e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gerenciar previsões

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Como posso melhorar o desempenho da taxa de transferência para previsões de consulta?</b></summary>

**Resposta**: problemas de desempenho de taxa de transferência indicam que você precisa escalar verticalmente tanto o serviço de aplicativo quanto o Cognitive Search. Considere adicionar uma réplica ao Cognitive Search para aprimorar o desempenho.

Saiba mais sobre os [tipos de preço](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Como obter o ponto de extremidade de serviço do QnAMaker</b></summary>

**Resposta**: o ponto de extremidade de serviço do QnAMaker será útil para finalidades de depuração quando você contatar o Suporte do QnAMaker ou UserVoice. O ponto de extremidade é uma URL neste formato: `https://your-resource-name.azurewebsites.net`.

1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o Serviço de Aplicativo associado ao recurso do QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o Serviço de Aplicativo do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A URL do ponto de extremidade está disponível na seção Visão Geral

    ![Ponto de extremidade do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

<details>
<summary><b>Como posso melhorar o desempenho da taxa de transferência para previsões de consulta?</b></summary>

**Resposta**: problemas de desempenho de taxa de transferência indicam que você precisa escalar verticalmente o Cognitive Search. Considere adicionar uma réplica ao Cognitive Search para aprimorar o desempenho.

Saiba mais sobre os [tipos de preço](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>Gerenciar a base de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Eu apaguei acidentalmente uma parte do meu QnA Maker, o que devo fazer?</b></summary>

**Resposta**: não exclua nenhum dos serviços do Azure criados com o recurso do QnA Maker, como pesquisa ou aplicativo Web. Eles são necessários para o funcionamento do QnA Maker. Se você excluir um deles, o QnA Maker deixará de funcionar corretamente.

Todas as exclusões são permanentes, incluindo pares de perguntas e respostas, arquivos, URLs, perguntas e respostas personalizadas, bases de conhecimento ou recursos do Azure. Certifique-se de exportar sua base de conhecimento da página **Configurações** antes de excluir qualquer parte de sua base de conhecimento.

</details>

<details>
<summary><b>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?</b></summary>

**Resposta**: é possível que o QnA Maker não possa extrair automaticamente algum conteúdo de QnA (perguntas e respostas) de URLs válidas de perguntas frequentes. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar conteúdo de modo editorial à sua base de dados de conhecimento no [Portal do QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Qual o tamanho da base de dados de conhecimento que posso criar?</b></summary>

**Resposta**: o tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolhe ao criar o serviço QnA Maker. Leia [este texto](./concepts/azure-resources.md) para obter mais detalhes.

</details>

<details>
<summary><b>Por que eu não vejo nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?</b></summary>

**Resposta**: você ainda não criou serviços do QnA Maker no Azure. Leia [este artigo](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

</details>

<details>
<summary><b>Como posso compartilhar uma base de dados de conhecimento com outras pessoas?</b></summary>

**Resposta**: o compartilhamento funciona no nível do serviço QnA Maker, ou seja, todas as bases de dados de conhecimento nos serviços serão compartilhadas. Leia [este texto](./index.yml) sobre como colaborar em uma base de dados de conhecimento.

</details>

<details>
<summary><b>É possível compartilhar uma base de conhecimento com um colaborador que não está no mesmo locatário do AAD, para modificar uma base de Conhecimento?</b></summary>

**Resposta**: o compartilhamento é baseado no RBAC (controle de acesso baseado em função) do Azure. Se pode compartilhar _qualquer_ recurso do Azure com outro usuário, você também pode compartilhar o QnA Maker.

</details>

<details>
<summary><b>Se você tiver um plano do Serviço de Aplicativo com cinco bases de dados de conhecimento do QnAMaker, poderá atribuir direitos de leitura/gravação a cinco usuários diferentes para que cada um deles possa acessar apenas uma base de dados de conhecimento do QnAMaker?</b></summary>

**Resposta**: você pode compartilhar todo o serviço do QnAMaker, não bases de dados de conhecimento individuais.

</details>

<details>
<summary><b>Como alterar a mensagem padrão quando nenhuma correspondência boa for encontrada?</b></summary>

**Resposta**: a mensagem padrão faz parte das configurações no Serviço de Aplicativo.
- Vá até o recurso do seu Serviço de Aplicativo no portal do Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **Configurações**

![qnamaker appservice settings](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da configuração **DefaultAnswer**
- Reinicie o serviço Aplicativo

![qnamaker appservice restart](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Por que meu link do SharePoint não está sendo extraído?</b></summary>

**Resposta**: confira mais informações em [Locais da fonte de dados](./concepts/data-sources-and-content.md#data-source-locations).

</details>

<details>
<summary><b>As atualizações que eu fiz em minha base de dados de conhecimento não são refletidas na publicação. Por que não?</b></summary>

**Resposta**: para publicar uma operação de edição, seja em uma atualização de tabela, teste ou configuração, é preciso antes salvá-la. Clique no botão **Salvar e treinar** após cada operação de edição.

</details>

<details>
<summary><b>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?</b></summary>

**Resposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática de multimídia para arquivos e URLs

* URLS – funcionalidade limitada de conversão de HTML para Markdown.
* Arquivos – sem suporte

#### <a name="answer-text-in-markdown"></a>Texto de resposta em Markdown
Quando os pares de QnA estão na base de dados de conhecimento, você pode editar o texto de uma resposta em Markdown para incluir links para mídia disponível em URLs públicas.


</details>

<details>
<summary><b>O QnA Maker dá suporte para idiomas além do inglês?</b></summary>

**Resposta**: veja mais detalhes sobre os [idiomas com suporte](./overview/language-support.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

<details>
<summary><b>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?</b></summary>

**Resposta**: é possível que o QnA Maker não possa extrair automaticamente algum conteúdo de QnA (perguntas e respostas) de URLs válidas de perguntas frequentes. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar conteúdo de modo editorial à sua base de dados de conhecimento no [Portal do QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Qual o tamanho da base de dados de conhecimento que posso criar?</b></summary>

**Resposta**: o tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolhe ao criar o serviço QnA Maker. Leia [este texto](./concepts/azure-resources.md) para obter mais detalhes.

</details>

<details>
<summary><b>Por que eu não vejo nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?</b></summary>

**Resposta**: você ainda não criou serviços do QnA Maker no Azure. Leia [este artigo](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

</details>

<details>
<summary><b>Como posso compartilhar uma base de dados de conhecimento com outras pessoas?</b></summary>

**Resposta**: o compartilhamento funciona no nível do serviço QnA Maker, ou seja, todas as bases de dados de conhecimento nos serviços serão compartilhadas. Leia [este texto](./index.yml) sobre como colaborar em uma base de dados de conhecimento.

</details>

<details>
<summary><b>É possível compartilhar uma base de conhecimento com um colaborador que não está no mesmo locatário do Azure Active Directory, para modificar uma base de conhecimento?</b></summary>

**Resposta**: o compartilhamento é baseado no RBAC (controle de acesso baseado em função) do Azure. Se pode compartilhar _qualquer_ recurso do Azure com outro usuário, você também pode compartilhar o QnA Maker.

</details>

<details>
<summary><b>É possível atribuir direitos de leitura/gravação para 5 usuários diferentes para que cada uma deles possa acessar apenas uma base de dados de conhecimento do QnAMaker?</b></summary>

**Resposta**: você pode compartilhar todo o serviço do QnAMaker, não bases de dados de conhecimento individuais.

</details>

<details>
<summary><b>Por que meu link do SharePoint não está sendo extraído?</b></summary>

**Resposta**: confira mais informações em [Locais da fonte de dados](./concepts/data-sources-and-content.md#data-source-locations).

</details>

<details>
<summary><b>As atualizações que eu fiz em minha base de dados de conhecimento não são refletidas na publicação. Por que não?</b></summary>

**Resposta**: para publicar uma operação de edição, seja em uma atualização de tabela, teste ou configuração, é preciso antes salvá-la. Clique no botão **Salvar e treinar** após cada operação de edição.

</details>

<details>
<summary><b>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?</b></summary>

**Resposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática de multimídia para arquivos e URLs

* URLS – funcionalidade limitada de conversão de HTML para Markdown.
* Arquivos – sem suporte

#### <a name="answer-text-in-markdown"></a>Texto de resposta em Markdown
Quando os pares de QnA estão na base de dados de conhecimento, você pode editar o texto de uma resposta em Markdown para incluir links para mídia disponível em URLs públicas.


</details>

<details>
<summary><b>O QnA Maker dá suporte para idiomas além do inglês?</b></summary>

**Resposta**: veja mais detalhes sobre os [idiomas com suporte](./overview/language-support.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

</details>

---

## <a name="manage-service"></a>Gerenciar serviço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Quando devo reiniciar o serviço de aplicativo?</b></summary>

**Resposta**: atualize o serviço de aplicativo quando o ícone de cuidado estiver ao lado do valor da versão da base de dados de conhecimento na tabela **Chaves de ponto de extremidade** da [página](https://www.qnamaker.ai/UserSettings) **Configurações do Usuário**.

</details>

<details>
<summary><b>Excluí meu serviço Pesquisa. Como posso corrigir isso?</b></summary>

**Resposta**: se você excluir um índice do Azure Cognitive Search, a operação será final e o índice não poderá ser recuperado.

</details>

<details>
<summary><b>Excluí o índice `testkb` do meu serviço Pesquisa. Como posso corrigir isso?</b></summary>

**Resposta**: os dados antigos não podem ser recuperados. Crie um recurso do QnA Maker e crie a base de dados de conhecimento novamente.

</details>

<details>
<summary><b>Quando devo atualizar minhas chaves de ponto de extremidade?</b></summary>

**Resposta**: atualize suas chaves de ponto de extremidade se suspeitar que elas foram comprometidas.

</details>

<details>
<summary><b>Posso usar o mesmo recurso do Azure Cognitive Search para bases de dados de conhecimento que usam vários idiomas?</b></summary>

**Resposta**: para usar vários idiomas e várias bases de dados de conhecimento, o usuário precisa criar um recurso do QnA Maker para cada idioma. Isso criará serviços separados do Azure Search para cada idioma. Misturar bases de dados de conhecimento de idiomas diferentes em um único serviço de pesquisa do Azure resultará em resultados com relevância degradada.

</details>

<details>
<summary><b>Como alterar o nome do recurso do Azure Cognitive Search usado pelo QnA Maker?</b></summary>

**Resposta**: o nome do recurso do Azure Cognitive Search é o nome do recurso do QnA Maker com algumas letras aleatórias acrescentadas ao final. Isso torna difícil distinguir entre vários recursos de pesquisa do QnA Maker. Crie outro serviço de pesquisa separado (com o nome que você deseja) e conecte-o ao serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Quando QnA Maker retorna `Runtime core is not initialized,`, como corrigir isso?</b></summary>

**Resposta**: talvez o espaço em disco do serviço de aplicativo esteja cheio. Etapas para corrigir o espaço em disco:

1. No [portal do Azure](https://portal.azure.com), selecione o serviço de aplicativo do QnA Maker e interrompa o serviço.
1. Ainda no serviço de aplicativo, selecione **Ferramentas de Desenvolvimento**, depois **Ferramentas Avançadas** e **Ir**. Uma nova janela do navegador é aberta.
1. Selecione **Console de depuração** e, depois, **cmd** para abrir uma ferramenta de linha de comando.
1. Acesse o diretório _site/wwwroot/Data/QnAMaker/_ .
1. Remova todas as pastas cujo nome começa com `rd`.

    **Não exclua** estes itens:

    * Arquivo KbIdToRankerMappings.txt
    * Arquivo EndpointSettings.json
    * Pasta EndpointKeys

1. Inicie o Serviço de Aplicativo.
1. Acesse a base de dados de conhecimento para confirmar que ela funciona agora.

</details>
<details>
<summary><b>Por que meu Application Insights não está funcionando?</b></summary>

**Resposta**: verifique e atualize as etapas abaixo para corrigir o problema:

1. Em Serviço de Aplicativo -> grupo Configurações -> seção Configuração -> Configurações do Aplicativo –os parâmetros de nome "UserAppInsightsKey" devem estar devidamente configurados e definidos como a GUID da guia Visão geral ("Chave de Instrumentação") do respectivo Application Insights. 

1. Em Serviço de Aplicativo -> grupo Configurações -> seção "Application Insights" -> Application Insights precisa estar habilitado e conectado ao respectivo recurso.

</details>

<details>
<summary><b>Meu Application Insights está habilitado. Por que ele não está funcionando corretamente?</b></summary>

**Resposta**: siga as etapas abaixo: 

1.  Copie o valor do nome "APPINSIGHTS_INSTRUMENTATIONKEY" para o nome "UserAppInsightsKey", substituindo se já houver algum valor presente. 

1.  Se a chave "UserAppInsightsKey" não existir nas configurações do aplicativo, adicione uma nova chave com esse nome e copie o valor.

1.  Salve a chave, e o serviço de aplicativo será reiniciado automaticamente. Isso deve resolver o problema. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)


<details>
<summary><b>Excluí meu serviço Pesquisa. Como posso corrigir isso?</b></summary>

**Resposta**: se você excluir um índice do Azure Cognitive Search, a operação será final e o índice não poderá ser recuperado.

</details>

<details>
<summary><b>Excluí o índice `testkb` do meu serviço Pesquisa. Como posso corrigir isso?</b></summary>

**Resposta**: os dados antigos não podem ser recuperados. Crie um recurso do QnA Maker e crie a base de dados de conhecimento novamente.

</details>

<details>
<summary><b>Posso usar o mesmo recurso do Azure Cognitive Search para bases de dados de conhecimento que usam vários idiomas?</b></summary>

**Resposta**: para usar vários idiomas e várias bases de dados de conhecimento, o usuário precisa criar um recurso do QnA Maker para cada idioma. Isso criará serviços separados do Azure Search para cada idioma. Misturar bases de dados de conhecimento de idiomas diferentes em um único serviço de pesquisa do Azure resultará em resultados com relevância degradada.

</details>

<details>
<summary><b>Como alterar o nome do recurso do Azure Cognitive Search usado pelo QnA Maker?</b></summary>

**Resposta**: o nome do recurso do Azure Cognitive Search é o nome do recurso do QnA Maker com algumas letras aleatórias acrescentadas ao final. Isso torna difícil distinguir entre vários recursos de pesquisa do QnA Maker. Crie outro serviço de pesquisa separado (com o nome que você deseja) e conecte-o ao serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integrar com outros serviços, incluindo Bots

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Preciso usar o Bot Framework para usar o QnA Maker?</b></summary>

**Resposta**: não, você não precisa usar o [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com o QnA Maker. No entanto, o QnA Maker é oferecido como um de vários modelos no [Serviço de Bot do Azure](/azure/bot-service/). O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft e é executado em um ambiente sem servidor.

</details>

<details>
<summary><b>Como criar um bot com o QnA Maker?</b></summary>

**Resposta**: siga as instruções [nesta documentação](./Quickstarts/create-publish-knowledge-base.md) para criar um Bot com o Serviço de Bot do Azure.

</details>

<details>
<summary><b>Como usar outra base de dados de conhecimento com um serviço de bot existente do Azure?</b></summary>

**Resposta**: você precisa ter as seguintes informações sobre a base de dados de conhecimento:

* ID da base de dados de conhecimento.
* O nome de subdomínio personalizado do ponto de extremidade publicado da base de dados de conhecimento, chamado de `host`, que é exibido na página **Configurações** após a publicação.
* A chave de ponto de extremidade publicada da base de dados de conhecimento, que é exibida na página **Configurações** após a publicação.

Com essas informações, acesse o serviço de aplicativo do bot no portal do Azure. Em **Configurações -> Configuração -> Configurações de aplicativo**, altere esses valores.

A chave de ponto de extremidade da base de dados de conhecimento é rotulada `QnAAuthkey` no serviço ABS.

</details>

<details>
<summary><b>Dois ou mais aplicativos clientes podem compartilhar uma base de dados de conhecimento?</b></summary>

**Resposta**: sim, qualquer número de clientes pode consultar a base de dados de conhecimento. Se a resposta da base de dados de conhecimento estiver lenta ou atingir o tempo limite, considere alterar a camada de serviço de aplicativo associado à base de dados de conhecimento.

</details>

<details>
<summary><b>Como posso inserir o serviço QnA Maker no meu site?</b></summary>

**Resposta**: execute as etapas para inserir o serviço QnA Maker como um controle de webchat em seu site:

1. Crie seu bot de perguntas frequentes seguindo as instruções [aqui](./Quickstarts/create-publish-knowledge-base.md).
2. Habilite o chat da Web executando [estas](/azure/bot-service/bot-service-channel-connect-webchat) etapas

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)


<details>
<summary><b>Preciso usar o Bot Framework para usar o QnA Maker?</b></summary>

**Resposta**: não, você não precisa usar o [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com o QnA Maker. No entanto, o QnA Maker é oferecido como um de vários modelos no [Serviço de Bot do Azure](/azure/bot-service/). O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft e é executado em um ambiente sem servidor.

</details>

<details>
<summary><b>Como criar um bot com o QnA Maker?</b></summary>

**Resposta**: siga as instruções [nesta documentação](./Quickstarts/create-publish-knowledge-base.md) para criar um Bot com o Serviço de Bot do Azure.

</details>

<details>
<summary><b>Como usar outra base de dados de conhecimento com um serviço de bot existente do Azure?</b></summary>

**Resposta**: você precisa ter as seguintes informações sobre a base de dados de conhecimento:

* ID da base de dados de conhecimento.
* O nome de subdomínio personalizado do ponto de extremidade publicado da base de dados de conhecimento, chamado de `host`, que é exibido na página **Configurações** após a publicação.
* A chave de ponto de extremidade publicada da base de dados de conhecimento, que é exibida na página **Configurações** após a publicação.

Com essas informações, acesse o serviço de aplicativo do bot no portal do Azure. Em **Configurações -> Configuração -> Configurações de aplicativo**, altere esses valores.

A chave de ponto de extremidade da base de dados de conhecimento é rotulada `QnAAuthkey` no serviço ABS.

</details>

<details>
<summary><b>Dois ou mais aplicativos clientes podem compartilhar uma base de dados de conhecimento?</b></summary>

**Resposta**: sim, qualquer número de clientes pode consultar a base de dados de conhecimento. Se a resposta da base de dados de conhecimento estiver lenta ou atingir o tempo limite, considere alterar a camada de serviço de aplicativo associado à base de dados de conhecimento.

</details>

<details>
<summary><b>Como posso inserir o serviço QnA Maker no meu site?</b></summary>

**Resposta**: execute as etapas para inserir o serviço QnA Maker como um controle de webchat em seu site:

1. Crie seu bot de perguntas frequentes seguindo as instruções [aqui](./Quickstarts/create-publish-knowledge-base.md).
2. Habilite o chat da Web executando [estas](/azure/bot-service/bot-service-channel-connect-webchat) etapas

---

## <a name="data-storage"></a>Armazenamento de dados

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Quais dados são armazenados e onde ele é armazenado?</b></summary>

**Resposta**:

Quando você cria seu serviço QnA Maker, você selecionou uma região do Azure. Suas bases de conhecimento e arquivos de log são armazenados nessa região.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

<details>
<summary><b>Quais dados são armazenados e onde ele é armazenado?</b></summary>

**Resposta**:

Quando você cria seu serviço QnA Maker, você selecionou uma região do Azure. Suas bases de conhecimento e arquivos de log são armazenados nessa região.

</details>

---
