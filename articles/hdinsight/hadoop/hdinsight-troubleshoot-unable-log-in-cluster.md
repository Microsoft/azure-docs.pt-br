---
title: Não é possível fazer logon no cluster HDInsight do Azure
description: Solucionar problemas por que não é possível fazer logon no cluster Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944305"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Cenário: não é possível fazer logon no cluster HDInsight do Azure

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível fazer logon no cluster HDInsight do Azure.

## <a name="cause"></a>Causa

Os motivos podem variar. Tenha em mente que, ao fazer logon nos painéis do cluster ou do aplicativo, use o "logon do cluster" ou as credenciais HTTP. Ao conectar-se remotamente, use suas credenciais do Secure Shell (SSH) ou a área de trabalho remota.

## <a name="resolution"></a>Resolução

Para resolver problemas comuns, tente uma ou mais das etapas a seguir.

* Tente abrir o painel do cluster em uma nova guia do navegador no modo de privacidade.

* Se não for possível lembrar suas credenciais SSH, você poderá [redefinir as credenciais na interface do usuário do amAmbari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]