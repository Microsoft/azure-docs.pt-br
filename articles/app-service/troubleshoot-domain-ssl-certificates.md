---
title: Solucionar problemas de domínio e TLS/certificados SSL
description: Encontre soluções para os problemas comuns que você pode encontrar ao configurar um domínio ou um certificado TLS/SSL no serviço Azure App.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97607602"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Solucionar problemas de domínio e TLS/certificado SSL no serviço Azure App

Este artigo lista os problemas comuns que você pode encontrar ao configurar um domínio ou um certificado TLS/SSL para seus aplicativos Web no serviço Azure App. Adicionalmente, descreve as possíveis causas e soluções para esses problemas.

Se você precisar de mais ajuda em qualquer ponto deste artigo, contate os especialistas do Azure nos [fóruns do Azure e do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemas de certificado

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Não é possível adicionar uma associação de certificado TLS/SSL a um aplicativo 

#### <a name="symptom"></a>Sintoma

Ao adicionar uma associação TLS, você recebe a seguinte mensagem de erro:

"Falha ao adicionar associação SSL. Não é possível definir o certificado para o VIP existente, pois outro VIP já usa esse certificado."

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se você tiver várias associações SSL com base em IP para o mesmo endereço IP entre vários aplicativos. Por exemplo, o aplicativo A tem um SSL com base em um IP com certificado antigo. O aplicativo B tem um SSL com base em IP com um certificado novo para o mesmo endereço IP. Quando você atualiza a associação TLS do aplicativo com o novo certificado, ele falha com esse erro porque o mesmo endereço IP está sendo usado para outro aplicativo. 

#### <a name="solution"></a>Solução 

Para corrigir esse problema, use um dos seguintes métodos:

- Exclua a associação SSL baseada em IP no aplicativo que usa o certificado antigo. 
- Crie uma nova associação SSL com base em IP que usa o novo certificado.

### <a name="you-cant-delete-a-certificate"></a>Não é possível excluir um certificado 

#### <a name="symptom"></a>Sintoma

Quando você tentar excluir um certificado, a seguinte mensagem de erro é exibida:

"Não é possível excluir o certificado porque ele está sendo usado atualmente em uma associação TLS/SSL. A associação TLS deve ser removida antes que você possa excluir o certificado. "

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se outro aplicativo usar o certificado.

#### <a name="solution"></a>Solução

Remova a associação TLS para esse certificado dos aplicativos. Em seguida, tente excluir o certificado. Se você ainda não conseguir excluir o certificado, limpe o cache do navegador da Internet e reabra o portal do Azure em uma nova janela do navegador. Em seguida, tente excluir o certificado.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Não é possível comprar um certificado do Serviço de Aplicativo 

#### <a name="symptom"></a>Sintoma
Não é possível comprar um [certificado do Serviço de Aplicativo do Azure](./configure-ssl-certificate.md#import-an-app-service-certificate) pelo portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução
Esse problema pode ocorrer por qualquer um dos seguintes motivos:

- O plano do Serviço de Aplicativo é Gratuito ou Compartilhado. Esses tipos de preço não dão suporte a TLS. 

    **Solução**: Atualize o plano do serviço de aplicativo para o aplicativo para Standard.

- A assinatura não possui um cartão de crédito válido.

    **Solução**: adicione um cartão de crédito à sua assinatura. 

- A oferta de assinatura não dá suporte para compra de um certificado do Serviço de Aplicativo, como o Microsoft Student.  

    **Solução**: atualize sua assinatura. 

- A assinatura alcançou o limite de compras permitidas em uma assinatura.

    **Solução**: certificados do Serviço de Aplicativo têm um limite de 10 compras de certificado para os tipos de assinatura EA e Pagamento Conforme o Uso. Para outros tipos de assinatura, o limite é 3. Para aumentar o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O certificado de Serviço de Aplicativo foi marcado como fraude. Você recebeu mensagem de erro a seguir: "Seu certificado foi sinalizado para uma possível fraude. A solicitação está sendo examinada. Se o certificado não se tornar utilizável dentro de 24 horas, entre em contato com o suporte do Azure.

    **Solução**: se o certificado estiver marcado como fraude e não for resolvido após 24 horas, siga estas etapas:

    1. Entre no [portal do Azure](https://portal.azure.com).
    2. Vá para **Certificados do Serviço de Aplicativo** e selecione o certificado.
    3. Selecione **configuração de certificado**  >  **etapa 2: verificar** a  >  **verificação de domínio**. Essa etapa envia uma notificação de email para o provedor de certificados do Azure para resolver o problema.

## <a name="custom-domain-problems"></a>Problemas de domínio personalizado

### <a name="a-custom-domain-returns-a-404-error"></a>Um domínio personalizado retorna um erro 404 

#### <a name="symptom"></a>Sintoma

Ao navegar para o site usando o nome de domínio personalizado, você pode receber a seguinte mensagem de erro:

"Erro 404 - aplicativo Web não encontrado."

#### <a name="cause-and-solution"></a>Causa e solução

**Causa 1** 

O domínio personalizado configurado não tem um registro CNAME ou A. 

**Solução para a causa 1**

- Se você adicionou um registro A, verifique se um registro TXT também foi adicionado. Para obter mais informações, consulte [Criar o registro A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se você não precisar usar o domínio raiz para o aplicativo, é recomendável usar um registro CNAME em vez de um registro A.
- Não use um registro CNAME e um registro A para o mesmo domínio. Esse problema pode causar um conflito e impedir que o domínio seja resolvido. 

**Causa 2** 

O navegador da Internet ainda pode estar armazenando o endereço IP antigo em cache para o domínio. 

**Solução para a Causa 2**

Limpe o navegador. Para dispositivos do Windows, você pode executar o comando `ipconfig /flushdns`. Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o domínio aponta para o endereço IP do aplicativo.

### <a name="you-cant-add-a-subdomain"></a>Não é possível adicionar um subdomínio 

#### <a name="symptom"></a>Sintoma

Não é possível adicionar um novo nome do host a um aplicativo para atribuir um subdomínio.

#### <a name="solution"></a>Solução

- Verifique com o administrador de assinatura para certificar-se de que você tem permissões para adicionar um nome do host ao aplicativo.
- Se precisar de mais subdomínios, recomendamos que você altere o domínio de hospedagem para o serviço de nomes de domínio do Azure (DNS). Usando o DNS do Azure, será possível adicionar 500 nomes do host ao aplicativo. Para obter mais informações, consulte [Adicionar um subdomínio](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website).

### <a name="dns-cant-be-resolved"></a>DNS não pode ser resolvido

#### <a name="symptom"></a>Sintoma

Você recebeu a mensagem de erro a seguir:

"O registro DNS não pôde ser localizado."

#### <a name="cause"></a>Causa
Esse problema ocorre por um dos seguintes motivos:

- O período de TTL (vida útil) não expirou. Verifique a configuração de DNS para seu domínio para determinar o valor de TTL e aguarde até que o período expire.
- A configuração do DNS está incorreta.

#### <a name="solution"></a>Solução
- Aguarde 48 horas para que esse problema seja resolvido sozinho.
- Se você puder alterar a configuração de TTL em sua configuração de DNS, altere o valor para 5 minutos para ver se isso resolve o problema.
- Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o domínio aponta para o endereço IP do aplicativo. Se isso não estiver, configure o registro A para o endereço IP correto do aplicativo.

### <a name="you-need-to-restore-a-deleted-domain"></a>É necessário restaurar um domínio excluído 

#### <a name="symptom"></a>Sintoma
O domínio não está mais visível no portal do Azure.

#### <a name="cause"></a>Causa 
O proprietário da assinatura pode ter excluído acidentalmente o domínio.

#### <a name="solution"></a>Solução
Se o domínio foi excluído há menos de sete dias, o domínio ainda não iniciou o processo de exclusão. Nesse caso, você poderá comprar o mesmo domínio novamente no portal do Azure com a mesma assinatura. (Certifique-se de digitar o nome de domínio exato na caixa de pesquisa.) Você não será cobrado novamente para este domínio. Se o domínio tiver sido excluído há mais de sete dias, entre em contato com o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter ajuda com a restauração do domínio.

## <a name="domain-problems"></a>Problemas de domínio

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Você comprou um certificado TLS/SSL para o domínio errado

#### <a name="symptom"></a>Sintoma

Você comprou um certificado do Serviço de Aplicativo para o domínio incorreto. Não é possível atualizar o certificado para usar o domínio correto.

#### <a name="solution"></a>Solução

Exclua o certificado e, em seguida, compre um novo.

Se o certificado atual que usa o domínio incorreto estiver no estado "Emitido", você também será cobrado pelo certificado. Certificados de Serviço de Aplicativo não são reembolsáveis, mas você pode contatar o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver se há outras opções. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Um certificado do Serviço de Aplicativo foi renovado, mas o aplicativo mostra o certificado antigo 

#### <a name="symptom"></a>Sintoma

O certificado do Serviço de Aplicativo foi renovado, mas o aplicativo que usa o certificado do Serviço de Aplicativo ainda está usando o certificado antigo. Além disso, você recebeu um aviso de que o protocolo HTTPS é necessário.

#### <a name="cause"></a>Causa 
O serviço de aplicativo sincroniza automaticamente seu certificado dentro de 48 horas. Ao girar ou atualiza um certificado, às vezes, o aplicativo ainda está recuperando o certificado antigo e não o certificado atualizado recentemente. O motivo é que o trabalho para sincronizar o recurso de certificado ainda não foi executado. Clique em sincronizar. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

#### <a name="solution"></a>Solução

Você pode forçar uma sincronização do certificado:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione os **Certificados do Serviço de Aplicativo** e, em seguida, selecione o certificado.
2. Selecione **rechaveamento e sincronização** e, em seguida, selecione **sincronizar**. A sincronização leva algum tempo para ser concluída. 
3. Quando a sincronização for concluída, você verá a notificação a seguir: "Todos os recursos atualizados com êxito com o certificado mais recente."

### <a name="domain-verification-is-not-working"></a>A verificação de domínio não está funcionando 

#### <a name="symptom"></a>Sintoma 
O certificado do Serviço de Aplicativo requer a verificação de domínio antes do certificado estar pronto para uso. Ao selecionar **Verificar**, o processo falha.

#### <a name="solution"></a>Solução
Verifique seu domínio manualmente adicionando um registro TXT:

1. Acesse o provedor de Serviço de Nomes de Domínio (DNS) que hospeda o nome de domínio.
1. Adicione um registro TXT ao domínio que usa o valor do token de domínio exibido no portal do Azure. 

Aguarde alguns minutos para que a propagação de DNS seja executada e, em seguida, selecione o botão **Atualizar** para disparar a verificação. 

Como uma alternativa, é possível usar o método de páginas da Web em HTML para verificar o domínio manualmente. Esse método permite que a autoridade de certificação confirme a propriedade do domínio do domínio para o qual o certificado é emitido.

1. Crie um arquivo HTML nomeado {token de verificação de domínio}.html. O conteúdo desse arquivo deve ser o valor do token de verificação de domínio.
1. Carregue esse arquivo na raiz do servidor Web que hospeda o domínio.
1. Selecione **Atualizar** para verificar o status do certificado. Pode demorar alguns minutos até a verificação ser concluída.

Por exemplo, se você estiver comprando um certificado padrão do azure.com utilizando o token de verificação de domínio 1234abcd, uma solicitação da Web feita para https://azure.com/1234abcd.html deverá retornar 1234abcd. 

> [!IMPORTANT]
> A ordem de um certificado tem apenas 15 dias para concluir a operação de verificação de domínio. Após 15 dias, a autoridade de certificação negará o certificado e você não será cobrado pelo certificado. Nessa situação, exclua esse certificado e tente novamente.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Não é possível comprar um domínio

#### <a name="symptom"></a>Sintoma
Não é possível comprar um domínio do serviço de aplicativo no portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução

Esse problema ocorre por um dos seguintes motivos:

- Não há cartão de crédito na assinatura do Azure ou o cartão de crédito é inválido.

    **Solução**: adicione um cartão de crédito à sua assinatura.

- Você não é o proprietário da assinatura, portanto, não tem permissão para comprar um domínio.

    **Solução**: [atribua a função de Proprietário](../role-based-access-control/role-assignments-portal.md) à sua conta. Ou contate o administrador da assinatura para obter permissão para comprar um domínio.
- Você atingiu o limite para comprar os domínios na sua assinatura. O limite atual é 20.

    **Solução**: para solicitar um aumento até o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O tipo de assinatura do Azure não dá suporte para compra de um domínio do Serviço de Aplicativo.

    **Solução**: atualize a assinatura do Azure para outro tipo de assinatura, como uma assinatura de Pagamento Conforme o Uso.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Não é possível adicionar um nome do host a um aplicativo 

#### <a name="symptom"></a>Sintoma

Ao adicionar um nome do host, o processo falha ao validar e verificar o domínio.

#### <a name="cause"></a>Causa 

Esse problema ocorre por um dos seguintes motivos:

- Você não tem permissão para adicionar um nome do host.

    **Solução**: solicite ao administrador da assinatura a conceder-lhe permissão para adicionar um nome do host.
- Não foi possível verificar a propriedade do domínio.

    **Solução**: verifique se o registro A ou CNAME está configurado corretamente. Para mapear um domínio personalizado para um aplicativo, crie um registro A ou um registro CNAME. Se quiser utilizar um domínio raiz, use os registros TXT e A:

    |Tipo de registro|Host|Apontar para|
    |------|------|-----|
    |A|@|Endereço IP para um aplicativo|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Perguntas frequentes

**É necessário configurar meu domínio personalizado para meu site depois de comprá-lo?**

Quando você adquire um domínio do portal do Azure, o aplicativo do serviço de aplicativo é configurado automaticamente para usar esse domínio personalizado. Você não precisa executar nenhuma etapa adicional. Para obter mais informações, Assista à [auto-ajuda do serviço de Azure App: adicionar um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) em channel9.

**Posso usar um domínio adquirido no portal do Azure para apontar para uma VM do Azure em vez disso?**

Sim, você pode apontar o domínio para uma VM. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](../dns/dns-custom-domain.md).

**O meu domínio é hospedado pelo GoDaddy ou pelo DNS do Azure?**

Os domínios do serviço de aplicativo usam GoDaddy para o registro de domínio e o DNS do Azure para hospedar os domínios. 

**Tenho a renovação automática habilitada, mas ainda recebeva um aviso de renovação para o meu domínio por email. O que devo fazer?**

Se você tiver a renovação automática habilitada, não será necessário realizar nenhuma ação. O email de aviso é fornecido para informar que o domínio está perto de expirar e para renovar manualmente se a renovação automática não estiver habilitada.

**Serei cobrado pelo DNS do Azure que hospeda meu domínio?**

O custo inicial da compra de domínio se aplica somente ao registro de domínio. Além do custo de registro, há encargos incorridos para o DNS do Azure com base no seu uso. Para obter mais informações, consulte [preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/) para obter mais detalhes.

**Comprei meu domínio anteriormente na portal do Azure e desejamos mudar da hospedagem GoDaddy para a hospedagem de DNS do Azure. Como posso fazer isso?**

Não é obrigatório migrar para a hospedagem de DNS do Azure. Se você quiser migrar para o DNS do Azure, a experiência de gerenciamento de domínio no portal do Azure sobre fornece informações sobre as etapas necessárias para migrar para o DNS do Azure. Se o domínio foi adquirido por meio do serviço de aplicativo, a migração da hospedagem do GoDaddy para o DNS do Azure é um procedimento relativamente simples.

**Eu gostaria de comprar meu domínio do domínio do serviço de aplicativo, mas posso hospedar meu domínio em GoDaddy em vez de DNS do Azure?**

A partir de 24 de julho de 2017, os domínios do serviço de aplicativo adquiridos no portal são hospedados no DNS do Azure. Se preferir usar um provedor de hospedagem diferente, você deverá acessar seu site para obter uma solução de Hospedagem de domínio.

**É necessário pagar pela proteção de privacidade para meu domínio?**

Ao comprar um domínio por meio do portal do Azure, você pode optar por adicionar privacidade sem custo adicional. Essa é uma das vantagens de comprar seu domínio por meio do serviço de Azure App.

**Se eu decidir que não quero mais meu domínio, posso me repassar ao meu dinheiro?**

Quando você adquire um domínio, não é cobrado por um período de cinco dias, durante o qual você pode decidir que não deseja o domínio. Se você decidir que não quer o domínio dentro desse período de cinco dias, não será cobrado. (. os domínios do Reino Unido são uma exceção a isso. Se você comprar um domínio. Reino Unido, você será cobrado imediatamente e não poderá ser reembolsado.)

**Posso usar o domínio em outro aplicativo de serviço Azure App em minha assinatura?**

Sim. Ao acessar a folha domínios personalizados e TLS no portal do Azure, você vê os domínios que comprou. Você pode configurar seu aplicativo para usar qualquer um desses domínios.

**Posso transferir um domínio de uma assinatura para outra assinatura?**

Você pode mover um domínio para outro grupo de recursos/assinatura usando o cmdlet do PowerShell [move-AzResource](/powershell/module/az.Resources/Move-azResource) .

**Como posso gerenciar meu domínio personalizado se não tiver atualmente um aplicativo de serviço de Azure App?**

Você pode gerenciar seu domínio mesmo que não tenha um aplicativo Web do serviço de aplicativo. O domínio pode ser usado para serviços do Azure como máquina virtual, armazenamento, etc. Se você pretende usar o domínio para aplicativos Web do serviço de aplicativo, precisará incluir um aplicativo Web que não esteja no plano de Serviço de Aplicativo Gratuito para associar o domínio ao seu aplicativo Web.

**Posso mover um aplicativo Web com um domínio personalizado para outra assinatura ou de Ambiente do Serviço de Aplicativo v1 para v2?**

Sim, você pode mover seu aplicativo Web entre assinaturas. Siga as orientações em [como mover recursos no Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Há algumas limitações ao mover o aplicativo Web. Para obter mais informações, consulte [limitações para mover os recursos do serviço de aplicativo](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

Depois de mover o aplicativo Web, as associações de nome de host dos domínios dentro da configuração domínios personalizados devem permanecer as mesmas. Nenhuma etapa adicional é necessária para configurar as associações de nome de host.