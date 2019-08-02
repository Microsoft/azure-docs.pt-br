---
title: Conectar dados do Symantec AWS à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Symantec AWS ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 246d4cd7d64554ae575767cdba2e26066ad1720d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295613"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Conectar o Azure Sentinel ao AWS CloudTrail

Use o conector do AWS para transmitir todos os eventos do AWS CloudTrail para o Azure Sentinel. Esse processo de conexão delega o acesso para o Azure Sentinel aos logs de recursos do AWS, criando uma relação de confiança entre o AWS CloudTrail e o Azure sentinela. Isso é feito em AWS criando uma função que concede permissão ao Azure Sentinel para acessar seus logs do AWS.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.

> [!NOTE]
> O Azure Sentinel coleta eventos CloudTrail de todas as regiões. É recomendável que você não transmita eventos de uma região para outra.

## <a name="connect-aws"></a>Conectar à AWS 


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione a linha de **Amazon Web Services** na tabela e, no painel AWS à direita, clique em **abrir página de conector**.

1. Siga as instruções em **configuração** usando as etapas a seguir.
 
1.  No console do Amazon Web Services, em **segurança, identidade & conformidade**, selecione **iam**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Escolha **funções** e selecione **criar função**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Escolha **outra conta do AWS.** No campo **ID da conta** , insira a **ID da conta da Microsoft** (**123412341234**) que pode ser encontrada na página conector do AWS no portal do Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Certifique-se de que **exigir ID externa** esteja selecionado e insira a ID externa (ID do espaço de trabalho) que pode ser encontrada na página conector do AWS no portal do Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Em **anexar política de permissões** , selecione **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Insira uma marca (opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Em seguida, insira um **nome de função** e selecione o botão **criar função** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na lista funções, escolha a função que você criou.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copie a **função ARN**. No portal do Azure Sentinel, na tela do conector do Amazon Web Services, Cole-o na **função para adicionar** o campo e clique em **Adicionar**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Para usar o esquema relevante em Log Analytics para eventos AWS, pesquise **AWSCloudTrail**.



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o AWS CloudTrail ao Azure sentinela. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

