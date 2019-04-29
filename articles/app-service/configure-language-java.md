---
title: Configurar o Windows aplicativos Java – serviço de aplicativo do Azure | Microsoft Docs
description: Saiba como configurar aplicativos Java executados nas instâncias padrão do Windows no serviço de aplicativo do Azure.
keywords: o serviço de aplicativo do Azure, aplicativo web, windows, sistemas operacionais e java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: efeab014c7d92a6c19d516a121fbc4436925be75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850980"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurar um Windows aplicativo Java para o serviço de aplicativo do Azure

Serviço de aplicativo do Azure permite aos desenvolvedores de Java para rapidamente criar, implantar e dimensionar seu Tomcat ou Java Standard Edition (SE) por pacotes de aplicativos web em um serviço totalmente gerenciado baseado em Windows. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece instruções para desenvolvedores de Java usando o serviço de aplicativo e principais conceitos. Se você nunca usou o serviço de aplicativo do Azure, você deve ler por meio de [início rápido de Java](app-service-web-get-started-java.md) primeiro. Perguntas gerais sobre como usar o serviço de aplicativo que não são específicas para o desenvolvimento de Java são respondidas na [perguntas frequentes sobre o aplicativo de serviço Windows](faq-configuration-and-management.md).

> [!NOTE]
> Não encontrou o que você está procurando? Consulte a [perguntas frequentes sobre sistemas operacionais do Windows](faq-configuration-and-management.md) ou o [guia de configuração do Linux de Java](containers/app-service-linux-java.md) para obter informações sobre como implantar e proteger seu aplicativo Java.

## <a name="configuring-tomcat"></a>Configuração do Tomcat

Para editar do Tomcat `server.xml` ou outros arquivos de configuração, primeiro anote sua versão principal do Tomcat no portal.

1. Localize o diretório base do Tomcat para sua versão, executando o `env` comando. Pesquise a variável de ambiente que começa com `AZURE_TOMCAT`e corresponde à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório do Tomcat para Tomcat 8.5.
1. Depois de ter identificado o diretório base do Tomcat para a sua versão, copie o diretório de configuração para `D:\home`. Por exemplo, se `AZURE_TOMCAT85_HOME` tinha um valor de `D:\Program Files (x86)\apache-tomcat-8.5.37`, o caminho completo do diretório copiado configuração seria `D:\home\tomcat\conf`.

Por fim, reinicie o Serviço de Aplicativo. As implantações devem ir para `D:\home\site\wwwroot\webapps` exatamente como antes.

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do tempo de execução do Java

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O JDK (Java Development Kit) com suporte do Azure é o [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido pela [Azul Systems](https://www.azul.com/).

Atualizações de versão principal serão fornecidas por meio de novas opções de tempo de execução no serviço de aplicativo do Azure para Windows. Os clientes podem atualizar para essas versões mais recentes do Java configurando a implantação do Serviço de Aplicativo. Além disso, são responsáveis por testar e garantir que a atualização da versão principal atenda às suas necessidades.

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um tempo de execução do Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de reprovação pelo menos seis meses antes de o tempo de execução ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Production Edition do Azul Zulu Enterprise JDK para desenvolvimento local do [site de download da Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

Suporte de produto para o [com suporte pelo Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) está disponível por meio da Microsoft ao desenvolver para o Azure ou [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [qualificado plano de suporte do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o Azul Zulu JDK por meio do Suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Este tópico fornece a instrução de tempo de execução Java de suporte para o serviço de aplicativo do Azure no Windows.

- Para saber mais sobre como hospedar aplicativos web com o serviço de aplicativo do Azure, consulte [visão geral do serviço de aplicativo](overview.md).
- Para obter informações sobre o Java no desenvolvimento do Azure, consulte [do Azure para o Centro de desenvolvimento Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
