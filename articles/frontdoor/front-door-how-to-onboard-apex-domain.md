---
title: Carregar um domínio raiz ou Apex para uma porta frontal existente-portal do Azure
description: Saiba como carregar um domínio raiz ou Apex para uma porta frontal existente usando o portal do Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646331"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Integrar um domínio raiz ou apex no seu Front Door
A porta frontal do Azure usa registros CNAME para validar a propriedade do domínio para a integração de domínios personalizados. A porta frontal não expõe o endereço IP de front-end associado ao seu perfil de porta frontal. Portanto, você não poderá mapear seu domínio Apex para um endereço IP se sua intenção for integrar-o à porta frontal do Azure.

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. Por exemplo, se seu domínio for `contoso.com` ; você pode criar registros CNAME para o `somelabel.contoso.com` ; mas não pode criar o CNAME para `contoso.com` si mesmo. Essa restrição apresenta um problema para proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás da porta frontal do Azure. Como o uso de um perfil de porta frontal requer a criação de um registro CNAME, não é possível apontar para o perfil de porta frontal do Apex da zona.

Esse problema pode ser resolvido usando registros de alias no DNS do Azure. Ao contrário dos registros CNAME, os registros de alias são criados no Apex da zona. Os proprietários de aplicativos podem usá-lo para apontar o registro de Apex de zona para um perfil de porta frontal que tem pontos de extremidade públicos. Os proprietários de aplicativo apontam para o mesmo perfil de porta frontal usado para qualquer outro domínio dentro de sua zona DNS. Por exemplo, `contoso.com` e `www.contoso.com` pode apontar para o mesmo perfil de porta frontal. 

O mapeamento de seu Apex ou domínio raiz para o perfil de porta frontal basicamente exige o nivelamento CNAME ou a caça do DNS. Um mecanismo no qual o provedor DNS resolve recursivamente a entrada CNAME até atingir um endereço IP. Essa funcionalidade é suportada pelo DNS do Azure para pontos de extremidade de porta frontal. 

> [!NOTE]
> Também há outros provedores DNS que dão suporte ao nivelamento CNAME ou à procura de DNS. no entanto, a porta frontal do Azure recomenda usar o DNS do Azure para seus clientes para hospedar seus domínios.

Você pode usar a portal do Azure para carregar um domínio Apex na sua porta frontal e habilitar o HTTPS nele associando-o a um certificado para terminação de TLS. Os domínios Apex também são chamados de domínios raiz ou Naked.

## <a name="create-an-alias-record-for-zone-apex"></a>Criar um registro de alias para o Apex da zona

1. Abra a configuração de **DNS do Azure** para o domínio a ser integrado.

1. Crie ou edite o registro do Apex da zona.

1. Selecione o **tipo** de registro como *um* registro e, em seguida, selecione *Sim* para conjunto de **registros de alias**. O **tipo de alias** deve ser definido como *recurso do Azure*.

1. Selecione a assinatura do Azure em que o perfil de porta frontal é hospedado. Em seguida, selecione o recurso de porta frontal na lista suspensa de **recursos do Azure** .

1. Selecione **OK** para enviar suas alterações.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Registro de alias para o Apex da zona":::

1. A etapa acima criará um registro de Apex de zona apontando para o recurso de porta frontal e também um mapeamento de registro CNAME ' afdverify ' (exemplo- `afdverify.contosonews.com` ) para que será usado para a integração do domínio em seu perfil de porta frontal.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Carregar o domínio personalizado na sua porta frontal

1. Na guia Designer de porta frontal, selecione o ícone "+" na seção hosts de front-end para adicionar um novo domínio personalizado.

1. Insira o nome de domínio raiz ou Apex no campo nome de host personalizado, por exemplo `contosonews.com` .

1. Depois que o mapeamento CNAME do domínio para sua porta frontal for validado, selecione **Adicionar** para adicionar o domínio personalizado.

1. Selecione **salvar** para enviar as alterações.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Menu de domínio personalizado":::

## <a name="enable-https-on-your-custom-domain"></a>Habilitar HTTPS em seu domínio personalizado

1. Selecione o domínio personalizado que foi adicionado e sob a seção **domínio personalizado https**, altere o status para **habilitado**.

1. Selecione o  **tipo de gerenciamento de certificado** para *' usar meu próprio certificado '*.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Configurações de HTTPS de domínio personalizado":::    

   > [!WARNING]
   > No momento, não há suporte para o tipo de gerenciamento de certificado gerenciado de porta frontal para Apex ou domínios raiz. A única opção disponível para habilitar HTTPS em um Apex ou domínio raiz para a porta frontal é usar seu próprio certificado TLS/SSL personalizado hospedado no Azure Key Vault.

1. Verifique se você configurou as permissões corretas para a porta frontal acessar o cofre de chaves, conforme observado na interface do usuário, antes de prosseguir para a próxima etapa.

1. Escolha uma **conta de Key Vault** da sua assinatura atual e, em seguida, selecione a **versão** **secreta** e secreta apropriada para mapear para o certificado correto.

1. Selecione **Atualizar** para salvar a seleção e, em seguida, selecione **salvar**.

1. Selecione **Atualizar** após alguns minutos e, em seguida, selecione o domínio personalizado novamente para ver o progresso do provisionamento de certificado. 

> [!WARNING]
> Verifique se você criou as regras de roteamento apropriadas para seu domínio Apex ou adicionou o domínio às regras de roteamento existentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
