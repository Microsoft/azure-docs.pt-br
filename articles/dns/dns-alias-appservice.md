---
title: Hospedar aplicativos da web do Azure com carga balanceada no apex da zona
description: Use um registro de alias do DNS do Azure para hospedar aplicativos da Web com balanceamento de carga no ápice da zona
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 7d20ef750aa4556a73852982631423d3d08271f5
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854103"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hospedar aplicativos da web do Azure com carga balanceada no apex da zona

O protocolo DNS impede a atribuição de algo diferente de um registro A ou AAAA no ápice da zona. Um vértice da zona de exemplo é contoso.com. Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do Gerenciador de Tráfego. Não é possível apontar para o perfil do Gerenciador de Tráfego no registro do ápice da zona. Como resultado, os proprietários do aplicativo devem usar uma solução alternativa. Um redirecionamento na camada de aplicativo deve redirecionar do ápice da zona para outro domínio. Um exemplo é um redirecionamento de contoso.com\.para www contoso.com. Esse arranjo apresenta um único ponto de falha para a função de redirecionamento.

Com registros de alias, esse problema não existe mais. Agora, os proprietários do aplicativo podem apontar seu registro do ápice da zona para um perfil do Gerenciador de Tráfego que tenha nós de extremidade externos. Os proprietários de aplicativos podem apontar para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio em sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com podem apontar para o mesmo perfil do Gerenciador de tráfego. Esse é o caso, desde que o perfil do Gerenciador de Tráfego tenha apenas endpoints externos configurados.

