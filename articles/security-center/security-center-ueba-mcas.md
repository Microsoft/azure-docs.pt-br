---
title: Detecção de ameaças na Central de Segurança do Azure | Microsoft Docs
description: " Detectar ameaças e itens mal-intencionados ao integrar o Microsoft Cloud App Security à Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: af7896ec4afaeefda7261542bf593a89a7bb9ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551794"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA para usuários e recursos do Azure 

A Central de Segurança do Azure trabalha em parceria com o Microsoft Cloud App Security para fornecer alertas com base em UEBA (análise comportamental de usuários e de entidades) aos seus recursos e usuários do Azure (atividade do Azure). Esses alertas detectam anomalias no comportamento do usuário e são baseados na análise comportamental de usuários e de entidades e no ML ( aprendizado de máquina) para que você execute imediatamente uma detecção avançada de ameaças nas atividades das suas assinaturas. Como eles são habilitados automaticamente, as novas detecções de anomalias fornecem resultados imediatos ao fornecer detecções imediatas, visando inúmeras anomalias comportamentais entre os usuários e recursos associados à sua assinatura. Além disso, esses alertas aproveitam dados adicionais que já existem no mecanismo de detecção do Microsoft Cloud App Security, para ajudar a acelerar o processo de investigação e conter ameaças contínuas. 

> [!NOTE]
> A Central de Segurança do Azure, que pode armazenar uma cópia dos dados do cliente relacionados à segurança, coletados ou associados a um recurso do cliente (por exemplo, à máquina virtual ou ao locatário do Azure Active Directory): (a) na mesma área geográfica que esse recurso, exceto nessas áreas geográficas em que a Microsoft ainda precisa implantar a Central de Segurança do Azure e, nesse caso uma cópia desses dados será armazenada nos Estados Unidos; e (b) em que a Central de Segurança do Azure usa outro serviço online da Microsoft para processar esses dados, ela pode armazenar esses dados de acordo com as regras da localização geográfica desse outro serviço online.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma [licença do Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security) válida e ativada
- [Nível Standard da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Alertas de detecção de ameaças

A Central de Segurança dá suporte a alertas de detecção de anomalias do Cloud App Security, como:

**Viagem impossível**
-  Essa detecção identifica duas atividades do usuário (é uma única ou várias sessões) provenientes de locais geograficamente distantes em um período de tempo menor que o tempo que teria levado para o usuário viajar do primeiro local para o segundo, indicando que um usuário diferente está usando as mesmas credenciais. Essa detecção usa um algoritmo de aprendizado de máquina que ignora "falsos positivos" óbvios que contribuem para a condição de viagem impossível, como VPNs e locais geralmente usados por outros usuários na organização. A detecção tem um período inicial de aprendizado de sete dias, durante o qual aprende o padrão de atividade de um novo usuário.

**Atividade de país pouco frequente**
- Essa detecção considera os locais de atividades anteriores para determinar os locais novos e pouco frequentes. O mecanismo de detecção de anomalias armazena informações sobre locais anteriores usados por usuários na organização. Um alerta é disparado quando ocorre uma atividade em um local que não foi visitado recentemente ou nunca por algum usuário na organização. 

**Atividade de endereços IP anônimos**
- Essa detecção identifica que os usuários estavam ativos com base em um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados. Essa detecção usa um algoritmo que reduz "falsos positivos", como endereços IP marcados incorretamente que são amplamente usados pelos usuários na organização de aprendizado de máquina.
 
  ![alerta de detecção de ameaças](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Desabilitando alertas de detecção de ameaças

Esses alertas são habilitados por padrão, mas você pode desabilitá-los:

1. Na folha da Central de segurança, selecione **preços & configurações** e selecione a assinatura aplicável.
2. Clique em **Detecção de ameaças**.
3. Emb **Habilitar integrações**, desmarque a opção **Permitir que o Microsoft Cloud App Security acesse meus dados** e clique em **Salvar**.

   ![alerta de detecção de ameaças](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Há um período inicial de aprendizado de sete dias durante os quais nem todos os alertas de detecção de anomalias são gerados. Depois disso, cada sessão é comparada à atividade, quando os usuários estavam ativos, os endereços IP, os dispositivos, entre outros itens detectados ao longo do mês anterior e a pontuação de risco dessas atividades. Essas detecções fazem parte do mecanismo de detecção de anomalias do aprendizado de máquina que cria perfis do ambiente e dispara alertas em relação a uma linha de base que foi aprendida sobre a atividade da organização. Essas detecções também aproveitam os algoritmos de aprendizado de máquina desenvolvidos para criar perfis do padrão de usuários e de logon para reduzir falsos positivos.
>
  
## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como trabalhar com a detecção de ameaças na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
