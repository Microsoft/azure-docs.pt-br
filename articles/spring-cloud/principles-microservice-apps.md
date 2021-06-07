---
title: Java e sistema operacional base para aplicativos de microserviço do Azure Spring Cloud
description: Princípios para manter o sistema operacional de Java e base íntegro para aplicativos de microserviço do Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c90062f1968cc7be5a742a67363f57b9632fdfa
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877766"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java e sistema operacional base para Aplicativos de microsserviços do Spring

**Este artigo aplica-se a:** ✔️ Java

Veja a seguir os princípios para manter o sistema operacional de Java e base íntegro para aplicativos de microatendimento Spring.
## <a name="principles-for-healthy-java-and-base-os"></a>Princípios para Java íntegro e so base
* Deve ser o mesmo sistema operacional base em camadas-básico | Padrão | Especiais.
    * Atualmente, os aplicativos no Azure Spring Cloud usam uma combinação de Debian 10 e Ubuntu 18, 4.
    * O serviço de compilação do VMware usa o Ubuntu 18, 4.
* Deve ser o mesmo sistema operacional base, independentemente dos pontos de início da implantação-origem | VIDRO
    * Atualmente, os aplicativos no Azure Spring Cloud usam uma combinação de Debian 10 e Ubuntu 18, 4.
* O sistema operacional base deve estar livre de vulnerabilidades de segurança.
    * O sistema operacional base Debian 10 tem 147 abrir CVEs.
    * O sistema operacional de base Ubuntu 18, 4 tem 132 abra CVEs.
* Deve usar o JRE-sem periféricos.
    * Atualmente, os aplicativos no Azure Spring Cloud usam o JDK. JRE-sem periféricos é uma imagem menor.
* Deve usar as compilações mais recentes do Java.
    * Atualmente, os aplicativos no Azure Spring Cloud usam o Build 242 do Java 8. Essa é uma compilação desatualizada.
 
Os sistemas azul examinarão continuamente as alterações nos sistemas operacionais de base e manterão as últimas imagens criadas atualizadas. O Azure Spring Cloud procura alterações em imagens e as atualiza continuamente entre implantações.
 
## <a name="faq-for-azure-spring-cloud"></a>Perguntas frequentes do Azure Spring Cloud

* Quais versões do Java têm suporte? Versão principal e número de Build.
    * Suporte a versões LTS-Java 8 e 11.
    * Usa a compilação mais recente-por exemplo, no momento, o Java 8 Build 252 e o Java 11 Build 7.
* Quem construiu esses tempos de execução Java?
    * Sistemas azul.
* Qual é o sistema operacional base para as imagens?
    * Ubuntu 20, 4 LTS (focal fossa). Os aplicativos continuarão a permanecer na versão mais recente do LTS do Ubuntu.
    * Consulte [Ubuntu 20, 4 LTS (focal fossa)](http://releases.ubuntu.com/focal/)
* Como posso baixar um tempo de execução Java com suporte para dev local? 
    * Consulte [instalar o JDK para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-install)
* Como posso obter suporte para problemas no nível do tempo de execução Java?
    * Abra um tíquete de suporte com o suporte do Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Implantação padrão no Azure Spring Cloud

> ![Implantação padrão](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md)
* [Suporte a longo prazo Java para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-long-term-support)