Neste artigo, você aprende a criar um registro de alias para o seu ápice de domínio e a configurar seu perfil de Gerenciador de Tráfego do Microsoft Azure para seus aplicativos da web.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, confira o [Tutorial: Hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo usado neste tutorial é contoso.com, mas use seu próprio nome de domínio.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para armazenar todos os recursos usados neste artigo.

## <a name="create-app-service-plans"></a>Criar planos de serviço de aplicativo

Crie dois planos do Serviço de Aplicativo da Web em seu grupo de recursos usando a tabela a seguir para obter informações de configuração. Para obter mais informações sobre como criar um plano do Serviço de Aplicativo, consulte [Gerenciar um plano do Serviço de Aplicativo no Azure](../app-service/app-service-plan-manage.md).


|Nome  |Sistema operacional  |Location  |Camada de preços  |
|---------|---------|---------|---------|
|ASP-01     |Windows|East US|Dev / Teste D1-Compartilhado|
|ASP-02     |Windows|Centro dos EUA|Dev / Teste D1-Compartilhado|

## <a name="create-app-services"></a>Criar Serviços de Aplicativos

Crie dois aplicativos da web, um em cada plano do Serviço de Aplicativo.

1. No canto superior esquerdo da página do portal do Azure, clique em **Criar um recurso**.
2. Digite **Web app** na barra de pesquisa e pressione Enter.
3. Clique em **aplicativo Web**.
4. Clique em **Criar**.
5. Aceite os padrões e use a tabela a seguir para configurar os dois aplicativos da Web:

   |Nome<br>(deve ser exclusivo dentro do. azurewebsites.net)|Grupo de recursos |Plano do serviço de aplicativo/localização
   |---------|---------|---------|
   |App-01|Uso existente<br>Selecione o grupo de recursos|ASP-01(East US)|
   |App-02|Uso existente<br>Selecione o grupo de recursos|ASP-02(Central US)|

### <a name="gather-some-details"></a>Reúna alguns detalhes

Agora você precisa observar o endereço IP e o nome do host dos aplicativos.

1. Abra seu grupo de recursos e clique no seu primeiro aplicativo (**App-01** neste exemplo).
2. Na coluna da esquerda, clique em **Propriedades**.
3. Anote o endereço em **URL** e, em **Endereços IP de saída**, observe o primeiro endereço IP na lista. Você usará essas informações mais tarde quando configurar seus pontos de extremidade do Gerenciador de Tráfego do Microsoft Azure.
4. Repita a operação em **aplicativo-02**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Crie um perfil do Gerenciador de Tráfego no seu grupo de recursos. Use os padrões e digite um nome exclusivo dentro do namespace trafficmanager.net.

Para obter informações sobre como criar um perfil do Gerenciador [de tráfego, consulte início rápido: Crie um perfil do Gerenciador de tráfego para um aplicativo](../traffic-manager/quickstart-create-traffic-manager-profile.md)Web altamente disponível.

### <a name="create-endpoints"></a>Criar pontos de extremidade

Agora você pode criar os pontos de extremidade para os dois aplicativos web.

1. Abra seu grupo de recursos e clique em seu perfil do Gerenciador de Tráfego do Microsoft Azure.
2. Na coluna à esquerda, clique em **pontos de extremidade**.
3. Clique em **Adicionar** .
4. Use a tabela a seguir para configurar os terminais:

   |Tipo  |NOME  |Destino  |Location  |Configurações personalizadas de cabeçalho|
   |---------|---------|---------|---------|---------|
   |Ponto de extremidade externo     |Final-01|Endereço IP que você registrou para o App-01|East US|host:\<a URL que você registrou para o aplicativo 01\><br>Exemplo: **: o aplicativo host-01.azurewebsites.net**|
   |Ponto de extremidade externo     |Final-02|Endereço IP que você registrou para o aplicativo-02|Centro dos EUA|host:\<a URL que você registrou para o aplicativo-02\><br>Exemplo: **: o aplicativo host-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Criar zona DNS

Você pode usar uma zona DNS existente para teste ou criar uma nova zona. Para criar e delegar uma nova zona DNS no Azure, [consulte o tutorial: Hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Adicione o conjunto de registros de alias

Quando sua zona DNS estiver pronta, você poderá adicionar um registro de alias para o ápice da zona.

1. Abra o seu grupo de recursos e clique na zona DNS.
2. Clique em **conjunto de registros**.
3. Adicione o registro definido usando a tabela a seguir:

   |NOME  |Tipo  |Conjunto de registros de alias  |Tipo de alias  |Recursos do Azure|
   |---------|---------|---------|---------|-----|
   |@     |O|Sim|Recursos do Azure|Gerenciador de tráfego - seu perfil|

## <a name="add-custom-hostnames"></a>Adicionar nomes de host personalizados

Adicione um nome de host personalizado para ambos os aplicativos web.

1. Abra seu grupo de recursos e clique em seu primeiro aplicativo da web.
2. Na coluna da esquerda, clique em **Domínios personalizados**.
3. Clique em **Adicionar nome do host**.
4. Em nome de host, digite seu nome de domínio. Por exemplo, contoso.com.

   Seu domínio deve passar pela validação e mostrar marcas de verificação verdes ao lado de **disponibilidade do nome do host** e **propriedade do domínio**.
5. Clique em **Adicionar nome do host**.
6. Para ver o novo nome do host em **Hostnames atribuídos ao site**, atualize seu navegador. A atualização na página sempre mostra alterações imediatamente.
7. Repita este procedimento para seu segundo aplicativo web.

## <a name="test-your-web-apps"></a>Teste seus aplicativos web

Agora você pode testar para garantir que você pode acessar seu aplicativo web e que ele está sendo carga balanceada.

1. Abra um navegador da web e navegue até seu domínio. Por exemplo, contoso.com. Você deve ver a página de aplicativo web padrão.
2. Interrompa seu primeiro aplicativo web.
3. Feche seu navegador da web e aguarde alguns minutos.
4. Inicie seu navegador da Web e navegue até seu domínio. Você ainda deve ver a página do aplicativo da web padrão.
5. Interrompa seu segundo aplicativo web.
6. Feche seu navegador da web e aguarde alguns minutos.
7. Inicie seu navegador da Web e navegue até seu domínio. Você deve ver o erro 403, indicando que o aplicativo da Web está parado.
8. Inicie seu segundo aplicativo da web.
9. Feche seu navegador da web e aguarde alguns minutos.
10. Inicie seu navegador da Web e navegue até seu domínio. Novamente, você verá a página de aplicativo web padrão.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de Tráfego do Apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Para saber como migrar um nome DNS ativo, consulte [migrar um nome DNS ativo para Azure app serviço](../app-service/manage-custom-dns-migrate-domain.md).
