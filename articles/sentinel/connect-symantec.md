---
title: Conectar dados do Symantec ICDx ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector do Symantec ICDx para conectar facilmente todos os seus logs de solução de segurança da Symantec com o Azure Sentinel.
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
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092699"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Conecte seu dispositivo Symantec ICDx 



O conector do Symantec ICDx permite que você conecte facilmente todos os seus logs de solução de segurança da Symantec com sua Sentinela do Azure, exiba painéis, crie alertas personalizados e melhore a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança. A integração entre o Symantec ICDx e o Azure Sentinel usa a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurar e conectar o Symantec ICDx 

O Symantec ICDx pode integrar e exportar logs diretamente para o Azure Sentinel.

1. Abra o console de gerenciamento do ICDx para adicionar encaminhadores Microsoft Azure Sentinel (Log Analytics).
2. Na barra de navegação do ICDx, clique em **configuração**. 
3. Na parte superior da tela de **configuração** , clique em **encaminhadores**.
4. Em **encaminhadores**, ao lado de Microsoft Azure sentinela (log Analytics), clique em **Adicionar**. 
4. Na janela **Microsoft Azure sentinela (log Analytics)** , clique em **Mostrar avançado**. 
5. Na parte superior da janela expandida para Microsoft Azure sentinela (Log Analytics), faça o seguinte:
    -   **Nome**: digite um nome para o encaminhador que não tenha mais de 30 caracteres. Escolha um nome exclusivo e significativo. Esse nome aparece na lista de encaminhadores na tela de **configuração** e nos painéis na tela do **painel** . Por exemplo: Microsoft Azure Log Analytics East. Esse campo é obrigatório.
    -   **Descrição**: digite uma descrição para o encaminhador. Essa descrição também aparece na lista de encaminhadores na tela de **configuração** . Inclua detalhes como o tipo de evento que está sendo encaminhado e o grupo que precisa inspecionar os dados.
    -   **Tipo de inicialização**: selecione o método de inicialização para a configuração do encaminhador. Suas opções são manual e automática.<br>O padrão é automático. 
6. Em **eventos**, faça o seguinte: 
    - **Origem**: selecione um ou mais arquivos dos quais deseja encaminhar eventos. Você pode selecionar arquivos de coletor ativos (incluindo o arquivo comum), arquivos de coletor órfãos (ou seja, arquivos para os coletores que você excluiu), arquivos de receptor de ICDx ou o arquivo morto do sistema. <br>O padrão é arquivo comum.
      > [!NOTE]
      > Os arquivos mortos do receptor ICDx são listados separadamente, por nome. 
 
    - **Filtro**: Adicione um filtro que especifica o subconjunto de eventos a serem encaminhadas. Realize um dos seguintes procedimentos:
        - Para selecionar uma condição de filtro, clique em um tipo, atributo, operador e valor. 
        - No campo filtro, examine sua condição de filtro. Você pode editá-lo diretamente no campo ou excluí-lo conforme necessário.
        - Clique em e ou ou em para adicionar à condição de filtro.
        - Você também pode clicar em consultas salvas para aplicar uma consulta salva.
    - **Atributos incluídos**: digite a lista delimitada por vírgulas de atributos a serem incluídos nos dados encaminhados. Os atributos incluídos têm precedência sobre atributos excluídos.
    - **Atributos excluídos**: digite a lista delimitada por vírgulas de atributos a serem excluídos dos dados encaminhados.
    - **Tamanho do lote**: selecione o número de eventos a serem enviados por lote. Suas opções são 10, 50, 100, 500 e 1000.<br>O padrão é 100. 
    - **Limite de taxa**: selecione a taxa na qual os eventos são encaminhados, expressos como eventos por segundo. Suas opções são ilimitadas, 500, 1000, 5000, 10000. <br> O padrão é 5000. 
7. Em **destino do Azure**, faça o seguinte: 
    - **ID do espaço de trabalho**: Cole a ID do espaço de trabalho abaixo. Esse campo é obrigatório.
    - **Chave primária**: Cole a chave primária abaixo. Esse campo é obrigatório.
    - **Nome do log personalizado**: digite o nome do log personalizado no espaço de trabalho portal do Microsoft Azure log Analytics para o qual você vai encaminhar eventos. O padrão é SymantecICDx. Esse campo é obrigatório.
8. Clique em *salvar* para concluir a configuração do encaminhador. 
9. Para iniciar o encaminhador, em **Opções**, clique em **mais** e em **Iniciar**.
10. Para usar o esquema relevante em Log Analytics para os eventos do Symantec ICDx, procure por **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Symantec ICDx ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


