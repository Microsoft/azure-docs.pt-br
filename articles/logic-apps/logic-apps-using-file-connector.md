---
title: "Conectar a sistemas de arquivos locais – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Conectar a sistemas de arquivos locais nos fluxos de trabalho dos aplicativos lógicos por meio do gateway de dados local e do conector do Sistema de Arquivos"
keywords: sistemas de arquivos, local
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 7738b3346af49cb8aa811eb17003d1b72b1bbe46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Conectar a sistemas de arquivos locais em aplicativos lógicos com o conector do Sistema de Arquivos

Para gerenciar dados e acessar os recursos locais com segurança, os aplicativos lógicos podem usar o gateway de dados local. Este artigo demonstra como se conectar a um sistema de arquivos local em um cenário simples: copiar um arquivo que é carregado no Dropbox para um compartilhamento de arquivos e depois enviar um email.

## <a name="prerequisites"></a>Pré-requisitos

* Baixe a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127).

* Instale e configure o gateway de dados local mais recente, versão 1.15.6150.1 ou superior. Para obter as etapas, consulte [Conectar a fontes de dados locais](http://aka.ms/logicapps-gateway). Você deve instalar o gateway em um computador local antes de continuar com essas etapas.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Adicionar um gatilho e ações para conectar ao sistema de arquivos

1. Criar um aplicativo lógico em branco. Adicione esse gatilho como a primeira etapa: **Dropbox – quando um arquivo é criado** 

2. No gatilho, escolha **Próxima Etapa** > **Adicionar uma ação**. 

3. Na caixa de pesquisa, insira "sistema de arquivos" como filtro. Quando você vir todas as ações para o conector do Sistema de arquivos, escolha a ação **Sistema de Arquivos – Criar arquivo**. 

   ![Procurar conector de arquivo](media/logic-apps-using-file-connector/search-file-connector.png)

4. Se você ainda não tiver uma conexão com seu sistema de arquivos, receberá uma solicitação para criar essa conexão. 

5. Selecione **Conectar por meio do gateway de dados local**. Quando as propriedades de conexão aparecerem, configure sua conexão como especificado na tabela.

   ![Configurar conexão](media/logic-apps-using-file-connector/create-file.png)

   | Configuração | Descrição |
   | ------- | ----------- |
   | **Pasta raiz** | Especifique a pasta raiz do sistema de arquivos. É possível especificar uma pasta local no computador em que o gateway de dados local está instalado ou a pasta pode ser um compartilhamento de rede que pode ser acessado pelo computador. <p>**Dica:** a pasta raiz é a pasta pai principal, que é usada para caminhos relativos de todas as ações relacionadas ao arquivo. | 
   | **Tipo de autenticação** | O tipo de autenticação usado pelo seu sistema de arquivos | 
   | **Nome de Usuário** | Forneça seu nome de usuário {*domínio*\\*nome de usuário*} para o gateway instalado anteriormente. | 
   | **Senha** | Forneça sua senha para o gateway instalado anteriormente. | 
   | **Gateway** | Selecione o gateway instalado anteriormente. | 
   ||| 

6. Depois de fornecer todos os detalhes da conexão, escolha **Criar**. 

   Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto. 
   Se a conexão estiver configurada de forma correta, você verá opções para a ação selecionada anteriormente. 
   O conector do sistema de arquivos está pronto para uso.

7. Configure a ação **Criar arquivo** para copiar os arquivos do Dropbox para a pasta raiz do compartilhamento de arquivos local.

   ![Ação Criar arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

8. Depois dessa ação de copiar o arquivo, adicione uma ação do Outlook que envia um email para que os usuários relevantes fiquem cientes do novo arquivo. Insira os destinatários, o título e o corpo do email. 

   Na lista **Conteúdo dinâmico**, é possível escolher saídas de dados do conector de arquivo, de forma que você possa adicionar mais detalhes ao email.

   ![Ação Enviar email](media/logic-apps-using-file-connector/send-email.png)

9. Salve seu aplicativo lógico. Teste o aplicativo carregando um arquivo no Dropbox. O arquivo deve ser copiado para o compartilhamento de arquivos local e você deverá receber um email sobre a operação.

Parabéns! Agora você tem um aplicativo lógico em operação que pode se conectar ao sistema de arquivos local. 

Tente explorar outras funcionalidades oferecidas pelo conector, por exemplo:

- Criar arquivo
- Lista de arquivos na pasta
- Acrescentar arquivo
- Excluir arquivo
- Obter conteúdo do arquivo
- Obter o conteúdo do arquivo usando o caminho
- Obter Metadados do Arquivo
- Obter Metadados do Arquivo usando o caminho
- Lista de arquivos na pasta-raiz
- Atualizar arquivo

## <a name="view-the-swagger"></a>Exibir o Swagger

Consulte os [detalhes do Swagger](/connectors/fileconnector/). 

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site do User Voice dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Conectar a dados locais](../logic-apps/logic-apps-gateway-connection.md) 
* [Monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Enterprise Integration para cenários B2B](../logic-apps/logic-apps-enterprise-integration-overview.md)
