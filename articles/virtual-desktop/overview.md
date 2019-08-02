---
title: O que é a Versão Prévia da Área de Trabalho Virtual do Windows?  - Azure
description: Uma visão geral da Versão Prévia da Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 07/17/2019
ms.author: helohr
ms.openlocfilehash: 03105232cb92a65f8c38d6d755910739a3fc4720
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305061"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>O que é a Versão Prévia da Área de Trabalho Virtual do Windows? 

Agora disponível em versão prévia pública, a Versão Prévia da Área de Trabalho Virtual do Windows é um serviço de virtualização de área de trabalho e aplicativo executado na nuvem.

Estas são as tarefas que você pode realizar ao executar a Área de Trabalho Virtual do Windows no Azure:

* Configurar uma implantação do Windows 10 de várias sessões que fornece uma escalabilidade completa do Windows 10
* Virtualizar o Office 365 ProPlus e otimizá-lo para execução em cenários virtuais de vários usuários
* Fornecer áreas de trabalho virtuais do Windows 7 com Atualizações de Segurança Estendida gratuitas
* Trazer seu RDS (Serviços de Área de Trabalho Remota) existente, além de aplicativos e áreas de trabalho do Windows Server para qualquer computador
* Virtualizar aplicativos e áreas de trabalho
* Gerenciar aplicativos e áreas de trabalho do Windows 10, Windows Server e Windows 7 com uma experiência unificada de gerenciamento

## <a name="introductory-video"></a>Vídeo de introdução

No vídeo a seguir, Scott Manchester demonstra alguns dos recursos da Área de Trabalho Virtual do Windows:

<br></br><iframe src="https://www.youtube-nocookie.com/embed/30dOLcZ4_9U" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="key-capabilities"></a>Principais recursos

Com a Área de Trabalho Virtual do Windows, você pode configurar um ambiente escalonável e flexível:

* Crie um ambiente completo de virtualização de área de trabalho em sua assinatura do Azure sem precisar executar servidores de gateway adicionais.
* Publique a quantidade de pools de host que precisar para acomodar cargas de trabalho diversificadas.
* Traga sua própria imagem para cargas de trabalho de produção ou de teste por meio da Galeria do Azure.
* Reduza os custos com recursos em pool de várias sessões. Com a nova funcionalidade de várias sessões do Windows 10 Enterprise exclusiva da Área de Trabalho Virtual do Windows e da função RDSH (Host da Sessão da Área de Trabalho Remota) no Windows Server, você pode reduzir consideravelmente o número de máquinas virtuais e a sobrecarga do sistema operacional, enquanto ainda fornece os mesmos recursos aos usuários.
* Forneça uma propriedade individual por meio de áreas de trabalho pessoais (persistentes).

Você pode implantar e gerenciar áreas de trabalho virtuais:

* Use as interfaces REST e PowerShell da Área de Trabalho Virtual do Windows para configurar os pools de host, criar grupos de aplicativos, atribuir usuários e publicar recursos.
* Publique aplicativos remotos completos de área de trabalho ou individuais por meio de um único pool de hosts, crie grupos de aplicativos individuais para diferentes conjuntos de usuários ou, até mesmo, atribua usuários a vários grupos de aplicativos para reduzir o número de imagens.
* Enquanto gerencia seu ambiente, use o acesso delegado interno para atribuir funções e coletar diagnóstico para compreender os vários erros de configuração ou de usuário.
* Use o novo serviço de Diagnóstico para solucionar problemas de erros.
* Gerencie somente a imagem e as máquinas virtuais, não a infraestrutura. Você não precisa gerenciar pessoalmente as funções da Área de Trabalho Remota, como faz com os Serviços de Área de Trabalho Remota, apenas as máquinas virtuais em sua assinatura do Azure.

Você também pode atribuir e conectar os usuários às áreas de trabalho virtuais:

* Depois de atribuídos, os usuários podem iniciar qualquer cliente da Área de Trabalho Virtual do Windows para conectar os usuários às áreas de trabalho e aos aplicativos publicados do Windows. Conecte-se em qualquer dispositivo por meio de um aplicativo nativo em seu dispositivo ou no cliente Web HTML5 da Área de Trabalho Virtual do Windows.
* Estabeleça usuários com segurança por meio de conexões reversas com o serviço, para que você nunca precise deixar as portas de entrada abertas.

