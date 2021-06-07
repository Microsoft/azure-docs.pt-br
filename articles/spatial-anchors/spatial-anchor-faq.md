---
title: Perguntas frequentes
description: Perguntas frequentes sobre o serviço de Âncoras Espaciais do Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: b67dcf038dbe13b8c040f317bb117f3b7a7afdde
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384231"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Perguntas frequentes sobre as Âncoras Espaciais do Azure

Âncoras Espaciais do Azure é um serviço de nuvem gerenciado e uma plataforma de desenvolvedor que permite experiências de realidade misturada com reconhecimento espacial multiusuários entre dispositivos Android, iOS e HoloLens.

Para obter mais informações, veja [Visão geral de Âncoras Espaciais do Azure](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Perguntas frequentes sobre o produto Âncoras Espaciais do Azure

**P: Quais dispositivos são compatíveis com as Âncoras Espaciais do Azure?**

**R:** O recurso Âncoras Espaciais do Azure permite aos desenvolvedores compilar aplicativos no HoloLens, em dispositivos iOS com suporte para ARKit e em dispositivos Android com suporte para ARCore; para iOS e Android, isso inclui telefones e tablets.

**P: Eu preciso estar conectado à nuvem para usar Âncoras Espaciais do Azure?**

**R:** O recurso Âncoras Espaciais do Azure atualmente exige uma conexão de rede com a Internet. Envie seus comentários em nosso [site de comentários](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**P: Quais são os requisitos de conectividade para Âncoras Espaciais do Azure?**

**R:** O recurso Âncoras Espaciais do Azure funciona com conexões de banda larga móvel e Wi-Fi.

**P: Com que exatidão o recurso Âncoras Espaciais do Azure pode localizar âncoras?**

**R:** Muitos fatores afetam a precisão da localização de âncoras, como condições de iluminação, objetos no ambiente e até mesmo a superfície em que a âncora é colocada. Para determinar se a precisão atenderá às suas necessidades, tente as âncoras representativos do local em que você planeja usá-las. Se você encontrar ambientes em que a precisão não está atendendo às suas necessidades, veja [Registro em log e diagnósticos em Âncoras Espaciais do Azure](./concepts/logging-diagnostics.md).

**P: Quanto tempo leva para criar e localizar as âncoras?**

**R:** O tempo necessário para criar e localizar as âncoras depende de muitos fatores, como conexão de rede, processamento e carga do dispositivo e o ambiente específico. Temos clientes criando aplicativos em vários setores, como manufatura, varejo e jogos, o que indica que o serviço permite uma excelente experiência do usuário para os cenários deles.

## <a name="privacy-faq"></a>Perguntas frequentes sobre privacidade

**P: Quando meu aplicativo coloca uma Âncora Espacial em algum lugar, todos os aplicativos têm acesso a ela?**

**R:** As âncoras são isoladas por conta do Azure. Somente os aplicativos aos quais você permite acesso à sua conta poderão acessar as âncoras de dentro da conta.

**P: Como o recurso Âncoras Espaciais do Azure armazena dados?**

**R:** Todos os dados são armazenados criptografados com uma chave de criptografia de dados gerenciados da Microsoft e todos os dados são armazenados regionalmente para cada um dos recursos.

**P: Quais informações sobre um ambiente são transmitidas e armazenadas no serviço ao usar Âncoras Espaciais do Azure? Imagens do ambiente são transmitidas e armazenadas?**

**R**: Ao criar ou localizar âncoras, imagens do ambiente são processadas no dispositivo em um formato derivado. Esse formato derivado é transmitido e armazenado no serviço.

Para transparência, abaixo está uma imagem de um ambiente e a nuvem de pontos esparsa derivada. A nuvem do ponto mostra a representação geométrica do ambiente que é transmitido e armazenado no serviço. Para cada ponto na nuvem ponto esparsas, podemos transmitir e armazenar um hash das características visuais desse ponto. O hash é derivado de dados de pixel, mas não contém nenhum dado de pixel.

O recurso Âncoras Espaciais do Azure segue os [Termos do Contrato de Serviço do Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) e a [Política de Privacidade da Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Um ambiente e sua nuvem de pontos esparsa derivada](./media/sparse-point-cloud.png)
*Figura 1: Um ambiente e sua nuvem de pontos esparsa derivada*

**P: Há uma maneira de enviar informações de diagnóstico à Microsoft?**

**R**: Sim. O recurso Âncoras Espaciais do Azure tem um modo de diagnóstico que os desenvolvedores podem optar por aceitar por meio da API de Âncoras Espaciais do Azure. Isso será útil, por exemplo, se você encontrar um ambiente no qual não consiga criar e localizar as âncoras de maneira previsível. Podemos perguntar se você pode enviar um relatório de diagnóstico contendo informações que nos ajudem na depuração. Para obter mais informações, veja [Registro em log e diagnósticos em Âncoras Espaciais do Azure](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Preço sobre disponibilidade e preços

**P: Vocês fornece um SLA?**

**R:** Como é padrão para serviços do Azure, nosso objetivo é uma disponibilidade superior a 99,9%. 

**P: Posso publicar meus aplicativos usando Âncoras Espaciais do Azure em lojas de aplicativos? Posso usar Âncoras Espaciais do Azure para cenários de produção de missão crítica?**

**R:** Sim, as Âncoras Espaciais do Azure estão em disponibilidade geral e têm um SLA de serviços padrão do Azure. Convidamos você a desenvolver aplicativos para suas implantações de produção e [compartilhar seus comentários](https://feedback.azure.com/forums/919252-azure-spatial-anchors) sobre o produto conosco.

**P: Vocês têm limites de limitação em vigor?**

**R**: Sim, temos limites de limitação.  Não esperamos que você os atinja para teste e desenvolvimento de aplicativos típicos. Para implantações de produção, estamos prontos para dar suporte aos requisitos de alta escala de nossos clientes. [Entre em contato conosco](mailto:azuremrscontact@microsoft.com) para discutir isso.

**P: Em quais regiões o recurso Âncoras Espaciais do Azure está disponível?**

**R:** As Âncoras Espaciais do Azure estão disponíveis no momento no Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Centro-Sul dos EUA, Oeste da Europa, Norte da Europa, Sul do Reino Unido e Leste da Austrália. Ele será disponibilizado em outras regiões no futuro.

Isso significa que há tanto computação quanto armazenamento habilitando esse serviço nessas regiões. Dito isso, não há nenhuma restrição quanto ao local onde os clientes estão localizados. 

**P: Vocês cobram pelo recurso Âncoras Espaciais do Azure?**

**R:** Você pode encontrar detalhes sobre preços em nossa [página de preços](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Perguntas frequentes técnicas

**P: Como funciona o recurso Âncoras Espaciais do Azure?**

**R:** O recurso Âncoras Espaciais do Azure depende de rastreadores de realidade misturada/realidade aumentada. Esses rastreadores interpretam o ambiente com câmeras e rastreiam o dispositivo em 6DoF (6 graus de liberdade) enquanto se movimentam pelo espaço.

Dado um rastreador 6DoF como um bloco de construção, o recurso Âncoras Espaciais do Azure permite que você designe determinados pontos de interesse no ambiente real como pontos de "ancoragem". Você pode, por exemplo, usar uma âncora para renderizar o conteúdo em um local específico no mundo real.

Quando você cria uma âncora, o SDK do cliente captura informações de ambiente em torno daquele ponto e as transmite para o serviço. Se outro dispositivo procurar a âncora nesse mesmo espaço, dados semelhantes serão transmitidos para o serviço. Esses dados serão comparados com os dados do ambiente armazenados anteriormente. A posição da âncora em relação ao dispositivo então é enviada de volta para uso no aplicativo.

**P: Como o recurso Âncoras Espaciais do Azure integra-se com ARKit e ARCore no iOS e Android?**

**R:** O recurso Âncoras Espaciais do Azure aproveita as funcionalidades de acompanhamento nativas do ARKit e do ARCore. Além disso, nossos SDKs para iOS e Android oferecem recursos como persistir âncoras em um serviço de nuvem gerenciado e permitir que seus aplicativos localizem essas âncoras novamente simplesmente conectando-se ao serviço.

**P: Como o recurso Âncoras Espaciais do Azure integra-se com o HoloLens?**

**R:** O recurso Âncoras Espaciais do Azure aproveita as funcionalidades de acompanhamento nativas do HoloLens. Nós fornecemos um SDK de Âncoras Espaciais do Azure para criar aplicativos no HoloLens. O SDK integra-se com os recursos nativos do HoloLens e fornece funcionalidades adicionais. Essas funcionalidades incluem permitir que os desenvolvedores de aplicativos mantenham as âncoras em um serviço de nuvem gerenciado e permitir que seus aplicativos localizem essas âncoras novamente conectando-se ao serviço.

**P: A quais plataformas e idiomas o recurso Âncoras Espaciais do Azure dá suporte?**

**R:** Os desenvolvedores podem criar aplicativos com Âncoras Espaciais do Azure usando ferramentas e familiares estruturas para o dispositivo:

- Unity em HoloLens, iOS e Android
- Xamarin no iOS e no Android
- Swift ou Objective-C em iOS
- Java ou o NDK do Android em Android
- C++/WinRT em HoloLens

Introdução ao [desenvolvimento aqui](index.yml).

**P: Ele funciona com Unreal?**

**R:** O suporte para Unreal será considerado no futuro.

**P: Quais portas e protocolos usam Âncoras Espaciais do Azure?**

**R:** As Âncoras Espaciais do Azure se comunicam pela porta TCP 443 usando um protocolo criptografado. Para autenticação, é usado o [Azure Active Directory](../active-directory/index.yml), que se comunica usando HTTPS pela porta 443.