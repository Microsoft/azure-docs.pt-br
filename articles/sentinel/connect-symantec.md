---
title: Conectar dados do Symantec ICDx ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector do Symantec ICDx para conectar com facilidade todos os seus logs da solução de segurança da Symantec ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092699"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Conectar seu dispositivo do Symantec ICDx 



O conector do Symantec ICDx permite que você conecte com facilidade todos os seus logs da solução de segurança da Symantec ao Azure Sentinel para ver painéis, criar alertas personalizados e aprimorar a investigação. Isso proporciona mais insights sobre a rede da sua organização e aprimora suas funcionalidades de operação de segurança. A integração entre o Symantec ICDx e o Azure Sentinel usa a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do workspace no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurar e conectar o Symantec ICDx 

Os logs de atividades do Symantec ICDx podem integrar os logs diretamente ao Azure Sentinel, além de exportá-los.

1. Abra o Console de Gerenciamento do ICDx para adicionar encaminhadores do Microsoft Azure Sentinel (Log Analytics).
2. Na barra de navegação do ICDx, clique em **Configuração**. 
3. Na parte superior da tela **Configuração**, clique em **Encaminhadores**.
4. Em **Encaminhadores**, ao lado do Microsoft Azure Sentinel (Log Analytics), clique em **Adicionar**. 
4. Na janela **Microsoft Azure Sentinel (Log Analytics)** , clique em **Mostrar Avançado**. 
5. Na parte superior da janela expandida do Microsoft Azure Sentinel (Log Analytics), faça o seguinte:
    -   **Nome**: digite um nome para o encaminhador com, no máximo, 30 caracteres. Escolha um nome exclusivo e significativo. Esse nome é exibido na lista de encaminhadores na tela **Configuração** e nos painéis na tela **Painel**. Por exemplo: Microsoft Azure Log Analytics Leste. Esse campo é obrigatório.
    -   **Descrição**: digite uma descrição para o encaminhador. Essa descrição também é exibida na lista de encaminhadores na tela **Configuração**. Inclua detalhes como o tipo de evento que está sendo encaminhado e o grupo que precisa inspecionar os dados.
    -   **Tipo de Inicialização**: selecione o método de inicialização para a configuração do encaminhador. As opções são manual e automático.<br>O padrão é Automático. 
6. Em **Eventos**, faça o seguinte: 
    - **Origem**: selecione um ou mais arquivos dos quais deseja encaminhar eventos. Você pode selecionar arquivos mortos de coletor ativo (incluindo o Arquivo Morto Comum), arquivos mortos de coletor órfão (ou seja, arquivos mortos para os coletores que você excluiu), arquivos mortos de receptor do ICDx ou o Arquivo Morto do Sistema. <br>O padrão é Arquivo Morto Comum.
      > [!NOTE]
      > Os arquivos mortos de receptor do ICDx são listados separadamente, por nome. 
 
    - **Filtro**: adicione um filtro que especifica o subconjunto de eventos a serem encaminhados. Realize um dos seguintes procedimentos:
        - Para selecionar uma condição de filtro, clique em um Tipo, um Atributo, um Operador e um Valor. 
        - No campo Filtro, examine a condição de filtro. Você pode editá-la diretamente no campo ou excluí-la conforme necessário.
        - Clique em AND ou OR para adicioná-lo à condição de filtro.
        - Clique também em Consultas Salvas para aplicar uma consulta salva.
    - **Atributos Incluídos**: digite a lista delimitada por vírgula de atributos a serem incluídos nos dados encaminhados. Os atributos incluídos têm precedência sobre os atributos excluídos.
    - **Atributos Excluídos**: digite a lista delimitada por vírgula de atributos a serem excluídos dos dados encaminhados.
    - **Tamanho do Lote**: selecione o número de eventos a serem enviados por lote. As opções são 10, 50, 100, 500 e 1.000.<br>O padrão é 100. 
    - **Limite de Taxa**: selecione a taxa na qual os eventos são encaminhados, expressa como eventos por segundo. As opções são Ilimitado, 500, 1.000, 5.000 e 10.000. <br> O padrão é 5000. 
7. Em **Destino do Azure**, faça o seguinte: 
    - **ID do Workspace**: cole a ID do Workspace abaixo. Esse campo é obrigatório.
    - **Chave Primária**: cole a chave primária abaixo. Esse campo é obrigatório.
    - **Nome do Log Personalizado**: digite o nome do log personalizado no workspace do Log Analytics no portal do Microsoft Azure para o qual você encaminhará os eventos. O padrão é SymantecICDx. Esse campo é obrigatório.
8. Clique em *Salvar* para concluir a configuração do encaminhador. 
9. Para iniciar o encaminhador, em **Opções**, clique em **Mais** e em **Iniciar**.
10. Para usar o esquema relevante no Log Analytics para os eventos do Symantec ICDx, procure **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a ser exibidos no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Symantec ICDx ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


