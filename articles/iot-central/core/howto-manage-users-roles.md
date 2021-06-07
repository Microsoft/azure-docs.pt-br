---
title: Gerenciar usuários e funções no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar usuários e funções no aplicativo IoT Central do Azure
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: f6c45b8d9804f16c4e59d259f562cc03f187e6a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92122970"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gerenciar usuários e funções em seu aplicativo IoT Central

Este artigo descreve como, como administrador, você pode adicionar, editar e excluir usuários em seu aplicativo de IoT Central do Azure. O artigo também descreve como gerenciar funções no aplicativo IoT Central do Azure.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo de IoT Central do Azure, você será adicionado automaticamente à função de **administrador** para esse aplicativo.

## <a name="add-users"></a>Adicionar usuários

Cada usuário deve ter uma conta de usuário antes de poder entrar e acessar um aplicativo Azure IoT Central. Contas da Microsoft e contas de Azure Active Directory têm suporte no IoT Central do Azure. Atualmente, os grupos do Azure Active Directory não têm suporte no Azure IoT Central.

Para saber mais, confira a [Ajuda da Conta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e o [Início Rápido: Adicionar novos usuários ao Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Para adicionar um usuário a um aplicativo IoT Central, acesse a página **Usuários** na seção **Administração**.
    
    > [!div class="mx-imgBorder"]
    >![Gerenciar usuários](media/howto-manage-users-roles/manage-users-pnp.png)

1. Na página **Usuários**, escolha **+ Adicionar usuário** para adicionar um usuário.

1. Escolha uma função para o usuário na lista suspensa **Função**. Saiba mais sobre as funções na seção [Gerenciar funções](#manage-roles) deste artigo.

    > [!div class="mx-imgBorder"]
    >![Adicionar usuário e selecionar uma função](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Um usuário que está em uma função personalizada que concede a eles a permissão para adicionar outros usuários só pode adicionar usuários a uma função com as mesmas ou menos permissões do que sua própria função.

Se uma ID de usuário IoT Central for excluída do Azure Active Directory e, em seguida, readicionada, o usuário não poderá entrar no aplicativo IoT Central. Para reabilitar o acesso, o administrador do IoT Central deve excluir e ler o usuário no aplicativo.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não podem ser alteradas depois que são atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e adicione-o novamente com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas para IoT Central aplicativo e não podem ser gerenciadas no portal do Azure.

## <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **Usuários**. Em seguida, selecione **Excluir**.

## <a name="manage-roles"></a>Gerenciar funções

As funções permitem que você controle quem dentro de sua organização tem permissão para realizar várias tarefas no IoT Central. Há três funções internas que você pode atribuir a usuários do seu aplicativo. Você também pode [criar funções personalizadas](#create-a-custom-role) se precisar de um controle mais refinado.

> [!div class="mx-imgBorder"]
> ![Seleção de gerenciar funções](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrador

Os usuários na função **administrador** podem gerenciar e controlar todas as partes do aplicativo, incluindo a cobrança.

O usuário que cria um aplicativo é automaticamente designado à função **Administrador**. Sempre deverá haver pelo menos um usuário na função **Administrador**.

### <a name="builder"></a>Construtor

Os usuários na função de **Construtor** podem gerenciar todas as partes do aplicativo, mas não podem fazer alterações nas guias administração ou exportação de dados contínuas.

### <a name="operator"></a>Operador

Os usuários na função **operador** podem monitorar a integridade e o status do dispositivo. Eles não podem fazer alterações nos modelos de dispositivo ou administrar o aplicativo. Os operadores podem adicionar e excluir dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. 

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Se sua solução exigir controles de acesso mais refinados, você poderá criar funções personalizadas com conjuntos personalizados de permissões. Para criar uma função personalizada, navegue até a página **funções** na seção **Administração** do seu aplicativo. Em seguida, selecione **+ nova função** e adicione um nome e uma descrição para sua função. Selecione as permissões que sua função requer e, em seguida, selecione **salvar**.

Você pode adicionar usuários à sua função personalizada da mesma maneira que adiciona usuários a uma função interna.

> [!div class="mx-imgBorder"]
> ![Criar uma função personalizada](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opções de função personalizadas

Ao definir uma função personalizada, você escolhe o conjunto de permissões que um usuário recebe se ele for um membro da função. Algumas permissões dependem de outras. Por exemplo, se você adicionar a permissão **Atualizar painéis do aplicativo** a uma função, a permissão **Exibir painéis do aplicativo** será adicionada automaticamente. As tabelas a seguir resumem as permissões disponíveis e suas dependências, que você pode usar ao criar funções personalizadas.

#### <a name="managing-devices"></a>Gerenciando dispositivos

**Permissões de modelo de dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Gerenciar | Visualizar <br/> Outras dependências: exibir instâncias de dispositivo  |
| Controle total | Exibir, gerenciar <br/> Outras dependências: exibir instâncias de dispositivo |

**Permissões de instância de dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos |
| Atualizar | Visualizar <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Criar | Visualizar <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Excluir | Visualizar <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Executar comandos | Atualizar, exibir <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Controle total | Exibir, atualizar, criar, excluir, executar comandos <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |

**Permissões de grupos de dispositivos**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo |
| Atualizar | Visualizar <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo   |
| Criar | Exibir, atualizar <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo   |
| Excluir | Visualizar <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo   |
| Controle total | Exibir, atualizar, criar, excluir <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo |

**Permissões de gerenciamento de conectividade do dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Ler instância | Nenhum <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, instâncias de dispositivo |
| Gerenciar instância | Nenhum |
| Ler global | Nenhum   |
| Gerenciar global | Ler global |
| Controle total | Ler instância, gerenciar instância, ler global, gerenciar global. <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, instâncias de dispositivo |

**Permissões de trabalhos**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Atualizar | Visualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Criar | Exibir, atualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Excluir | Visualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Execute (executar) | Visualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos; Atualizar instâncias de dispositivo; Executar comandos em instâncias de dispositivo |
| Controle total | Exibir, atualizar, criar, excluir, executar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos; Atualizar instâncias de dispositivo; Executar comandos em instâncias de dispositivo |

**Permissões de regras**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: exibir modelos de dispositivo |
| Atualizar | Visualizar <br/> Outras dependências: exibir modelos de dispositivo |
| Criar | Exibir, atualizar <br/> Outras dependências: exibir modelos de dispositivo |
| Excluir | Visualizar <br/> Outras dependências: exibir modelos de dispositivo |
| Controle total | Exibir, atualizar, criar, excluir <br/> Outras dependências: exibir modelos de dispositivo |

#### <a name="managing-the-app"></a>Gerenciando o aplicativo

**Permissões de configurações do aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Copiar | Visualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo, grupos de dispositivos, painéis, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |
| Excluir | Visualizar   |
| Controle total | Exibir, atualizar, copiar, excluir <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, painéis de aplicativos, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |

**Permissões de exportação de modelo de aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Exportação | Visualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo, grupos de dispositivos, painéis, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |
| Controle total | Exibir, exportar <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, painéis de aplicativos, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |

**Permissões de cobrança**

| Nome | Dependências |
| ---- | -------- |
| Gerenciar | Nenhum     |
| Controle total | Gerenciar |

#### <a name="managing-users-and-roles"></a>Gerenciando usuários e funções

**Permissões de funções personalizadas**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum |
| Atualizar | Visualizar |
| Criar | Exibir, atualizar |
| Excluir | Visualizar |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de gerenciamento do usuário**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: exibir funções personalizadas |
| Adicionar | Visualizar <br/> Outras dependências: exibir funções personalizadas |
| Excluir | Visualizar <br/> Outras dependências: exibir funções personalizadas |
| Controle total | Exibir, adicionar, excluir <br/> Outras dependências: exibir funções personalizadas |

> [!NOTE]
> Um usuário que está em uma função personalizada que concede a eles a permissão para adicionar outros usuários só pode adicionar usuários a uma função com as mesmas ou menos permissões do que sua própria função.

#### <a name="customizing-the-app"></a>Personalizando o aplicativo

**Permissões do painel de aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Criar | Exibir, atualizar |
| Excluir | Visualizar   |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de painéis pessoais**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Criar | Exibir, atualizar   |
| Excluir | Visualizar   |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de identidade visual, favicon e cores**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Controle total | Exibir, atualizar |

**Permissões de links de ajuda**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Controle total | Exibir, atualizar |

#### <a name="extending-the-app"></a>Estendendo o aplicativo

**Permissões de exportação de dados**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Criar | Exibir, atualizar  |
| Excluir | Visualizar   |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de token de API**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Criar | Visualizar   |
| Excluir | Visualizar   |
| Controle total | Exibir, criar, excluir |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como gerenciar usuários e funções em seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar sua fatura](howto-view-bill.md).