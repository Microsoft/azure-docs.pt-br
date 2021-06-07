---
title: O que é a Área de Trabalho Virtual do Windows? - Azure
description: Uma visão geral da Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7e14dbf5aab68f5ea2d96274606306378feedad2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445984"
---
# <a name="what-is-windows-virtual-desktop"></a>O que é a Área de Trabalho Virtual do Windows?

A Área de Trabalho Virtual do Windows é um serviço de virtualização de aplicativos e área de trabalho executado na nuvem.

Veja o que você pode fazer ao executar a Área de Trabalho Virtual do Windows no Azure:

* Configurar uma implantação do Windows 10 de várias sessões que fornece uma escalabilidade completa do Windows 10
* Virtualizar os Aplicativos do Microsoft 365 para Empresas e otimizá-los para execução em cenários virtuais de vários usuários
* Fornecer áreas de trabalho virtuais do Windows 7 com Atualizações de Segurança Estendida gratuitas
* Trazer seu RDS (Serviços de Área de Trabalho Remota) existente, além de aplicativos e áreas de trabalho do Windows Server para qualquer computador
* Virtualizar aplicativos e áreas de trabalho
* Gerenciar aplicativos e áreas de trabalho do Windows 10, Windows Server e Windows 7 com uma experiência unificada de gerenciamento

## <a name="introductory-video"></a>Vídeo de introdução

