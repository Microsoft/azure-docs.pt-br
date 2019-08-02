---
title: Integrar um domínio raiz ou apex para uma porta da frente existente usando o portal do Azure
description: Saiba como integrar um domínio raiz ou apex para uma porta da frente existente usando o portal do Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605788"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Integrar um domínio raiz ou apex na sua frente
Porta da frente do Azure usa registros CNAME para validar a propriedade do domínio para a integração de domínios personalizados. Além disso, a porta da frente não expõe o endereço IP de front-end associado com seu perfil de porta da frente e então você não pode mapear seu domínio apex para um endereço IP, se a intenção é integrá-lo à frente do Azure.

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. Por exemplo, se seu domínio está `contoso.com`; você pode criar registros CNAME para `somelabel.contoso.com`; mas você não pode criar um CNAME para `contoso.com` em si. Essa restrição apresenta um problema para os proprietários do aplicativo que têm aplicativos de balanceamento de carga da frente por trás do Azure. Como usando um perfil de porta da frente requer a criação de um registro CNAME, que não é possível apontar o perfil de porta da frente do apex da zona.

Esse problema é resolvido usando os registros de alias no DNS do Azure. Ao contrário de registros CNAME, registros de alias são criados no ápice da zona e os proprietários do aplicativo podem usá-lo para apontar seu registro apex de zona para um perfil de porta de entrada que tem pontos de extremidade públicos. Os proprietários do aplicativo apontam para o mesmo perfil de porta de entrada que é usado para qualquer outro domínio em sua zona DNS. Por exemplo, `contoso.com` e `www.contoso.com` pode apontar para o mesmo perfil de porta da frente. 

Mapear o domínio de apex ou raiz para seu perfil de porta da frente basicamente requer o nivelamento de CNAME ou DNS perseguindo, que é um mecanismo onde no DNS provedor recursivamente resolve a entrada CNAME até que ele atinja um endereço IP. Essa funcionalidade é suportada pelo DNS do Azure para pontos de extremidade de porta da frente. 

> [!NOTE]
> Há outros provedores de DNS bem o que dão suporte a ajuste de CNAME ou DNS perseguindo, no entanto, porta de entrada do Azure recomenda o uso de DNS do Azure para seus clientes para hospedar seus domínios.

Você pode usar o portal do Azure para integrar um domínio apex na sua frente e habilitar o HTTPS nela, associando-a um certificado para terminação SSL. Domínios de Apex também são conhecidos como root ou domínios naked.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um registro de alias que aponta para o seu perfil de porta da frente
> * Adicionar o domínio raiz para a frente
> * Configurar o HTTPS no domínio raiz

> [!NOTE]
> Este tutorial requer que você já tenha criado um perfil de porta da frente. Consulte outros tutoriais como [guia de início rápido: Criar uma porta da frente](./quickstart-create-front-door.md) ou [criar uma porta da frente com HTTP para redirecionamento HTTPS](./front-door-how-to-redirect-https.md) para começar a usar.

## <a name="create-an-alias-record-for-zone-apex"></a>Criar um registro de alias para o apex de zona

1. Abra **DNS do Azure** configuração para o domínio a serem integradas.
2. Crie ou edite o registro para o apex da zona.
3. Selecione o registro **tipo** como _um_ registrar e, em seguida, selecione _Sim_ para **conjunto de registros de Alias**. **Tipo de alias** deve ser definido como _recursos do Azure_.
4. Escolha a assinatura do Azure onde seu perfil de porta de entrada é hospedado e, em seguida, selecione o recurso de porta da frente do **recursos do Azure** lista suspensa.
5. Clique em **Okey** para enviar suas alterações.

    ![Registro de alias para o apex de zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. A etapa acima criará um registro apex de zona que aponta para o recurso de porta da frente e também um mapeamento de registro CNAME de afdverify (exemplo - `afdverify.contosonews.com`) para `afdverify.<name>.azurefd.net` que será usado para a integração do domínio em seu perfil de porta da frente.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Integrar o domínio personalizado na sua frente

1. Na guia designer de porta da frente, clique em '+' ícone nos hosts do front-end da seção para adicionar um novo domínio personalizado.
2. Insira o nome de domínio raiz ou apex no campo de nome de host personalizado, exemplo `contosonews.com`.
3. Depois que o mapeamento de CNAME do domínio à sua frente for validado, clique em **adicionar** para adicionar o domínio personalizado.
4. Clique em **salvar** para enviar as alterações.

![Menu de domínio personalizado](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Habilitar HTTPS em seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e, na seção **HTTPS de domínio personalizado**, altere o status para **habilitado**.
2. Selecione o **tipo de gerenciamento de certificado** à _'Usar meu próprio certificado'_ .

> [!WARNING]
> Tipo de gerenciamento de certificado gerenciado de porta front-não há suporte atualmente para domínios apex ou raiz. A única opção disponível para habilitar o HTTPS em um domínio raiz ou apex de porta da frente é usando seu próprio certificado SSL personalizado hospedado no Azure Key Vault.

3. Certifique-se de que você configurou as permissões corretas para a porta da frente acessar a sua chave de cofre, conforme observado na interface do usuário, antes de prosseguir para a próxima etapa.
4. Escolha uma **conta do Key Vault** de sua assinatura atual e, em seguida, selecione o apropriado **segredo** e **versão do segredo** para mapear para o certificado correto.
5. Clique em **atualização** para salvar a seleção e, em seguida, clique em **salvar**.
6. Clique em **Refresh** depois de alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso de provisionamento de certificado. 

> [!WARNING]
> Certifique-se de que você criou regras de roteamentos apropriadas para seu domínio apex ou adicionado o domínio para as regras de roteamento existentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).