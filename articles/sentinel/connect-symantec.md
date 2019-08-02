---
title: Conectar-se dados Symantec ICDx para versão prévia do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados Symantec ICDx a Sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673745"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Conectar seu dispositivo Symantec ICDx 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDx conector permite que você se conecte facilmente todos os seus logs de solução de segurança Symantec com seu Sentinel do Azure, para exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança. Integração entre Symantec ICDx e o Azure Sentinel faz uso da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Configurar e conectar Symantec ICDx 

Symantec ICDx pode integrar e exportar logs diretamente para o Azure Sentinel.

1. Abra o Console de gerenciamento ICDx para adicionar encaminhadores do Microsoft Azure Sentinel (Log Analytics).
2. Na barra de navegação ICDx, clique em **configuração**. 
3. Na parte superior a **Configuration** tela, clique em **encaminhadores**.
4. Sob **encaminhadores**, ao lado do Microsoft Azure Sentinel (Log Analytics), clique em **Add**. 
4. No **Microsoft Azure Sentinel (Log Analytics)** janela, clique em **Mostrar avançado**. 
5. Na parte superior da expandidos para janela do Microsoft Azure Sentinel (Log Analytics), faça o seguinte:
    -   **Nome**: Digite um nome para o encaminhador que tem não mais do que 30 caracteres. Escolha um nome exclusivo e significativo. Esse nome aparece na lista de encaminhadores na **Configuration** tela e nos painéis na **painel** tela. Por exemplo: Microsoft Azure Log Analytics East. Esse campo é obrigatório.
    -   **Descrição**: Digite uma descrição para o encaminhador. Essa descrição também aparece na lista de encaminhadores na **configuração** tela. Incluem detalhes como o tipo de evento que está sendo encaminhado e o grupo que precisa para inspecionar os dados.
    -   **Tipo de inicialização**: Selecione o método de inicialização para a configuração do encaminhador. As opções são manuais e automáticas.<br>O padrão é automático. 
6. Sob **eventos**, faça o seguinte: 
    - **Fonte**: Selecione um ou mais arquivos dos quais encaminhar eventos. Você pode selecionar o Active Directory coletor arquivos mortos (incluindo o arquivamento comum), órfãos arquivos mortos do coletor (ou seja, arquivos mortos para os coletores que foram excluídos), arquivos mortos do receptor ICDx ou o arquivo do sistema. <br>O padrão é comum de arquivamento.
      > [!NOTE]
      > Arquivos mortos do receptor ICDx são listados separadamente, por nome. 
 
    - **Filtro**: Adicione um filtro que especifica o subconjunto de eventos para encaminhar. Realize um dos seguintes procedimentos:
        - Para selecionar uma condição de filtro, clique em um tipo, atributo, operador e valor. 
        - No campo de filtro, revise sua condição de filtro. Você pode editá-lo diretamente no campo ou excluí-lo conforme necessário.
        - Clique e ou ou para adicionar a condição de filtro.
        - Você também pode clicar em consultas salvas para aplicar uma consulta salva.
    - **Incluído atributos**: Digite a lista delimitada por vírgulas de atributos a serem incluídos nos dados encaminhados. Os atributos incluídos têm precedência sobre atributos excluídos.
    - **Atributos excluídos**: Digite a lista delimitada por vírgulas de atributos a serem excluídos dos dados encaminhados.
    - **Tamanho do lote**: Selecione o número de eventos a serem enviados por lote. As opções são 10, 50, 100, 500 e 1000.<br>O padrão é 100. 
    - **Limite de taxa**: Selecione a taxa na qual os eventos serão encaminhados, expresso como eventos por segundo. Suas opções são ilimitado, 500, 1000, 5000, 10000. <br> O padrão é 5000. 
7. Sob **destino do Azure**, faça o seguinte: 
    - **ID do espaço de trabalho**: Cole a ID do espaço de trabalho abaixo. Esse campo é obrigatório.
    - **Chave primária**: Cole a chave primária abaixo. Esse campo é obrigatório.
    - **Nome de Log personalizado**: Digite o nome de log personalizado no Microsoft Azure portal do Log Analytics espaço de trabalho ao qual você pretende encaminhar eventos. O padrão é SymantecICDx. Esse campo é obrigatório.
8. Clique em *salvar* para concluir a configuração do encaminhador. 
9. Para iniciar o encaminhador, sob **opções**, clique em **mais** e, em seguida, **iniciar**.
10. Para usar o esquema relevante no Log Analytics para os eventos Symantec ICDx, pesquise **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como se conectar a Symantec ICDx a Sentinela do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