Saiba mais sobre a Área de Trabalho Virtual do Windows, por que ela é única e o que há de novo neste vídeo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Para obter mais vídeos sobre a Área de Trabalho Virtual do Windows, confira [nossa playlist](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Principais recursos

Com a Área de Trabalho Virtual do Windows, você pode configurar um ambiente escalonável e flexível:

* Crie um ambiente completo de virtualização de área de trabalho em sua assinatura do Azure sem precisar executar servidores de gateway adicionais.
* Publique a quantidade de pools de host que precisar para acomodar cargas de trabalho diversificadas.
* Traga sua própria imagem para cargas de trabalho de produção ou de teste por meio da Galeria do Azure.
* Reduza os custos com recursos em pool de várias sessões. Com a nova funcionalidade de várias sessões do Windows 10 Enterprise exclusiva da Área de Trabalho Virtual do Windows e da função RDSH (Host da Sessão da Área de Trabalho Remota) no Windows Server, você pode reduzir consideravelmente o número de máquinas virtuais e a sobrecarga do sistema operacional, enquanto ainda fornece os mesmos recursos aos usuários.
* Forneça uma propriedade individual por meio de áreas de trabalho pessoais (persistentes).

Você pode implantar e gerenciar áreas de trabalho virtuais:

* Use o portal do Azure, as interfaces REST e do PowerShell da Área de Trabalho Virtual do Windows para configurar os pools de host, criar grupos de aplicativos, atribuir usuários e publicar recursos.
* Publique aplicativos remotos completos de área de trabalho ou individuais por meio de um único pool de hosts, crie grupos de aplicativos individuais para diferentes conjuntos de usuários ou, até mesmo, atribua usuários a vários grupos de aplicativos para reduzir o número de imagens.
* Enquanto gerencia seu ambiente, use o acesso delegado interno para atribuir funções e coletar diagnóstico para compreender os vários erros de configuração ou de usuário.
* Use o novo serviço de Diagnóstico para solucionar problemas de erros.
* Gerencie somente a imagem e as máquinas virtuais, não a infraestrutura. Você não precisa gerenciar pessoalmente as funções da Área de Trabalho Remota, como faz com os Serviços de Área de Trabalho Remota, apenas as máquinas virtuais em sua assinatura do Azure.

Você também pode atribuir e conectar os usuários às áreas de trabalho virtuais:

* Depois de atribuídos, os usuários podem iniciar qualquer cliente da Área de Trabalho Virtual do Windows para conectar os usuários às áreas de trabalho e aos aplicativos publicados do Windows. Conecte-se em qualquer dispositivo por meio de um aplicativo nativo em seu dispositivo ou no cliente Web HTML5 da Área de Trabalho Virtual do Windows.
* Estabeleça usuários com segurança por meio de conexões reversas com o serviço, para que você nunca precise deixar as portas de entrada abertas.

## <a name="requirements"></a>Requisitos

Há alguns itens necessários para configurar a Área de Trabalho Virtual do Windows e conectar com êxito os usuários aos aplicativos e às áreas de trabalho do Windows.

Damos suporte aos seguintes sistemas operacionais; portanto, verifique se você tem as [licenças apropriadas](https://azure.microsoft.com/pricing/details/virtual-desktop/) para os usuários com base na área de trabalho e nos aplicativos que pretende implantar:

|Sistema operacional|Licença necessária|
|---|---|
|Windows 10 Enterprise de várias sessões ou Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|CAL (licença de acesso para cliente) do RDS com o Software Assurance|

A infraestrutura precisa dos seguintes itens para dar suporte à Área de Trabalho Virtual do Windows:

* Um [Azure Active Directory](../active-directory/index.yml).
* Um Windows Server Active Directory em sincronia com o Azure Active Directory. Configure isso usando o Azure AD Connect (para organizações híbridas) ou o Azure AD Domain Services (para organizações híbridas ou de nuvem).
  * Um Windows Server AD em sincronia com o Azure Active Directory. O usuário é originado do Windows Server AD, e a VM da Área de Trabalho Virtual do Windows é ingressada no domínio do Windows Server AD.
  * Um Windows Server AD em sincronia com o Azure Active Directory. O usuário é originado do Windows Server AD, e a VM da Área de Trabalho Virtual do Windows é ingressada no domínio do Azure AD Domain Services.
  * Um domínio do Azure AD Domain Services. O usuário é originado do Azure Active Directory, e a VM da Área de Trabalho Virtual do Windows é ingressada no domínio do Azure AD Domain Services.
* Uma assinatura do Azure, com o pai no mesmo locatário do Azure AD, contendo uma rede virtual que contenha a instância do Windows Server Active Directory ou do Azure AD DS ou que esteja conectada a ela.

Requisitos do usuário para se conectar à Área de Trabalho Virtual do Windows:

* O usuário precisa ser originado do mesmo Active Directory que está conectado ao Azure AD. A Área de Trabalho Virtual do Windows não dá suporte a contas B2B nem MSA.
* O UPN usado para assinar a Área de Trabalho Virtual do Windows precisa existir no domínio do Active Directory no qual a VM foi ingressada.

As máquinas virtuais do Azure criadas para a Área de Trabalho Virtual do Windows precisam ser:

* [Ingressadas no domínio padrão](../active-directory-domain-services/compare-identity-solutions.md) ou [Ingressadas no AD híbridas](../active-directory/devices/hybrid-azuread-join-plan.md). As máquinas virtuais não podem ser ingressadas no Azure AD.
* A execução de uma das seguintes [imagens do sistema operacional compatíveis](#supported-virtual-machine-os-images).

>[!NOTE]
>Caso precise de uma assinatura do Azure, [inscreva-se em uma avaliação gratuita de um mês](https://azure.microsoft.com/free/). Se estiver usando a versão de avaliação gratuita do Azure, use o Azure AD Domain Services para manter o Windows Server Active Directory em sincronia com o Azure Active Directory.

Para obter uma lista de URLs que você deve desbloquear para que a implantação de Área de Trabalho Virtual do Windows funcione como pretendida, confira nossa [Lista de URLs seguras](safe-url-list.md).

A Área de Trabalho Virtual do Windows é composta por áreas de trabalho e aplicativos do Windows que você fornece aos usuários e pela solução de gerenciamento, que é hospedada pela Microsoft como um serviço no Azure. As áreas de trabalho e os aplicativos podem ser implantados em VMs (máquinas virtuais) em qualquer região do Azure. A solução de gerenciamento e os dados dessas VMs residirão nos Estados Unidos. Isso poderá resultar na transferência de dados para os Estados Unidos.

Para otimizar o desempenho, verifique se a rede atende aos seguintes requisitos:

* A latência RTT (viagem de ida e volta) da rede do cliente para a região do Azure em que os pools de host foram implantados deve ser inferior a 150 ms. Use o [avaliador de experiência](https://azure.microsoft.com/services/virtual-desktop/assessment) para ver a integridade da sua conexão e a região recomendada do Azure.
* O tráfego de rede poderá fluir para fora das fronteiras do país/região quando as VMs que hospedam os aplicativos e as áreas de trabalho se conectarem ao serviço de gerenciamento.
* Para otimizar o desempenho da rede, recomendamos que as VMs do host da sessão sejam colocadas na mesma região do Azure do serviço de gerenciamento.

Você pode ver uma configuração de arquitetura típica da Área de Trabalho Virtual do Windows para a empresa em nossa [documentação de arquitetura](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## <a name="supported-remote-desktop-clients"></a>Clientes compatíveis da Área de Trabalho Remota

Os seguintes clientes da Área de Trabalho Remota são compatíveis com a Área de Trabalho Virtual do Windows:

* [Área de trabalho do Windows](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Cliente para Microsoft Store

> [!IMPORTANT]
> A Área de Trabalho Virtual do Windows não dá suporte ao cliente de RADC (Conexões de RemoteApp e Área de Trabalho) nem ao cliente de Conexão de Área de Trabalho Remota (MSTSC).

Para saber mais sobre as URLs que você precisa desbloquear para usar os clientes, confira a [Lista de URLs seguras](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Imagens compatíveis do sistema operacional da máquina virtual

A Área de Trabalho Virtual do Windows é compatível com as seguintes imagens do sistema operacional x64:

* Windows 10 Enterprise de várias sessões, versão 1809 ou posterior
* Windows 10 Enterprise versão 1809 ou posterior (somente o Canal Semestral)
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

A Área de Trabalho Virtual do Windows não é compatível com imagens do sistema operacional x86 (32 bits), Windows 10 Enterprise N, Windows 10 Pro ou Windows 10 Enterprise KN. O Windows 7 também não dá suporte a nenhuma solução de perfil baseada em VHD ou VHDX hospedada no Armazenamento do Azure gerenciado devido a uma limitação de tamanho de setor.

As opções de automação e implantação disponíveis dependem do sistema operacional e da versão escolhidos, conforme mostrado na tabela a seguir:

|Sistema operacional|Galeria de Imagens do Azure|Implantação manual de VM|Integração de modelos do Azure Resource Manager|Provisionar pools de hosts no Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (multissessão), versão 2004|Sim|Sim|Sim|Sim|
|Windows 10 Enterprise (multissessão), versão 1909|Sim|Sim|Sim|Sim|
|Windows 10 Enterprise (multissessão), versão 1903|Sim|Sim|Não|Não|
|Windows 10 Enterprise (multissessão), versão 1809|Sim|Sim|Não|Não|
|Windows 7 Enterprise|Sim|Sim|Não|Não|
|Windows Server 2019|Sim|Sim|Não|Não|
|Windows Server 2016|Sim|Sim|Sim|Sim|
|Windows Server 2012 R2|Sim|Sim|Não|Não|

## <a name="next-steps"></a>Próximas etapas

Se estiver usando a Área de Trabalho Virtual do Windows (clássica), comece com nosso tutorial em [Criar um locatário na Área de Trabalho Virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Se estiver usando a Área de Trabalho Virtual do Windows com a integração ao Azure Resource Manager, você precisará criar um pool de host. Vá para o tutorial a seguir para começar.

> [!div class="nextstepaction"]
> [Criar um pool de host com o portal do Azure](create-host-pools-azure-marketplace.md)