## <a name="requirements"></a>Requisitos

Há alguns itens necessários para configurar a Área de Trabalho Virtual do Windows e conectar com êxito os usuários aos aplicativos e às áreas de trabalho do Windows.

Planejamos adicionar suporte aos sistemas operacionais a seguir, então verifique se você tem as [licenças apropriadas](https://azure.microsoft.com/pricing/details/virtual-desktop/) para os usuários com base na área de trabalho e nos aplicativos que você pretende implantar:

|SO|Licença necessária|
|---|---|
|Windows 10 Enterprise de várias sessões ou Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|CAL (licença de acesso para cliente) do RDS com o Software Assurance|

A infraestrutura precisa dos seguintes itens para dar suporte à Área de Trabalho Virtual do Windows:

* Um [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Um Windows Server Active Directory em sincronia com o Azure Active Directory. Isso pode ser habilitado por meio do:
  * Azure AD Connect
  * Azure AD Domain Services
* Uma assinatura do Azure, contendo uma rede virtual que contenha o Windows Server Active Directory ou que esteja conectada a ele
  
As máquinas virtuais do Azure criadas para a Área de Trabalho Virtual do Windows precisam ser:

* [Ingressadas no domínio padrão](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) ou [Ingressadas no AD híbridas](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). As máquinas virtuais não podem ser ingressadas no Azure AD.
* A execução de uma das seguintes [imagens do sistema operacional compatíveis](#supported-virtual-machine-os-image).

>[!NOTE]
>Caso precise de uma assinatura do Azure, [inscreva-se em uma avaliação gratuita de um mês](https://azure.microsoft.com/free/). Se estiver usando a versão de avaliação gratuita do Azure, use o Azure AD Domain Services para manter o Windows Server Active Directory em sincronia com o Azure Active Directory.

A Área de Trabalho Virtual do Windows é composta por áreas de trabalho e aplicativos do Windows que você fornece aos usuários e pela solução de gerenciamento, que é hospedada pela Microsoft como um serviço no Azure. Durante a versão prévia pública, as áreas de trabalho e os aplicativos podem ser implantados em VMs (máquinas virtuais) em qualquer região do Azure, e a solução de gerenciamento e os dados dessas VMs residirão nos Estados Unidos (região Leste dos EUA 2). Isso poderá resultar na transferência de dados para os Estados Unidos enquanto você testa o serviço durante a versão prévia pública. Começaremos expandindo a localização da solução de gerenciamento e dos dados para todas as regiões do Azure, começando com a disponibilidade geral.

Para otimizar o desempenho, verifique se a rede atende aos seguintes requisitos:

* A latência RTT (viagem de ida e volta) da rede do cliente para a região do Azure em que os pools de host foram implantados deve ser inferior a 150 ms.
* O tráfego de rede poderá fluir para fora das fronteiras do país/região quando as VMs que hospedam os aplicativos e as áreas de trabalho se conectarem ao serviço de gerenciamento.
* Para otimizar o desempenho da rede, recomendamos que as VMs do host da sessão sejam colocadas na mesma região do Azure do serviço de gerenciamento.

## <a name="supported-remote-desktop-clients"></a>Clientes compatíveis da Área de Trabalho Remota

Os seguintes clientes da Área de Trabalho Remota são compatíveis com a Área de Trabalho Virtual do Windows:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-image"></a>Imagem compatível de sistema operacional da máquina virtual

A Área de Trabalho Virtual do Windows dá suporte às seguintes imagens de sistema operacional:

* Windows 10 Enterprise de várias sessões
* Windows Server 2016

## <a name="next-steps"></a>Próximas etapas

Para começar, você precisará criar um locatário. Para saber mais sobre como criar um locatário, continue para o tutorial de criação de locatário.

> [!div class="nextstepaction"]
> [Criar um locatário na Versão Prévia da Área de Trabalho Virtual do Windows](tenant-setup-azure-active-directory.md)
