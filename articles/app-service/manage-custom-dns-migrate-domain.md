---
title: Migrar um nome DNS ativo
description: Saiba como migrar um nome de domínio DNS personalizado que já está atribuído a um site ativo ao Serviço de Aplicativo do Azure sem nenhum tempo de inatividade.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89484284"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure

Este artigo mostra como migrar um nome DNS ativo para o [Serviço de Aplicativo do Azure](../app-service/overview.md) sem nenhum tempo de inatividade.

Quando você migra um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo, o nome DNS já está atendendo ao tráfego em tempo real. Evite o tempo de inatividade na resolução do DNS durante a migração associando o nome DNS ativo preventivamente ao aplicativo Serviço de Aplicativo.

Caso não esteja preocupado com o tempo de inatividade na resolução do DNS, consulte [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estas instruções:

- [Verifique se o aplicativo Serviço de Aplicativo não está na camada GRATUITA](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Vincular o nome de domínio preventivamente

Ao associar um domínio personalizado de forma preventiva, você realiza os dois itens a seguir antes de fazer qualquer alteração nos registros DNS existentes:

- Verificar a propriedade de domínio
- Habilitar o nome de domínio para seu aplicativo

Quando você finalmente migrar o nome DNS personalizado do site antigo para o aplicativo Serviço de Aplicativo, não haverá nenhum tempo de inatividade na resolução do DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Obter a ID de verificação de domínio

Obtenha a ID de verificação de domínio para seu aplicativo seguindo as etapas em [obter ID de verificação de domínio](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).

### <a name="create-domain-verification-record"></a>Criar registro de verificação de domínio

Para verificar a propriedade do domínio, adicione um registro TXT para verificação de domínio. O nome do host para o registro TXT depende do tipo de tipo de registro DNS que você deseja mapear. Consulte a tabela a seguir ( `@` normalmente representa o domínio raiz):

| Exemplo de registro DNS | Host TXT | Valor TXT |
| - | - | - |
| \@ (raiz) | _asuid_ | [ID de verificação de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (sub) | _asuid. www_ | [ID de verificação de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* (curinga) | _asuid_ | [ID de verificação de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

Na página de registros DNS, anote o tipo de registro do nome DNS que você deseja migrar. O Serviço de Aplicativo dá suporte a mapeamentos de CNAME e registros A.

> [!NOTE]
> Curinga `*` registros não será validado subdomínios com o registro do CNAME existente da lista. Talvez você precise criar explicitamente um registro TXT para cada subdomínio.

### <a name="enable-the-domain-for-your-app"></a>Habilitar o domínio para o aplicativo

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda da página do aplicativo, selecione **Domínios personalizados**. 

    ![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na página **domínios personalizados** , selecione **Adicionar domínio personalizado**.

    ![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o nome de domínio totalmente qualificado que você deseja migrar, que corresponde ao registro TXT que você criar, como, `contoso.com` `www.contoso.com` ou `*.contoso.com` . Selecione **Validar**.

    O botão **Adicionar domínio personalizado** está ativado. 

1. Verifique se **Tipo de registro do nome do host** está definido como o tipo de registro DNS que você deseja migrar. Selecione **Adicionar nome do host**.

    ![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Pode levar algum tempo para que o novo nome do host seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

    ![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    Agora, o nome DNS personalizado está habilitado no Azure App. 

## <a name="remap-the-active-dns-name"></a>Remapear o nome DNS ativo

Agora só resta remapear o registro DNS ativo para que ele aponte para o Serviço de Aplicativo. No momento, ele ainda aponta para o site antigo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copiar o endereço IP do aplicativo (somente o registro A)

Se estiver fazendo o remapeamento de um registro CNAME, ignore esta seção. 

Para remapear um registro A, você precisa do endereço IP externo do aplicativo Serviço de Aplicativo, que é mostrado na página **Domínios personalizados**.

Na página **domínios personalizados**, copie o endereço IP do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualizar o registro DNS

Novamente na página de registros DNS do provedor de domínio, selecione o registro DNS a ser remapeado.

Para o exemplo de domínio raiz `contoso.com`, remapeie o registro A ou CNAME como os exemplos na seguinte tabela: 

| Exemplo de FQDN | Tipo de registro | Host | Valor |
| - | - | - | - |
| contoso.com (raiz) | Um | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
| \.contoso.com www (sub) | CNAME | `www` | _&lt;AppName>. azurewebsites.net_ |
| \*.contoso.com (curinga) | CNAME | _\*_ | _&lt;AppName>. azurewebsites.net_ |

Salve suas configurações.

As consultas DNS devem começar a serem resolvidas para o aplicativo Serviço de Aplicativo imediatamente após a propagação do DNS.

## <a name="migrate-domain-from-another-app"></a>Migrar domínio de outro aplicativo

Você pode migrar um domínio personalizado ativo no Azure, entre assinaturas ou dentro da mesma assinatura. No entanto, essa migração sem tempo de inatividade requer que o aplicativo de origem e o aplicativo de destino recebam o mesmo domínio personalizado em um determinado momento. Portanto, você precisa certificar-se de que os dois aplicativos não sejam implantados na mesma unidade de implantação (internamente conhecida como espaço Web). Um nome de domínio pode ser atribuído a apenas um aplicativo em cada unidade de implantação.

Você pode encontrar a unidade de implantação para seu aplicativo examinando o nome de domínio da URL do FTP/S `<deployment-unit>.ftp.azurewebsites.windows.net` . Verifique e certifique-se de que a unidade de implantação seja diferente entre o aplicativo de origem e o aplicativo de destino. A unidade de implantação de um aplicativo é determinada pelo [plano do serviço de aplicativo](overview-hosting-plans.md) em que ele está. Ele é selecionado aleatoriamente pelo Azure quando você cria o plano e não pode ser alterado. O Azure só garante que dois planos estejam na mesma unidade de implantação quando você [os cria no mesmo grupo de recursos *e* na mesma região](app-service-plan-manage.md#create-an-app-service-plan), mas não tem nenhuma lógica para garantir que os planos estejam em unidades de implantação diferentes. A única maneira de criar um plano em uma unidade de implantação diferente é continuar criando um plano em um novo grupo de recursos ou região até obter uma unidade de implantação diferente.

## <a name="next-steps"></a>Próximas etapas

Saiba como associar um certificado TLS/SSL personalizado ao serviço de aplicativo.

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação TLS no serviço Azure App](configure-ssl-bindings.md)
