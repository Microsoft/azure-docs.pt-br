---
title: Provisionar um usuário sob demanda usando Azure Active Directory
description: Forçar sincronização
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255654"
---
# <a name="on-demand-provisioning"></a>Provisionamento sob demanda
Use o provisionamento sob demanda para provisionar um usuário em um aplicativo em segundos. Entre outras coisas, você pode usar essa capacidade para:

* Solucione problemas de configuração rapidamente.
* Valide as expressões que você definiu.
* Testar filtros de escopo.

## <a name="how-to-use-on-demand-provisioning"></a>Como usar o provisionamento sob demanda

1. Entre no **portal do Azure**.
1. Vá para **todos os serviços**  >  **aplicativos empresariais**.
1. Selecione seu aplicativo e vá para a página de configuração de provisionamento.
1. Configure o provisionamento fornecendo suas credenciais de administrador.
1. Selecione **provisionar sob demanda**.
1. Pesquisar um usuário por nome, sobrenome, nome de exibição, nome principal de usuário ou endereço de email.
   > [!NOTE]
   > Para o aplicativo de provisionamento de RH na nuvem (workday/SuccessFactors para AD/Azure AD), o valor de entrada é diferente. Para o cenário workday, forneça "WID" do usuário no workday. Para o cenário SuccessFactors, forneça "personIdExternal" do usuário no SuccessFactors. 
 
1. Selecione **provisionar** na parte inferior da página.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Captura de tela que mostra a interface do usuário do portal do Azure para provisionamento de usuários sob demanda.":::

## <a name="understand-the-provisioning-steps"></a>Entender as etapas de provisionamento

O processo de provisionamento sob demanda tenta mostrar as etapas que o serviço de provisionamento usa ao provisionar um usuário. Normalmente, há cinco etapas para provisionar um usuário. Uma ou mais dessas etapas, explicadas nas seções a seguir, serão mostradas durante a experiência de provisionamento sob demanda.

### <a name="step-1-test-connection"></a>Etapa 1: testar a conexão

O serviço de provisionamento tenta autorizar o acesso ao aplicativo de destino fazendo uma solicitação para um "usuário de teste". O serviço de provisionamento espera uma resposta que indica que o serviço está autorizado a continuar com as etapas de provisionamento. Esta etapa é mostrada apenas quando ela falha. Ela não é mostrada durante a experiência de provisionamento sob demanda quando a etapa é bem-sucedida.

#### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* Verifique se você forneceu credenciais válidas, como o token secreto e a URL do locatário, para o aplicativo de destino. As credenciais necessárias variam de acordo com o aplicativo. Para obter os tutoriais de configuração detalhados, consulte a [lista do tutorial](../saas-apps/tutorial-list.md). 
* Verifique se o aplicativo de destino dá suporte à filtragem nos atributos correspondentes definidos no painel **mapeamentos de atributo** . Talvez seja necessário verificar a documentação da API fornecida pelo desenvolvedor do aplicativo para entender os filtros com suporte.
* Para o sistema para aplicativos SCIM (gerenciamento de identidade entre domínios), você pode usar uma ferramenta como o postmaster. Essas ferramentas ajudam a garantir que o aplicativo responda às solicitações de autorização da maneira que o serviço de provisionamento do Azure Active Directory (Azure AD) espera. Observe uma [solicitação de exemplo](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>Etapa 2: importar usuário

Em seguida, o serviço de provisionamento recupera o usuário do sistema de origem. Os atributos de usuário que o serviço recupera são usados posteriormente para:

* Avalie se o usuário está no escopo para provisionamento.
* Verifique o sistema de destino para um usuário existente.
* Determine quais atributos de usuário exportar para o sistema de destino.

#### <a name="view-details"></a>Exibir detalhes


A seção **Exibir detalhes** mostra as propriedades do usuário que foram importados do sistema de origem (por exemplo, Azure AD).

#### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* A importação do usuário pode falhar quando o atributo correspondente estiver ausente no objeto de usuário no sistema de origem. Para resolver essa falha, tente uma destas abordagens:

  * Atualize o objeto de usuário com um valor para o atributo correspondente.
  * Altere o atributo correspondente em sua configuração de provisionamento.

* Se um atributo esperado estiver ausente na lista importada, verifique se o atributo tem um valor no objeto de usuário no sistema de origem. O serviço de provisionamento atualmente não dá suporte ao provisionamento de atributos nulos.
* Certifique-se de que a página **mapeamento de atributos** de sua configuração de provisionamento contenha o atributo que você espera.

### <a name="step-3-determine-if-user-is-in-scope"></a>Etapa 3: determinar se o usuário está no escopo

Em seguida, o serviço de provisionamento determina se o usuário está no [escopo](./how-provisioning-works.md#scoping) para provisionamento. O serviço considera aspectos como:

* Se o usuário está atribuído ao aplicativo.
* Se o escopo está definido para **sincronização atribuída** ou **sincronização de todos**.
* Os filtros de escopo definidos em sua configuração de provisionamento.  

#### <a name="view-details"></a>Exibir detalhes

A seção **Exibir detalhes** mostra as condições de escopo que foram avaliadas. Você pode ver uma ou mais das seguintes propriedades:

* **Ativo no sistema de origem** indica que o usuário tem a propriedade `IsActive` definida como **true** no Azure AD.
* **Atribuído ao aplicativo** indica que o usuário está atribuído ao aplicativo no Azure AD.
* **Sincronização de escopo todos** indica que a configuração de escopo permite todos os usuários e grupos no locatário.
* O **usuário tem a função necessária** indica que o usuário tem as funções necessárias para serem provisionadas no aplicativo. 
* Os **filtros de escopo** também são mostrados se você tiver definido filtros de escopo para seu aplicativo. O filtro é exibido com o seguinte formato: {escopo do filtro de escopo} {atributo de filtro de escopo} {operador de filtro de escopo} {valor de filtro de escopo}.

#### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* Verifique se você definiu uma função de escopo válida. Por exemplo, evite usar o [operador de Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) com um valor não inteiro.
* Se o usuário não tiver a função necessária, examine as [dicas para provisionar usuários atribuídos à função de acesso padrão](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Etapa 4: corresponder o usuário entre a origem e o destino

Nesta etapa, o serviço tenta corresponder o usuário recuperado na etapa de importação com um usuário no sistema de destino.

#### <a name="view-details"></a>Exibir detalhes

A página **Exibir detalhes** mostra as propriedades dos usuários que foram correspondidos no sistema de destino. As propriedades que você vê no painel de contexto variam da seguinte maneira:

* Se nenhum usuário for correspondido no sistema de destino, você não verá nenhuma propriedade.
* Se houver um usuário correspondente no sistema de destino, você verá as propriedades desse usuário correspondente do sistema de destino.
* Se vários usuários forem correspondidos, você verá as propriedades de ambos os usuários correspondentes.
* Se vários atributos correspondentes fizerem parte dos mapeamentos de atributo, cada atributo correspondente será avaliado em sequência e os usuários correspondentes para esse atributo serão mostrados.

#### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* O serviço de provisionamento pode não ser capaz de corresponder um usuário no sistema de origem exclusivamente a um usuário no destino. Resolva esse problema garantindo que o atributo correspondente seja exclusivo.
* Verifique se o aplicativo de destino dá suporte à filtragem no atributo que é definido como o atributo correspondente.  

### <a name="step-5-perform-action"></a>Etapa 5: executar a ação

Por fim, o serviço de provisionamento executa uma ação, como criar, atualizar, excluir ou ignorar o usuário.

Veja um exemplo do que você pode ver após o provisionamento bem-sucedido sob demanda de um usuário:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Captura de tela que mostra o provisionamento com êxito sob demanda de um usuário.":::

#### <a name="view-details"></a>Exibir detalhes

A seção **Exibir detalhes** exibe os atributos que foram modificados no aplicativo de destino. Essa exibição representa a saída final da atividade do serviço de provisionamento e os atributos que foram exportados. Se essa etapa falhar, os atributos exibidos representarão os atributos que o serviço de provisionamento tentou Modificar.

#### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* As falhas de exportação de alterações podem variar muito. Verifique a [documentação para provisionar os logs](../reports-monitoring/concept-provisioning-logs.md#error-codes) para falhas comuns.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* **Você precisa desativar o provisionamento para usar o provisionamento sob demanda?.** Para aplicativos que usam um token de portador de vida útil longa ou um nome de usuário e senha para autorização, nenhuma etapa adicional é necessária. Os aplicativos que usam o OAuth para autorização atualmente exigem que o trabalho de provisionamento seja interrompido antes de usar o provisionamento sob demanda. Aplicativos como o G Suite, o box, o local de trabalho por Facebook e a margem de atraso se enquadram nessa categoria. O trabalho está em andamento para dar suporte ao provisionamento sob demanda para todos os aplicativos sem a necessidade de interromper os trabalhos de provisionamento.

* **Quanto tempo leva o provisionamento sob demanda?** O provisionamento sob demanda normalmente leva menos de 30 segundos.

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, há algumas limitações conhecidas para o provisionamento sob demanda. Poste suas [sugestões e comentários](https://aka.ms/appprovisioningfeaturerequest) para que possamos determinar melhor os aprimoramentos a serem feitos em seguida.

> [!NOTE]
> As limitações a seguir são específicas para o recurso de provisionamento sob demanda. Para obter informações sobre se um aplicativo dá suporte a grupos de provisionamento, exclusões ou outros recursos, consulte o tutorial do aplicativo.

* O aplicativo Amazon Web Services (AWS) não oferece suporte ao provisionamento sob demanda. 
* Não há suporte para o provisionamento sob demanda de grupos e funções.
* O provisionamento sob demanda dá suporte à desabilitação de usuários que foram desatribuídos do aplicativo. No entanto, ele não dá suporte à desabilitação ou exclusão de usuários que foram desabilitados ou excluídos do Azure AD. Esses usuários não aparecerão quando você procurar um usuário.

## <a name="next-steps"></a>Próximas etapas

* [Solucionando problemas de provisionamento](./application-provisioning-config-problem.md)