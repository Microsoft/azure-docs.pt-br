---
title: Solução de problemas-QnA Maker
description: A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612687"
---
# <a name="troubleshooting-for-qna-maker"></a>Solução de problemas para QnA Maker

A lista organizada das perguntas mais frequentes sobre o serviço de QnA Maker ajudará você a adotar o serviço mais rapidamente e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gerenciar previsões

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Como posso melhorar o desempenho da taxa de transferência para previsões de consulta?</b></summary>

**Resposta**: problemas de desempenho de taxa de transferência indicam que você precisa escalar verticalmente tanto para o serviço de aplicativo quanto para sua pesquisa cognitiva. Considere adicionar uma réplica à sua Pesquisa Cognitiva para melhorar o desempenho.

Saiba mais sobre os [tipos de preço](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Como obter o ponto de extremidade de serviço QnAMaker</b></summary>

**Resposta**: o ponto de extremidade de serviço QnAMaker é útil para fins de depuração quando você entra em contato com o suporte do QnAMaker ou UserVoice. O ponto de extremidade é uma URL neste formato: `https://your-resource-name.azurewebsites.net` .

1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o serviço de aplicativo associado ao recurso de QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o Serviço de Aplicativo do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A URL do ponto de extremidade está disponível na seção visão geral

    ![Ponto de extremidade QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

<details>
<summary><b>Como posso melhorar o desempenho da taxa de transferência para previsões de consulta?</b></summary>

**Resposta**: problemas de desempenho de taxa de transferência indicam que você precisa escalar verticalmente o pesquisa cognitiva. Considere adicionar uma réplica à sua Pesquisa Cognitiva para melhorar o desempenho.

Saiba mais sobre os [tipos de preço](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>Gerenciar a base de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Eu apaguei acidentalmente uma parte do meu QnA Maker, o que devo fazer?</b></summary>

**Resposta**: não exclua nenhum dos serviços do Azure criados junto com o recurso QnA Maker, como pesquisa ou aplicativo Web. Eles são necessários para que QnA Maker funcionem, se você excluir um, QnA Maker deixará de funcionar corretamente.

Todas as exclusões são permanentes, incluindo pares de perguntas e respostas, arquivos, URLs, perguntas e respostas personalizadas, bases de conhecimento ou recursos do Azure. Certifique-se de exportar sua base de conhecimento da página **Configurações** antes de excluir qualquer parte de sua base de conhecimento.

</details>

<details>
<summary><b>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?</b></summary>

**Resposta**: é possível que QnA Maker não possa extrair automaticamente um conteúdo de QnA (pergunta e resposta) de URLs de perguntas frequentes válidas. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar conteúdo de modo editorial à sua base de dados de conhecimento no [Portal do QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Qual o tamanho da base de dados de conhecimento que posso criar?</b></summary>

**Resposta**: o tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolher ao criar o serviço de QnA Maker. Leia [este texto](./concepts/azure-resources.md) para obter mais detalhes.

</details>

<details>
<summary><b>Por que eu não vejo nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?</b></summary>

**Resposta**: você ainda não criou nenhum serviço de QnA Maker no Azure. Leia [este artigo](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

</details>

<details>
<summary><b>Como posso compartilhar uma base de dados de conhecimento com outras pessoas?</b></summary>

**Resposta**: o compartilhamento funciona no nível de um serviço de QnA Maker, ou seja, todas as bases de conhecimento no serviço serão compartilhadas. Leia [este texto](./index.yml) sobre como colaborar em uma base de dados de conhecimento.

</details>

<details>
<summary><b>É possível compartilhar uma base de conhecimento com um colaborador que não está no mesmo locatário do AAD, para modificar uma base de Conhecimento?</b></summary>

**Resposta**: o compartilhamento é baseado no controle de acesso baseado em função do Azure (RBAC do Azure). Se pode compartilhar _qualquer_ recurso do Azure com outro usuário, você também pode compartilhar o QnA Maker.

</details>

<details>
<summary><b>Se você tiver um plano do serviço de aplicativo com 5 bases de dados de conhecimento QnAMaker. Você pode atribuir direitos de leitura/gravação a 5 usuários diferentes para que cada um deles possa acessar apenas uma base de dados de conhecimento QnAMaker?</b></summary>

**Resposta**: você pode compartilhar um serviço QnAMaker inteiro, não as bases de conhecimento individuais.

</details>

<details>
<summary><b>Como alterar a mensagem padrão quando nenhuma correspondência boa for encontrada?</b></summary>

**Resposta**: a mensagem padrão faz parte das configurações em seu serviço de aplicativo.
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

**Resposta**: consulte [locais de fonte de dados](./concepts/data-sources-and-content.md#data-source-locations) para obter mais informações.

</details>

<details>
<summary><b>As atualizações que fiz na base de dados de conhecimento não são refletidas na publicação. Por que não?</b></summary>

**Resposta**: cada operação de edição, seja em uma atualização de tabela, teste ou configuração, precisa ser salva antes de ser publicada. Certifique-se de clicar no botão **salvar e treinar** após cada operação de edição.

</details>

<details>
<summary><b>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?</b></summary>

**Resposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática de multimídia para arquivos e URLs

* URLS-recurso de conversão de HTML para redução limitada.
* Arquivos-sem suporte

#### <a name="answer-text-in-markdown"></a>Texto de resposta em redução
Depois que os pares de QnA estiverem na base de dados de conhecimento, você poderá editar o texto de redução de uma resposta para incluir links para mídia disponível de URLs públicas.


</details>

<details>
<summary><b>O QnA Maker dá suporte para idiomas além do inglês?</b></summary>

**Resposta**: Veja mais detalhes sobre os [idiomas com suporte](./overview/language-support.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

<details>
<summary><b>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?</b></summary>

**Resposta**: é possível que QnA Maker não possa extrair automaticamente um conteúdo de QnA (pergunta e resposta) de URLs de perguntas frequentes válidas. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar conteúdo de modo editorial à sua base de dados de conhecimento no [Portal do QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Qual o tamanho da base de dados de conhecimento que posso criar?</b></summary>

**Resposta**: o tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolher ao criar o serviço de QnA Maker. Leia [este texto](./concepts/azure-resources.md) para obter mais detalhes.

</details>

<details>
<summary><b>Por que eu não vejo nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?</b></summary>

**Resposta**: você ainda não criou nenhum serviço de QnA Maker no Azure. Leia [este artigo](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

</details>

<details>
<summary><b>Como posso compartilhar uma base de dados de conhecimento com outras pessoas?</b></summary>

**Resposta**: o compartilhamento funciona no nível de um serviço de QnA Maker, ou seja, todas as bases de conhecimento no serviço serão compartilhadas. Leia [este texto](./index.yml) sobre como colaborar em uma base de dados de conhecimento.

</details>

<details>
<summary><b>Você pode compartilhar uma base de dados de conhecimento com um colaborador que não está no mesmo locatário Azure Active Directory, para modificar uma base de dados de conhecimento?</b></summary>

**Resposta**: o compartilhamento é baseado no controle de acesso baseado em função do Azure (RBAC do Azure). Se pode compartilhar _qualquer_ recurso do Azure com outro usuário, você também pode compartilhar o QnA Maker.

</details>

<details>
<summary><b>É possível atribuir direitos de leitura/gravação para 5 usuários diferentes para que cada uma deles possa acessar apenas uma base de dados de conhecimento do QnAMaker?</b></summary>

**Resposta**: você pode compartilhar um serviço QnAMaker inteiro, não as bases de conhecimento individuais.

</details>

<details>
<summary><b>Por que meu link do SharePoint não está sendo extraído?</b></summary>

**Resposta**: consulte [locais de fonte de dados](./concepts/data-sources-and-content.md#data-source-locations) para obter mais informações.

</details>

<details>
<summary><b>As atualizações que fiz na base de dados de conhecimento não são refletidas na publicação. Por que não?</b></summary>

**Resposta**: cada operação de edição, seja em uma atualização de tabela, teste ou configuração, precisa ser salva antes de ser publicada. Certifique-se de clicar no botão **salvar e treinar** após cada operação de edição.

</details>

<details>
<summary><b>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?</b></summary>

**Resposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática de multimídia para arquivos e URLs

* URLS-recurso de conversão de HTML para redução limitada.
* Arquivos-sem suporte

#### <a name="answer-text-in-markdown"></a>Texto de resposta em redução
Depois que os pares de QnA estiverem na base de dados de conhecimento, você poderá editar o texto de redução de uma resposta para incluir links para mídia disponível de URLs públicas.


</details>

<details>
<summary><b>O QnA Maker dá suporte para idiomas além do inglês?</b></summary>

**Resposta**: Veja mais detalhes sobre os [idiomas com suporte](./overview/language-support.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

</details>

---

## <a name="manage-service"></a>Gerenciar serviço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Quando devo reiniciar o serviço de aplicativo?</b></summary>

**Resposta**: Atualize seu serviço de aplicativo quando o ícone de cuidado estiver próximo ao valor da versão da base de dados de conhecimento na tabela **chaves de ponto de extremidade** na [página](https://www.qnamaker.ai/UserSettings) **configurações do usuário** .

</details>

<details>
<summary><b>Excluí meu serviço de pesquisa existente. Como posso corrigir isso?</b></summary>

**Resposta**: se você excluir um índice de pesquisa cognitiva do Azure, a operação será final e o índice não poderá ser recuperado.

</details>

<details>
<summary><b>Excluí meu `testkb` índice no meu serviço de pesquisa. Como posso corrigir isso?</b></summary>

**Resposta**: os dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie sua base de dados de conhecimento novamente.

</details>

<details>
<summary><b>Quando devo atualizar minhas chaves de ponto de extremidade?</b></summary>

**Resposta**: Atualize suas chaves de ponto de extremidade se suspeitar que elas foram comprometidas.

</details>

<details>
<summary><b>Posso usar o mesmo recurso de Pesquisa Cognitiva do Azure para bases de dados de conhecimento usando vários idiomas?</b></summary>

**Resposta**: para usar vários idiomas e várias bases de dados de conhecimento, o usuário precisa criar um recurso de QnA Maker para cada idioma. Isso criará um serviço do Azure Search separado por idioma. Misturar bases de dados de conhecimento de idiomas diferentes em um único serviço de pesquisa do Azure resultará em resultados com relevância degradada.

</details>

<details>
<summary><b>Como posso alterar o nome do recurso de Pesquisa Cognitiva do Azure usado pelo QnA Maker?</b></summary>

**Resposta**: o nome do recurso de pesquisa cognitiva do Azure é o nome do recurso de QnA Maker com algumas letras aleatórias anexadas no final. Isso torna difícil distinguir entre vários recursos de pesquisa do QnA Maker. Crie um serviço de pesquisa separado (nomeando-o como você gostaria) e conecte-o ao serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Quando QnA Maker retorna `Runtime core is not initialized,` como corrigi-lo?</b></summary>

**Resposta**: o espaço em disco para o serviço de aplicativo pode estar cheio. Etapas para corrigir o espaço em disco:

1. Na [portal do Azure](https://portal.azure.com), selecione o serviço de aplicativo do QnA Maker e interrompa o serviço.
1. Enquanto ainda estiver no serviço de aplicativo, selecione **ferramentas de desenvolvimento**, depois **ferramentas avançadas** e, em seguida, **vá**. Isso abre uma nova janela do navegador.
1. Selecione **console de depuração** e, em seguida, **cmd** para abrir uma ferramenta de linha de comando.
1. Navegue até o diretório _site/wwwroot/data/QnAMaker/_ .
1. Remova todas as pastas cujo nome começa com `rd` .

    **Não exclua** o seguinte:

    * Arquivo de KbIdToRankerMappings.txt
    * EndpointSettings.jsno arquivo
    * Pasta EndpointKeys

1. Inicie o serviço de aplicativo.
1. Acesse sua base de dados de conhecimento para verificar se ela funciona agora.

</details>
<details>
<summary><b>Por que minha Application Insights não está funcionando?</b></summary>

**Resposta**: faça uma verificação cruzada e atualize as etapas abaixo para corrigir o problema:

1. No serviço de aplicativo-> grupo de configurações-> seção de configuração-> configurações do aplicativo-> nome "UserAppInsightsKey" parâmetros estão configurados corretamente e definidos para a respectiva Guia de visão geral do Application insights ("chave de instrumentação") GUID. 

1. No serviço de aplicativo-> grupo de configurações – > seção "Application Insights"-> Verifique se o app insights está habilitado e conectado ao respectivo recurso do Application insights.

</details>

<details>
<summary><b>O meu Application Insights está habilitado, mas por que ele não está funcionando corretamente?</b></summary>

**Resposta**: siga as etapas fornecidas abaixo: 

1.  Copie o valor de ' "APPINSIGHTS_INSTRUMENTATIONKEY" Name ' para o nome ' UserAppInsightsKey ' substituindo se já houver algum valor presente nele. 

1.  Se a chave ' UserAppInsightsKey ' não existir nas configurações do aplicativo, adicione uma nova chave com esse nome e copie o valor.

1.  Salve-o e isso reiniciará automaticamente o serviço de aplicativo. Isso deve resolver o problema. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)


<details>
<summary><b>Excluí meu serviço de pesquisa existente. Como posso corrigir isso?</b></summary>

**Resposta**: se você excluir um índice de pesquisa cognitiva do Azure, a operação será final e o índice não poderá ser recuperado.

</details>

<details>
<summary><b>Excluí meu `testkb` índice no meu serviço de pesquisa. Como posso corrigir isso?</b></summary>

**Resposta**: os dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie sua base de dados de conhecimento novamente.

</details>

<details>
<summary><b>Posso usar o mesmo recurso de Pesquisa Cognitiva do Azure para bases de dados de conhecimento usando vários idiomas?</b></summary>

**Resposta**: para usar vários idiomas e várias bases de dados de conhecimento, o usuário precisa criar um recurso de QnA Maker para cada idioma. Isso criará um serviço do Azure Search separado por idioma. Misturar bases de dados de conhecimento de idiomas diferentes em um único serviço de pesquisa do Azure resultará em resultados com relevância degradada.

</details>

<details>
<summary><b>Como posso alterar o nome do recurso de Pesquisa Cognitiva do Azure usado pelo QnA Maker?</b></summary>

**Resposta**: o nome do recurso de pesquisa cognitiva do Azure é o nome do recurso de QnA Maker com algumas letras aleatórias anexadas no final. Isso torna difícil distinguir entre vários recursos de pesquisa do QnA Maker. Crie um serviço de pesquisa separado (nomeando-o como você gostaria) e conecte-o ao serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integrar com outros serviços, incluindo Bots

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

<details>
<summary><b>Preciso usar o Bot Framework para usar o QnA Maker?</b></summary>

**Resposta**: não, você não precisa usar o [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com QnA Maker. No entanto, QnA Maker é oferecida como um dos vários modelos no [serviço de bot do Azure](/azure/bot-service/). O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft e é executado em um ambiente sem servidor.

</details>

<details>
<summary><b>Como posso criar um novo bot com QnA Maker?</b></summary>

**Resposta**: siga as instruções nesta [documentação para](./Quickstarts/create-publish-knowledge-base.md) criar o bot com o serviço de bot do Azure.

</details>

<details>
<summary><b>Como fazer usar uma base de dados de conhecimento diferente com um serviço de bot do Azure existente?</b></summary>

**Resposta**: você precisa ter as seguintes informações sobre sua base de dados de conhecimento:

* ID da base de dados de conhecimento.
* O nome de subdomínio personalizado do ponto de extremidade publicado da base de dados de conhecimento, conhecido como `host` , encontrado na página de **configurações** após a publicação.
* Chave de ponto de extremidade publicada da base de dados de conhecimento-encontrada na página de **configurações** após a publicação.

Com essas informações, vá para o serviço de aplicativo do bot no portal do Azure. Em **configurações-> configuração – > configurações do aplicativo**, altere esses valores.

A chave do ponto de extremidade da base de dados de conhecimento é rotulada `QnAAuthkey` no serviço ABS.

</details>

<details>
<summary><b>Dois ou mais aplicativos cliente podem compartilhar uma base de dados de conhecimento?</b></summary>

**Resposta**: Sim, a base de dados de conhecimento pode ser consultada de qualquer número de clientes. Se a resposta da base de dados de conhecimento parecer ser lenta ou atingir o tempo limite, considere atualizar a camada de serviço para o serviço de aplicativo associado à base de dados de conhecimento.

</details>

<details>
<summary><b>Como posso inserir o serviço QnA Maker no meu site?</b></summary>

**Resposta**: Siga estas etapas para inserir o serviço de QnA Maker como um controle de chat da Web em seu site:

1. Crie seu bot de perguntas frequentes seguindo as instruções [aqui](./Quickstarts/create-publish-knowledge-base.md).
2. Habilite o chat da Web executando [estas](/azure/bot-service/bot-service-channel-connect-webchat) etapas

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)


<details>
<summary><b>Preciso usar o Bot Framework para usar o QnA Maker?</b></summary>

**Resposta**: não, você não precisa usar o [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com QnA Maker. No entanto, QnA Maker é oferecida como um dos vários modelos no [serviço de bot do Azure](/azure/bot-service/). O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft e é executado em um ambiente sem servidor.

</details>

<details>
<summary><b>Como posso criar um novo bot com QnA Maker?</b></summary>

**Resposta**: siga as instruções nesta [documentação para](./Quickstarts/create-publish-knowledge-base.md) criar o bot com o serviço de bot do Azure.

</details>

<details>
<summary><b>Como fazer usar uma base de dados de conhecimento diferente com um serviço de bot do Azure existente?</b></summary>

**Resposta**: você precisa ter as seguintes informações sobre sua base de dados de conhecimento:

* ID da base de dados de conhecimento.
* O nome de subdomínio personalizado do ponto de extremidade publicado da base de dados de conhecimento, conhecido como `host` , encontrado na página de **configurações** após a publicação.
* Chave de ponto de extremidade publicada da base de dados de conhecimento-encontrada na página de **configurações** após a publicação.

Com essas informações, vá para o serviço de aplicativo do bot no portal do Azure. Em **configurações-> configuração – > configurações do aplicativo**, altere esses valores.

A chave do ponto de extremidade da base de dados de conhecimento é rotulada `QnAAuthkey` no serviço ABS.

</details>

<details>
<summary><b>Dois ou mais aplicativos cliente podem compartilhar uma base de dados de conhecimento?</b></summary>

**Resposta**: Sim, a base de dados de conhecimento pode ser consultada de qualquer número de clientes. Se a resposta da base de dados de conhecimento parecer ser lenta ou atingir o tempo limite, considere atualizar a camada de serviço para o serviço de aplicativo associado à base de dados de conhecimento.

</details>

<details>
<summary><b>Como posso inserir o serviço QnA Maker no meu site?</b></summary>

**Resposta**: Siga estas etapas para inserir o serviço de QnA Maker como um controle de chat da Web em seu site:

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
