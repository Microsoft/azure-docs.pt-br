---
title: Entender as estações de trabalho de seguras e gerenciado do Azure - Active Directory do Azure
description: Saiba mais sobre as estações de trabalho seguras e gerenciada pelo Azure e entenda por que eles são importantes.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491585"
---
# <a name="understand-secure-azure-managed-workstations"></a>Entender as estações de trabalho seguras e gerenciada pelo Azure

Estações de trabalho segura e isoladas são extremamente importantes para a segurança das funções confidenciais, como administradores, desenvolvedores e operadores essenciais do serviço. Se a segurança da estação de trabalho de cliente estiver comprometida, muitos controles de segurança e garantias podem falhar ou de vigorar.

Este documento explica o que você precisa para a criação de uma estação de trabalho segura, geralmente conhecida como uma estação de trabalho de acesso privilegiado (PAW). O artigo também contém instruções detalhadas sobre como configurar os controles de segurança iniciais. Este guia descreve como baseado em nuvem tecnologia podem gerenciar o serviço. Ele depende dos recursos de segurança que foram introduzidos no Windows 10RS5, Microsoft Defender Advanced Threat ATP (proteção), Active Directory do Azure e Intune.

> [!NOTE]
> Este artigo explica o conceito de uma estação de trabalho segura e sua importância. Se você já estiver familiarizado com o conceito e quiser ignorar a implantação, visite [implantar uma estação de trabalho segura](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Por que o acesso de estação de trabalho segura é importante

A rápida adoção de serviços de nuvem e a capacidade de trabalhar em qualquer lugar criou um novo método de exploração. Explorando os controles de segurança fraca em dispositivos em que os administradores trabalham, os invasores podem ganhar acesso a recursos privilegiados.

Uso indevido de privilégios e ataques de cadeia de suprimentos estão entre os principais cinco métodos usados pelos invasores para organizações de violação. Eles também são o segundo mais comumente detectada táticas em incidentes relatados em 2018 de acordo com o [relatório de ameaças da Verizon](https://enterprise.verizon.com/resources/reports/dbir/)e o [Security Intelligence Report](https://aka.ms/sir).

A maioria dos invasores siga estas etapas:

1. Reconhecimento de encontrar uma maneira, geralmente específico a um setor.
1. Análise para coletar informações e identificar a melhor maneira para invadir uma estação de trabalho que é percebida como um valor baixo.
1. A persistência para procurar um meio mover [lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Extração de dados confidenciais e sigilosas.

Durante o reconhecimento, os invasores frequentemente invadir dispositivos que parecem baixo risco ou subestimado. Eles usam esses dispositivos vulneráveis para localizar uma oportunidade para a movimentação lateral e para localizar dispositivos e usuários administrativos. Depois que elas acessem as funções de usuário com privilégios, os invasores identificam se infiltrar e com êxito os dados de alto valor que os dados.

![Padrão de comprometimento típico](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento descreve uma solução que pode ajudar a proteger seus dispositivos de computação contra esses ataques laterais. A solução isola o gerenciamento e os serviços de dispositivos de produtividade menos valiosos, interromper a cadeia antes do dispositivo que tem acesso aos recursos de nuvem confidenciais pode invadido. A solução usa serviços nativos do Azure que fazem parte da pilha do Microsoft 365 Enterprise:

* Intune para gerenciamento de dispositivo e uma lista segura de aplicativos e URLs
* AutoPilot para atualização, implantação e configuração do dispositivo
* Azure AD para gerenciamento de usuário, acesso condicional e autenticação multifator
* Windows 10 (versão atual) para a experiência de usuário e de atestado de integridade do dispositivo
* Defender ATP para proteção de ponto de extremidade gerenciados na nuvem, detecção e resposta
* Acesso aos recursos do Azure AD PIM para gerenciar a autorização e just-in-time (JIT) privilegiado

## <a name="who-benefits-from-a-secure-workstation"></a>Quem se beneficia de uma estação de trabalho segura?

Todos os operadores e os usuários se beneficiam ao usar uma estação de trabalho segura. Um invasor que compromete um PC ou dispositivo pode representar armazenado em cache todas as contas. Quando conectado ao dispositivo, eles também podem usar as credenciais e tokens. Esse risco torna importante para proteger os dispositivos que são usados para funções privilegiadas, incluindo direitos administrativos. Dispositivos com as contas privilegiadas são alvos de movimento lateral e ataques de elevação de privilégio. Essas contas podem ser usadas para uma variedade de ativos, como:

* Administrador de sistemas baseados em nuvem ou local
* Estação de trabalho do desenvolvedor para sistemas críticos
* Administrador da conta de mídia social com alta exposição
* Estação de trabalho altamente confidencial, como um terminal de pagamento SWIFT
* Segredos de comerciais de tratamento de estação de trabalho

Para reduzir o risco, você deve implementar controles de segurança com privilégios elevados para estações de trabalho com privilégios que fazem usam dessas contas. Para obter mais informações, consulte o [guia de implantação de recursos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [roteiro do Office 365](https://aka.ms/o365secroadmap), e [roteiro da proteção de acesso privilegiado](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Por que usar estações de trabalho dedicadas?

Embora seja possível adicionar segurança para um dispositivo existente, é melhor começar com uma base segura. Para colocar a sua organização na melhor posição para manter um nível de segurança alta, comece com um dispositivo que você sabe que é seguro e implementar um conjunto de controles de segurança conhecidas.

Um número crescente de vetores de ataque por meio de email e navegação na web fica cada vez mais difícil certificar-se de que um dispositivo pode ser confiável. Este guia pressupõe que uma estação de trabalho dedicada é isolada de email, navegação e produtividade padrão. Remoção de produtividade, navegação na web e email de um dispositivo pode ter um impacto negativo na produtividade. No entanto, essa proteção é geralmente aceitável para cenários em que as tarefas de trabalho não exigem explicitamente e risco de um incidente de segurança é alto.

> [!NOTE]
> Navegação na Web aqui refere-se ao acesso geral a sites arbitrários que podem ser uma atividade de alto risco. Essa navegação é distintamente diferente do uso de um navegador da web para acessar um pequeno número de sites administrativos bem conhecidos para serviços como o Azure, Office 365, outros provedores de nuvem e aplicativos SaaS.

Estratégias de contenção reforçar a segurança, aumentando o número e tipo de controles que deter a um invasor obtenha acesso a ativos restritos. O modelo descrito neste artigo usa um design de privilégio em camadas e restringe os privilégios administrativos para dispositivos específicos.

## <a name="supply-chain-management"></a>Gerenciamento da cadeia de fornecedores

Essencial para uma estação de trabalho protegida é uma solução de cadeia de fornecimento em que você usa uma estação de trabalho confiável chamada 'raiz de confiança'. Para esta solução usa a raiz de confiança [Autopilot Microsoft](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) tecnologia. Para proteger uma estação de trabalho, piloto automático permite que você utilize dispositivos Microsoft otimização de OEM do Windows 10. Esses dispositivos são fornecidos em um estado bom conhecido do fabricante. Em vez de refazer a imagem de um dispositivo potencialmente não seguros, piloto automático pode transformar um dispositivo do Windows em um estado "pronto para os negócios". Aplica as configurações e políticas, instala aplicativos e até mesmo altera a edição do Windows 10. Por exemplo, piloto automático pode alterar instalação do Windows de um dispositivo do Windows 10 Pro para Windows 10 Enterprise para que ele possa usar recursos avançados.

![Níveis de estação de trabalho segura](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Perfis e as funções do dispositivo

Este guia faz referência a vários perfis de segurança e as funções que podem ajudá-lo a criarem soluções mais seguras para usuários, desenvolvedores e profissionais de TI. Esses perfis equilibrar a usabilidade e riscos comuns que os usuários podem se beneficiar de uma estação de trabalho avançada ou segura. As configurações de configurações fornecidas aqui são baseadas nos padrões da indústria aceito. Este guia mostra como proteger o Windows 10 e reduzir os riscos associados ao comprometimento de dispositivo ou usuário. Ele faz isso usando a política e tecnologia para ajudar a gerenciar riscos e recursos de segurança.
![Níveis de estação de trabalho segura](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Baixa segurança** – uma estação de trabalho padrão, gerenciada fornece um bom ponto de partida para a maioria dos negócios pequenos e domésticos usa. Esses dispositivos são registrados no Azure AD e gerenciados com o Intune. Esse perfil permite que os usuários executem todos os aplicativos e navegue até qualquer site. Como uma solução anti-malware [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) deve ser habilitado.

* **Segurança aprimorada** – essa solução no nível de entrada, protegida, é bom para os usuários domésticos, usuários de pequenas empresas e desenvolvedores gerais.

   A estação de trabalho avançada é uma maneira baseada em política para aumentar a segurança do perfil de baixa segurança. Ele fornece um meio seguro para trabalhar com dados do cliente, além de usar as ferramentas de produtividade, como email e navegação na web. Você pode usar o Intune e políticas de auditoria para monitorar uma estação de trabalho aprimorada para o uso de comportamento e o perfil do usuário. Você implanta o perfil de estação de trabalho avançada com o script do Windows 10 (1809) e ela tira proveito da proteção de malware avançado usando [proteção avançada de ameaças (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Alta segurança** – o meio mais eficaz para reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de self-administer a estação de trabalho. Remover direitos administrativos locais é uma etapa que aprimora a segurança, mas ele pode afetar a produtividade se implementado incorretamente. O perfil de segurança alta baseia-se o perfil de segurança aprimorada com uma alteração considerável: a remoção de administrador local. Este perfil foi criado para usuários de alto perfil: executivos, folha de pagamento e dados confidenciais a usuários, os aprovadores para serviços e processos.

   O usuário de alta segurança exige um ambiente mais controlado enquanto ainda consegue fazer atividades, como email e navegação em uma experiência simples de usar. Os usuários esperam recursos como cookies, Favoritos e outros atalhos para trabalhar. No entanto, esses usuários podem não exigir a capacidade de modificar ou depurar o seu dispositivo. Eles também não precisam instalar os drivers. O perfil de segurança alta é implantado usando a segurança de alta - script do Windows 10 (1809).

* **Especializado** – alvo dos invasores desenvolvedores e administradores de TI porque eles podem alterar sistemas de interesse para os invasores. A estação de trabalho especializada expande as políticas da estação de trabalho de alta segurança ao gerenciar aplicativos locais e limitando os sites. Ele também restringe os recursos de produtividade de alto risco, como Java, ActiveX, plug-ins de navegador e outros controles do Windows. Você implanta esse perfil com DeviceConfiguration_NCSC - script SecurityBaseline Windows10 (1803).

* **Protegido** – um invasor que compromete a uma conta administrativa pode causar danos de negócio significativas por roubo de dados, a alteração de dados ou a interrupção do serviço. Nesse estado protegido, a estação de trabalho permite que todos os controles de segurança e políticas que restringem o controle direto de gerenciamento de aplicativo local. Uma estação de trabalho protegida não tem nenhuma ferramenta de produtividade portanto, o dispositivo mais difíceis de se comprometer. Ele bloqueia o vetor mais comuns de ataques de phishing: email e mídia social.  A estação de trabalho protegida pode ser implantada com a estação de trabalho segura - script SecurityBaseline Windows10 (1809).

   ![Estação de trabalho protegida](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece ao administrador uma com uma estação de trabalho protegida que tem controle de aplicativo não criptografado e a proteção do aplicativo. A estação de trabalho usa o credential guard, o device guard e exploit guard para proteger o host de comportamentos mal-intencionados. Todos os discos locais também são criptografados com BitLocker.

* **Isolado** – esse cenário personalizado, offline representa o final extremo do espectro. Nenhum script de instalação é fornecida para esse caso. Você talvez precise gerenciar uma função críticos de negócios que requer um sistema de operacional herdado sem patch ou sem suporte. Por exemplo, uma linha de produção de alto valor ou um sistema de suporte – vida. Como a segurança é fundamental e os serviços de nuvem não estão disponíveis, você pode gerenciar e atualizar esses computadores manualmente ou com uma arquitetura de floresta do Active Directory isolada, como o Enhanced Security Admin ESAE (ambiente). Nessas circunstâncias, considere a remoção de todo o acesso, exceto as verificações de integridade básicas do Intune e o ATP.

  * [Requisito de comunicações de rede do Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Requisito de comunicações de rede do ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Próximas etapas

[Implantar uma estação de trabalho segura gerenciada pelo Azure](howto-azure-managed-workstation.md).
