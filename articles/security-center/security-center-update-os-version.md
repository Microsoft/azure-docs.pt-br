---
title: Atualizar a versão do SO na Central de Segurança do Azure | Microsoft Docs
description: Este artigo mostra como implementar a recomendação da Central de Segurança do Azure **Atualizar versão do sistema operacional**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905810"
---
# <a name="update-os-version-in-azure-security-center"></a>Atualizar a versão do sistema operacional na Central de Segurança do Azure
Para VMs (máquinas virtuais) em serviços de nuvem, a Central de Segurança do Azure recomendará que o SO (sistema operacional) seja atualizado se houver uma versão mais recente disponível.  Apenas serviços de nuvem da Web e funções de trabalho em execução em slots de produção são monitorados.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
> 
> 

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações**, selecione **Atualizar versão do sistema operacional**.
   ![Atualizar a versão do sistema operacional][1]
2. Isso abrirá a folha **Atualizar versão do sistema operacional**. Siga as etapas nessa folha para atualizar a versão do sistema operacional.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação da Central de Segurança "Atualizar versão do iOS". Para saber mais sobre os serviços de nuvem e atualizar a versão do sistema operacional para um serviço de nuvem, confira:

* [Visão geral dos Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md)
* [Como atualizar um serviço de nuvem](../cloud-services/cloud-services-update-azure-service.md)
* [Como configurar serviços de nuvem](../cloud-services/cloud-services-how-to-configure-portal.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço de localização.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
