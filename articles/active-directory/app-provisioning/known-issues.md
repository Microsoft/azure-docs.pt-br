---
title: Problemas conhecidos do provisionamento de aplicativos no Azure AD
description: Saiba mais sobre problemas conhecidos ao trabalhar com o provisionamento automatizado de aplicativos no Azure AD.
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256636"
---
# <a name="known-issues-application-provisioning"></a>Problemas conhecidos: provisionamento de aplicativos
Problemas conhecidos aos quais você deve estar atento quando trabalhar com o provisionamento de aplicativos. Você pode fornecer comentários sobre o serviço de provisionamento de aplicativos no UserVoice, confira [UserVoice de provisionamento de aplicativos do Azure ad](https://aka.ms/appprovisioningfeaturerequest). Nós observamos com atenção o UserVoice para que possamos melhorar o serviço. 

> [!NOTE]
> Essa não é uma lista abrangente de problemas conhecidos. Se você souber de um problema que não está listado, forneça comentários na parte inferior da página.

## <a name="authorization"></a>Autorização 

**Não é possível salvar após o teste de conexão bem-sucedido**

Se você puder testar uma conexão com êxito, mas não puder salvar, você excedeu o limite de armazenamento permitido para as credenciais. Para saber mais, confira [problema ao salvar credenciais de administrador](./user-provisioning.md).

**Não é possível salvar**

A URL do locatário, o token secreto e o email de notificação devem ser preenchidos para salvar. Você não pode fornecer apenas um deles. 

**Não é possível alterar o modo de provisionamento de volta para manual**

Depois de configurar o provisionamento pela primeira vez, você observará que o modo de provisionamento mudou de manual para automático. Você não pode alterá-lo de volta para manual. Mas você pode desativar o provisionamento por meio da interface do usuário. Desativar o provisionamento na interface do usuário efetivamente faz o mesmo que definir a lista suspensa como manual.  


## <a name="attribute-mappings"></a>Mapeamentos de atributo 

**O atributo SamAccountName ou UserType não está disponível como um atributo de origem**

Os atributos SamAccountName e UserType não estão disponíveis como um atributo de origem por padrão. Estenda seu esquema para adicionar o atributo. Você pode adicionar os atributos à lista de atributos de origem disponíveis estendendo seu esquema. Para saber mais, veja [atributo de origem ausente](user-provisioning-sync-attributes-for-mapping.md). 

**Lista suspensa de atributo de origem ausente para extensão de esquema**

Às vezes, as extensões para seu esquema podem estar ausentes na lista suspensa atributo de origem na interface do usuário. Vá para as configurações avançadas de seus mapeamentos de atributo e adicione manualmente os atributos. Para saber mais, confira [Personalizar mapeamentos de atributo](customize-application-attributes.md).

**Atributo nulo não pode ser provisionado**

Atualmente, o Azure AD não pode provisionar atributos nulos. Se um atributo for nulo no objeto de usuário, ele será ignorado. 

**Máximo de caracteres para expressões de mapeamento de atributo**

As expressões de mapeamento de atributo podem ter um máximo de 10.000 caracteres. 

**Filtros de escopo sem suporte**

Não há suporte para extensões de diretório, appRoleAssignments, UserType e accountExpires como filtros de escopo.


## <a name="service-issues"></a>Problemas de serviço 

**Cenários sem suporte**

- Não há suporte para o provisionamento de senhas. 
- Não há suporte para o provisionamento de grupos aninhados. 
- O provisionamento para locatários B2C não tem suporte devido ao tamanho dos locatários.
- Nem todos os aplicativos de provisionamento estão disponíveis em todas as nuvens. Por exemplo, Atlassian ainda não está disponível na nuvem governamental. Estamos trabalhando com desenvolvedores de aplicativos para integrar seus aplicativos a todas as nuvens.

**O provisionamento automático não está disponível no meu aplicativo baseado em OIDC**

Se você criar um registro de aplicativo, a entidade de serviço correspondente nos aplicativos empresariais não será habilitada para o provisionamento automático de usuário. Você precisará solicitar que o aplicativo seja adicionado à galeria, se destinado para uso por várias organizações, ou criar um segundo aplicativo que não seja da galeria para provisionamento. 

**O intervalo de provisionamento é fixo**

O [tempo](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) entre os ciclos de provisionamento não é configurável no momento. 

**Alterações que não se movem do aplicativo de destino para o Azure AD**

O serviço de provisionamento de aplicativos não está ciente das alterações feitas em aplicativos externos. Portanto, nenhuma ação é executada para reverter. O serviço de provisionamento de aplicativo depende das alterações feitas no Azure AD. 

**A alternância de sincronizar tudo para a sincronização atribuída não está funcionando**

Depois de alterar o escopo de ' sincronizar tudo ' para ' sincronizar atribuído ', certifique-se também de executar uma reinicialização para garantir que a alteração entra em vigor. Você pode fazer a reinicialização da interface do usuário.

**O ciclo de provisionamento continua até a conclusão**

Ao definir `enabled = off` o provisionamento ou ao pressionar parar, o ciclo de provisionamento atual continuará em execução até a conclusão. O serviço deixará de executar qualquer ciclo futuro até você ativar o provisionamento novamente.

**Membro do grupo não provisionado**

Quando um grupo estiver no escopo e um membro estiver fora do escopo, o grupo será provisionado. O usuário fora do escopo não será provisionado. Se o membro voltar ao escopo, o serviço não detectará imediatamente a alteração. Reiniciar o provisionamento abordará o problema. É recomendável reiniciar o serviço periodicamente para garantir que todos os usuários sejam provisionados corretamente.  

**O Gerenciador não está provisionado**

Se um usuário e seu gerente estiverem no escopo do provisionamento, o serviço provisionará o usuário e, em seguida, atualizará o Gerenciador. No entanto, se no dia um usuário estiver no escopo e o Gerenciador estiver fora do escopo, provisionaremos o usuário sem a referência do Gerenciador. Quando o gerente chega ao escopo, a referência do Gerenciador não será atualizada até que você reinicie o provisionamento e faça com que o serviço reavalie todos os usuários novamente. 

## <a name="next-steps"></a>Próximas etapas
- [Como funciona o provisionamento](how-provisioning-works.md)
