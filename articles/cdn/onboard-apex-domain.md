---
title: Carregar um domínio raiz ou Apex para um ponto de extremidade da CDN do Azure existente-portal do Azure
description: Saiba como carregar um domínio raiz ou Apex para um ponto de extremidade da CDN do Azure existente usando o portal do Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94369968"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Carregar um domínio raiz ou Apex para um ponto de extremidade da CDN do Azure existente

A CDN do Azure usa registros CNAME para validar a propriedade do domínio para a integração de domínios personalizados. A CDN não expõe o endereço IP de front-end associado ao seu perfil CDN. Você não poderá mapear seu domínio Apex para um endereço IP se sua intenção for integrar-o à CDN do Azure.

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. Por exemplo, se seu domínio for `contoso.com` ; você pode criar registros CNAME para o `somelabel.contoso.com` ; mas não pode criar o CNAME para `contoso.com` si mesmo. Essa restrição apresenta um problema para proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás da CDN do Azure. Como o uso de um perfil CDN requer a criação de um registro CNAME, não é possível apontar para o perfil CDN do Apex da zona.

Esse problema pode ser resolvido usando registros de alias no DNS do Azure. Ao contrário dos registros CNAME, os registros de alias são criados no Apex da zona. Os proprietários de aplicativos podem usá-lo para apontar o registro de Apex de zona para um perfil CDN que tenha pontos de extremidade públicos. Os proprietários de aplicativo apontam para o mesmo perfil de CDN que é usado para qualquer outro domínio dentro de sua zona DNS. Por exemplo, `contoso.com` e `www.contoso.com` pode apontar para o mesmo perfil CDN. 

O mapeamento de seu Apex ou domínio raiz para o seu perfil CDN requer o nivelamento CNAME ou a caça do DNS. Um mecanismo no qual o provedor DNS resolve recursivamente a entrada CNAME até atingir um endereço IP. Essa funcionalidade é suportada pelo DNS do Azure para pontos de extremidade CDN. 

> [!NOTE]
> Também há outros provedores DNS que dão suporte ao nivelamento CNAME ou à procura de DNS. no entanto, a CDN do Azure recomenda usar o DNS do Azure para seus clientes para hospedar seus domínios.

Você pode usar a portal do Azure para carregar um domínio de Apex em sua CDN e habilitar o HTTPS nele, associando-o a um certificado para terminação de TLS. Os domínios Apex também são chamados de domínios raiz ou Naked.

## <a name="create-an-alias-record-for-zone-apex"></a>Criar um registro de alias para o Apex da zona

1. Abra a configuração de **DNS do Azure** para o domínio a ser integrado.

2. Selecione **+ Conjunto de registros**.

3. Em **Adicionar conjunto de registros**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ------|
    | Nome | Insira **@** . |
    | Type | Selecione **um**. |
    | Conjunto de registros de alias | Selecione **Sim**. |
    | Tipo de alias | Clique em **Recurso do Azure**. |
    | Escolha uma assinatura | Selecione sua assinatura. |
    | Recursos do Azure | Selecione seu ponto de extremidade CDN. |

4. Insira seu valor para **TTL**.

5. Selecione **OK** para enviar suas alterações.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Registro de alias para o Apex da zona":::

6. A etapa acima criará um registro de Apex de zona apontando para o recurso da CDN. Um **cdnverify** de mapeamento de registro CNAME é usado para integração do domínio em seu perfil CDN.
    1. Exemplo, **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Integrar o domínio personalizado no seu CDN

Depois de registrar seu domínio personalizado, adicione-o ao ponto de extremidade da CDN. 

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o perfil CDN contendo o ponto de extremidade que você deseja mapear para um domínio personalizado.
    
2. Na página **Perfil CDN**, selecione o ponto de extremidade da CDN a ser associado ao domínio personalizado.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Seleção do ponto de extremidade CDN" border="true":::
    
3. Selecione **+Domínio personalizado**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Botão Adicionar domínio personalizado" border="true":::

4. Em **Adicionar um domínio personalizado**, o **Nome do host do ponto de extremidade** é preenchido previamente e será derivado da URL do ponto de extremidade CDN: **\<endpoint-hostname>** .azureedge.net. Ele não pode ser alterado.

5. Para **nome de host personalizado**, insira seu domínio raiz personalizado ou Apex para usar como o domínio de origem do seu registro CNAME. 
    1. Por exemplo, **contoso.com**. **Não use o nome do subdomínio cdnverify**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Adicionar domínio personalizado" border="true":::

6. Selecione **Adicionar**.

   O Azure verifica se o registro CNAME existe para o nome de domínio personalizado digitado. Se o CNAME estiver correto, seu domínio personalizado será validado. 

   Pode levar algum tempo para que as novas configurações de domínio personalizado sejam propagadas para todos os nós de borda da CDN: 
    - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
    - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
    - Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos.   

## <a name="enable-https-on-your-custom-domain"></a>Habilitar HTTPS em seu domínio personalizado

Para obter mais informações sobre como habilitar HTTPS em seu domínio personalizado para a CDN do Azure, consulte [tutorial: configurar HTTPS em um domínio personalizado da CDN do Azure](cdn-custom-ssl.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um ponto de extremidade CDN](cdn-create-new-endpoint.md).
