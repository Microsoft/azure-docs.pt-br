---
title: Tempo de vida do objeto e do recurso
description: Explica o gerenciamento de tempo de vida para tipos diferentes
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593884"
---
# <a name="object-and-resource-lifetime"></a>Tempo de vida do objeto e do recurso

A renderização remota do Azure distingue entre dois tipos: **objetos** e **recursos**.

## <a name="object-lifetime"></a>Tempo de vida do objeto

Os *objetos* são considerados coisas que o usuário pode criar, modificar e destruir a seu próprio critério. Os objetos podem ser duplicados livremente e cada instância pode ser modificada ao longo do tempo. Consequentemente, as [entidades](entities.md) e os [componentes](components.md) são objetos.

O tempo de vida de objetos está totalmente sob controle de usuário. No entanto, ele não está relacionado ao tempo de vida da representação do lado do cliente. Classes como `Entity` e `Component` têm uma `Destroy` função que deve ser chamada para desalocar o objeto no host de renderização remoto. Além disso, `Entity.Destroy()` o destruirá a entidade, seus filhos e todos os componentes nessa hierarquia.

## <a name="resource-lifetime"></a>Tempo de vida do recurso

Os *recursos* são as coisas cujo tempo de vida é totalmente gerenciado pelo host de renderização remoto. Os recursos são uma referência contada internamente. Eles são desalocados quando ninguém os referenciam mais.

A maioria dos recursos só pode ser criada indiretamente, normalmente carregando-os de um arquivo. Quando o mesmo arquivo é carregado várias vezes, a renderização remota do Azure retornará a mesma referência e não carregará os dados novamente.

Muitos recursos são imutáveis, por [malhas](meshes.md) e [texturas](textures.md)de instância. No entanto, alguns recursos são mutáveis, por exemplo, [materiais](materials.md). Como os recursos geralmente são compartilhados, a modificação de um recurso pode afetar vários objetos. Por exemplo, alterar a cor de um material alterará a cor de todos os objetos que usam malhas, que por sua vez fazem referência a esse material.

### <a name="built-in-resources"></a>Recursos internos

A renderização remota do Azure contém alguns recursos internos, que podem ser carregados, predependendo do respectivo identificador com `builtin://` o durante a chamada para `RenderingSession.Connection.LoadXYZAsync()` . Os recursos internos disponíveis estão listados na documentação para cada recurso respectivo. Por exemplo, o [capítulo céu](../overview/features/sky.md) lista as texturas do céu interno.

## <a name="general-lifetime"></a>Tempo de vida geral

O tempo de vida de todos os objetos e recursos está associado à conexão. Na desconexão, tudo é Descartado. Ao reconectar-se à mesma sessão, o grafo de cena estará vazio e todos os recursos serão limpos.

Na prática, carregar o mesmo recurso em uma sessão, após uma desconexão, é geralmente mais rápido do que a primeira vez. Esse é o caso porque a maioria dos recursos deve ser baixada do armazenamento do Azure pela primeira vez, o que não é necessário na segunda vez, economizando um tempo considerável.

## <a name="next-steps"></a>Próximas etapas

* [Entidades](entities.md)
* [Componentes](components.md)
* [Modelos](models.md)
