---
title: Conectar dados de Cloud App Security ao Azure Sentinel | Microsoft Docs
description: Aprenda a usar o conector do Microsoft Cloud App Security (MCAS) para transmitir alertas e Cloud Discovery logs do MCAS para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 3312eed71865508e5e83d37c7ced8cf220f13ca9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97835101"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar dados de Microsoft Cloud App Security 

O conector do [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) permite transmitir alertas e [Cloud Discovery logs](/cloud-app-security/tutorial-shadow-it) do MCAS para o Azure Sentinel. Isso permitirá que você ganhe visibilidade em seus aplicativos de nuvem, obtenha análises sofisticadas para identificar e combater o ameaças cibernéticas e controlar como seus dados viajam.

## <a name="prerequisites"></a>Pré-requisitos

- Seu usuário deve ter permissões de leitura e gravação no espaço de trabalho.
- Seu usuário deve ter permissões de administrador global ou de administrador de segurança no locatário do espaço de trabalho.
- Para transmitir Cloud Discovery logs para o Azure Sentinel, [habilite o Azure Sentinel como seu Siem no Microsoft Cloud app Security](/cloud-app-security/siem-sentinel).

> [!IMPORTANT]
> A ingestão de logs de Cloud Discovery está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver Cloud App Security, verifique se ele está [habilitado em sua rede](/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security for implantado e ingerir seus dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.


1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**. Na lista de conectores, clique no bloco **Microsoft Cloud app Security** e, em seguida, no botão **abrir página do conector** no canto inferior direito.

1. Selecione quais logs você deseja transmitir para o Azure Sentinel; Você pode escolher **alertas** e **Cloud Discovery logs** (versão prévia). 

1. Clique em **Aplicar Alterações**.

1. Você pode selecionar se deseja que os alertas do Cloud App Security gerem automaticamente incidentes no Azure Sentinel. Em **criar incidentes – recomendado!**, selecione **habilitado** para ativar a regra de análise padrão que cria automaticamente incidentes de alertas. Em seguida, você pode editar essa regra em **análise**, na guia  **regras ativas** .

1. Para usar o esquema relevante no Log Analytics para alertas de Cloud App Security, digite `SecurityAlert` na janela de consulta. Para o esquema de logs de Cloud Discovery, digite `McasShadowItReporting` .

> [!NOTE]
> Cloud Discovery ajuda a detectar e identificar tendências agregando os dados das conexões de usuários subjacentes aos aplicativos de nuvem.
>
> Como Cloud Discovery dados são agregados por dia, lembre-se de que até 24 horas do valor dos dados mais recentes não serão refletidos no Azure Sentinel. Caso uma investigação de nível inferior exija dados mais imediatos, ela deve ser feita diretamente no dispositivo ou serviço de origem em que residem os dados brutos.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [internas](./tutorial-detect-threats-built-in.md) ou [personalizadas](tutorial-detect-threats-custom.md) .