---
title: "Sincronização do Azure AD Connect: conceitos técnicos | Microsoft Docs"
description: "Explica os conceitos técnicos da sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi;andkjell
ms.openlocfilehash: 6cf8debc6443bb60fc5f601ea4aa392eb2f13a8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: conceitos técnicos
Este artigo é um resumo do tópico [Entender a arquitetura](active-directory-aadconnectsync-technical-concepts.md).

O Azure AD Connect Sync tem como base uma plataforma sólida de sincronização de metadiretório.
As seções a seguir apresentam os conceitos de sincronização de metadiretório.
Complementando MIIS, ILM e FIM, os Serviços de Sincronização do Active Directory do Azure fornecem a próxima plataforma para conexão a fontes de dados, realizando a sincronização de dados entre diferentes fontes e o provisionamento e desprovisionamento de identidades.

![Conceitos técnicos](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

As seções a seguir fornecem mais detalhes sobre os seguintes aspectos do Serviço de Sincronização FIM:

* Conector
* Fluxo de atributos
* Espaço conector
* Metaverso
* Provisionamento

## <a name="connector"></a>Conector
Os módulos de código que são usados para comunicação com um diretório conectado são chamados de conectores (anteriormente conhecidos como MAs [agentes de gerenciamento]).

Eles são instalados no computador que executa a sincronização do Azure AD Connect. Os conectores fornecem a capacidade de conversar sem agentes usando protocolos de sistema remoto, em vez de depender da implantação de agentes especializados. Isso significa que o risco e tempos de implantação reduzidos, especialmente ao lidar com sistemas e aplicativos críticos.

Na figura acima, o conector é sinônimo de espaço conector, mas abrange toda a comunicação com o sistema externo.

O conector é responsável por todas as funcionalidades de importação e exportação no sistema e libera os desenvolvedores da necessidade de compreender como conectar a cada sistema nativamente ao usar provisionamento declarativo para personalizar as transformações de dados.

Importações e exportações ocorrem apenas quando agendadas, permitindo mais isolamento de alterações que ocorrem no sistema, desde que as alterações não sejam propagadas automaticamente para a fonte de dados conectada. Além disso, os desenvolvedores também podem criar seus próprios conectores para conectar a praticamente qualquer fonte de dados.

## <a name="attribute-flow"></a>Fluxo de atributos
O metaverso é a exibição consolidada de todas as identidades associadas de espaços conectores vizinhos. Na figura acima, o fluxo de atributos é representado por linhas com setas para o fluxo de entrada e saída. O fluxo de atributo é o processo de cópia ou transformação de dados de um sistema para outro, e todos os fluxos de atributos (entradas ou saídas).

O fluxo de atributos ocorre bidirecionalmente entre o metaverso e o espaço conector, quando as operações de sincronização (completa ou delta) estão agendadas para execução.

O fluxo de atributos ocorre apenas quando essas sincronizações são executadas. Fluxos de atributos são definidos nas Regras de Sincronização. Eles podem ser entrada (ISR na imagem anterior) ou saída (OSR na figura acima).

## <a name="connected-system"></a>Sistema conectado
Sistema conectado (também conhecido como diretório conectado) refere-se ao sistema remoto ao qual o Azure AD Connect sync se conectou e do qual está lendo e gravando dados de identidade.

## <a name="connector-space"></a>Espaço conector
Cada fonte de dados conectada é representada como um subconjunto filtrado dos objetos e atributos no espaço conector.
Isso permite que o serviço de sincronização funcione localmente sem a necessidade de entrar em contato com o sistema remoto ao sincronizar os objetos; além disso, restringe a interação a apenas importações e exportações.

Quando a fonte de dados e o conector têm a capacidade de fornecer uma lista de alterações (uma importação de delta); em seguida, a eficiência operacional aumenta drasticamente, já que apenas as alterações desde o último ciclo de sondagem são trocadas. O espaço conector isola a fonte de dados conectada de propagação automática de alterações, ao exigir que o conector agende importações e exportações. Essa garantia adicional lhe dá tranquilidade ao testar, visualizar ou confirmar a próxima atualização.

## <a name="metaverse"></a>Metaverso
O metaverso é a exibição consolidada de todas as identidades associadas de espaços conectores vizinhos.

Conforme as identidades são vinculadas uma à outra e atribui-se autoridade a vários atributos por meio de mapeamentos de fluxo de importação, o objeto metaverso central começa a agregar informações de vários sistemas. Desse fluxo de atributos de objeto, mapeamentos transportam informações para sistemas de saída.

Objetos são criados quando um sistema autoritativo projeta-os no metaverso. Assim que todas as conexões são removidas, o objeto do metaverso é excluído.

Objetos no metaverso não podem ser editados diretamente. Todos os dados do objeto devem ter contribuído por meio do fluxo de atributos. O metaverso mantém conectores persistentes com cada espaço de conector. Esses conectores não exigem reavaliação para cada execução de sincronização. Isso significa que a sincronização do Azure Connect AD não tem que localizar o objeto remoto correspondente a cada vez. Isso evita a necessidade de agentes caros para evitar alterações em atributos que normalmente seria responsável por correlacionar os objetos.

Ao descobrir novas fontes de dados que podem ter objetos preexistentes que precisam ser gerenciados, o Azure AD Connect usa um processo chamado regra de junção para avaliar os candidatos em potencial com os quais deseja estabelecer um vínculo.
Quando o vínculo é estabelecido, essa avaliação não ocorre novamente e o fluxo de atributos normal pode ocorrer entre a fonte de dados conectada remota e o metaverso.

## <a name="provisioning"></a>Provisionamento
Quando uma fonte autoritativa projeta um novo objeto no metaverso, um novo objeto de espaço conector pode ser criado em outro conector, representando uma fonte de dados conectada downstream.

Inerentemente, isso estabelece um vínculo e o fluxo de atributo pode prosseguir bidirecionalmente.

Sempre que uma regra determina que um novo objeto de espaço conector precisa ser criado, ele é chamado de provisionamento. No entanto, como essa operação só ocorre dentro do espaço conector, ele não se transfere para a fonte de dados conectada até que uma exportação seja executada.

## <a name="additional-resources"></a>Recursos adicionais
* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
