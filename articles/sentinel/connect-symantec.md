---
title: Conectar-se dados ICDX Symantec para versão prévia do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados Symantec ICDX a Sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714533"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Conectar seu dispositivo ICDX Symantec 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Conector do Symantec ICDX permite que você se conecte facilmente todos os seus logs de solução de segurança Symantec com seu Sentinel do Azure, para exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança. Integração entre ICDX Symantec e o Azure Sentinel faz uso da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Configurar e conectar ICDX Symantec 

Symantec ICDX pode integrar e exportar logs diretamente para o Azure Sentinel.

1. Abra o Console de gerenciamento ICDX.
2. No menu de navegação à esquerda, selecione **Configuration** e, em seguida, o **encaminhadores** guia.
3. Na linha do Log Analytics do Microsoft Azure, clique em **mais**, seguido pelo **editar**. 
4. No **encaminhador do Log Analytics do Microsoft Azure** janela, defina o seguinte:
    - Deixe o nome de Log personalizado como padrão, SymantecICDX.
    - Copie a ID do espaço de trabalho e cole-à **identificador de cliente** campo. Cópia de **Primary key** e cole-o no campo de chave compartilhada. Você pode copiar esses valores do portal do Azure Sentinel selecionando **conectores de dados** e, em seguida **Symantec ICDX**.
6. Para usar o esquema relevante no Log Analytics para os eventos ICDX Symantec, pesquise **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como se conectar a Symantec ICDX a Sentinela do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

