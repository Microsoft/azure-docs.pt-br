---
title: Perguntas frequentes sobre o Cloudyn no Azure
description: Saiba como usar o portal do Cloudyn para resolver problemas comuns de configuração corporativa indireta e responder a outras perguntas frequentes.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 92a62de4223d2ec6d0441840b90fdce20c656d02
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546221"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Perguntas frequentes sobre o Cloudyn

Este artigo aborda algumas perguntas comuns sobre o Cloudyn. Se você tiver dúvidas sobre o Cloudyn, faça perguntas nas [perguntas frequentes sobre o Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Como resolver problemas comuns de configuração de empresa indireta?

Ao usar o portal do Cloudyn pela primeira vez, você poderá ver as seguintes mensagens se for um usuário do Contrato Enterprise ou Provedor de Solução de Nuvem (CSP):

- "A chave de API especificada não é uma chave de registro de nível superior" exibida no assistente **Configurar o Cloudyn** .
- "Inscrição direta – Não" exibido no portal do Contrato Enterprise.
- "Nenhum dado de uso encontrado nos últimos 30 dias. Entre em contato com o distribuidor para ter certeza de que a marcação foi habilitada para sua conta do Azure" exibido no portal do Cloudyn.

As mensagens anteriores indicam que você adquiriu um Contrato Enterprise do Azure por meio de um revendedor ou CSP. Seu revendedor ou CSP precisa habilitar o _markup_ para que você possa exibir os dados da conta do Azure no Cloudyn.

Veja como corrigir os problemas:

1. Seu revendedor precisa habilitar _marcação_ para sua conta. Para obter instruções, consulte o [Guia de integração de cliente indireto](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Você gera a chave de Contrato Enterprise do Azure para usar com o Cloudyn.

Somente um administrador de serviços do Azure pode habilitar o Cloudyn. As permissões de coadministrador são insuficientes.

Habilite a API de cobrança do Azure antes de gerar a chave de API do Contrato Enterprise do Azure para configurar o Cloudyn seguindo as instruções em:

- [Visão geral das APIs de Relatórios para clientes Enterprise](../manage/enterprise-api.md)
- [API de relatórios do Microsoft Azure Enterprise Portal](https://ea.azure.com/helpdocs/reportingAPI) em **Habilitando o acesso a dados para a API**


Você também precisa conceder permissões para administradores de departamento, proprietários de conta e administradores de empresa para _exibir encargos_ com a API de cobrança.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Por que não vejo as recomendações do Otimizador?

As informações de recomendação só estão disponíveis para as contas que estão ativadas. Você não verá as informações de recomendação nas categorias do relatório do **Otimizador** para as contas que estão *desativadas* , incluindo:

- Gerenciador de otimização
- Otimização de dimensionamento
- Ineficiências

Se você não consegue visualizar nenhum dado de recomendação do Otimizador, provavelmente, você tem contas que não estão ativadas. Para ativar uma conta, você precisa registrá-la com suas credenciais do Azure.

Para ativar uma conta:

1.    No portal da Cloudyn, clique em **Configurações** no canto superior direito e selecione **Contas de Nuvem** .
2.    Na guia Contas do Microsoft Azure, procure as contas que têm uma assinatura **desativada** .
3.    À direita de uma conta não ativada, clique no símbolo **editar** se parece com um lápis.
4.    Sua ID de locatário e a ID da taxa são detectadas automaticamente. Clique em **Próximo** .
5.    Você será redirecionado para o portal do Azure. Entre no portal e autorize o Coletor Cloudyn a acessar os dados do Azure.
6.    Em seguida, você será redirecionado para a página de gerenciamento das Contas do Cloudyn e sua assinatura será atualizada com o status de conta **ativo** . Ela mostra um símbolo de marca de seleção verde.
7.    Se você não vir um símbolo de marca de seleção verde para uma ou mais assinaturas, isso significa que você não terá permissões para criar um aplicativo de leitor (o CloudynCollector) para a assinatura. Um usuário com permissões mais altas para a assinatura precisa repetir as etapas 3 e 4.  

Depois de concluir as etapas anteriores, você pode exibir as recomendações do Otimizador dentro de um a dois dias. No entanto, pode levar até cinco dias antes que os dados de otimização completa fiquem disponíveis.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Como fazer para habilitar usuários suspensos ou bloqueados?

Primeiro, vamos analisar o cenário mais comum que faz com que as contas de usuário sejam *initiallySuspended* .

> O Admin1 pode ser um usuário do Contrato Enterprise ou o Provedor de Soluções de Nuvem da Microsoft. Sua organização está pronta para começar a usar o Cloudyn.  Ele se registra por meio do portal do Azure e entra no portal do Cloudyn. Como a pessoa que registra o serviço do Cloudyn e entra no portal do Cloudyn, o Admin1 se torna o *administrador principal* . O Admin1 não cria as contas de usuário. No entanto, usando o portal do Cloudyn, ele cria contas do Azure e configura uma hierarquia de entidade. O Admin1 informa o Admin2, um administrador de locatários, que ele precisa se registrar no Cloudyn e entrar portal do Cloudyn.
>
> O Admin2 se registra por meio do portal do Azure. No entanto, quando tenta entrar no portal do Cloudyn, ele obtém um erro informando que sua conta está **suspensa** . O administrador principal, Admin1, é notificado sobre a suspensão da conta. O Admin1 precisa ativar a conta do Admin2 e permitir *acesso de entidade de administrador* para as entidades apropriadas, permitir o acesso ao gerenciamento de usuários e ativar a conta de usuário.


Se você receber um alerta solicitando para permitir o acesso de um usuário, precisará ativar a conta do usuário.

Para ativar a conta do usuário:

1. Entre no Cloudyn usando a conta de usuário administrativo do Azure que você usou para configurar o Cloudyn. Ou entre usando uma conta de usuário que recebeu acesso de administrador.
2. Selecione o símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Usuários** .
3. Localize o usuário, selecione o símbolo de lápis e edite o usuário.
4. Em **Status do usuário** , altere o status de **Suspenso** para **Ativo** .

As contas de usuário do Cloudyn se conectam usando o logon único do Azure. Se um usuário digitar incorretamente sua senha, ele poderá ficar bloqueado no Cloudyn mesmo que ainda possa acessar o Azure.

Se você alterar seu endereço de email no Cloudyn do endereço padrão do Azure, sua conta poderá ser bloqueada. Ela pode mostrar “status initiallySuspended.” Se sua conta de usuário estiver bloqueada, contate um administrador alternativo para redefinir a sua conta.

Recomendamos que você crie pelo menos duas contas de administrador do Cloudyn caso uma das contas fique bloqueada.

Se não puder entrar no portal do Cloudyn, verifique se você está usando a URL correta para entrar no Cloudyn. Use [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Evite usar a URL de direcionamento do Cloudyn `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Como ativar contas não ativadas com as credenciais do Azure?

Assim que suas contas do Azure são descobertas pelo Cloudyn, os dados de custo são fornecidos imediatamente nos relatórios baseados em custo. No entanto, para o Cloudyn fornecer dados de uso e desempenho, você precisa registrar suas credenciais do Azure das contas. Para obter instruções, confira [Adicionar uma conta ou atualizar uma assinatura](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Para adicionar as credenciais do Azure a uma conta, no portal do Cloudyn, selecione o símbolo de edição à direita do nome da conta, não da assinatura.

Até que suas credenciais do Azure sejam adicionadas ao Cloudyn, a conta aparece como _inativa_ .

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Como adicionar várias contas e entidades a uma assinatura existente?

Outras entidades são usadas para adicionar outros Contratos Enterprise a uma assinatura do Cloudyn. Para saber mais, confira [Criar e gerenciar entidades](tutorial-user-access.md#create-and-manage-entities).

Para os CSPs:

Para adicionar contas do CSP adicionais a uma entidade, selecione **Acesso MSP** em vez de **Enterprise** ao criar a nova entidade. Se sua conta está registrada como um Contrato Enterprise e você deseja adicionar as credenciais do CSP, a equipe de suporte do Cloudyn talvez precise modificar as configurações da conta. Se você for um assinante pago do Azure, poderá criar uma nova solicitação de suporte no portal do Azure. Selecione **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte** .

## <a name="currency-symbols-in-cloudyn-reports"></a>Símbolos de moeda em relatórios do Cloudyn

Você pode ter várias contas do Azure usando moedas diferentes. No entanto, os relatórios de custos no Cloudyn não mostram mais de um tipo de moeda por relatório.

Se você tiver várias assinaturas usando moedas diferentes, uma entidade pai e suas moedas de entidades filho serão exibidas em USD **$** . A melhor prática sugerida é evitar o uso de moedas diferentes na mesma hierarquia de entidades. Em outras palavras, todas as suas assinaturas organizadas em uma estrutura de entidade devem usar a mesma moeda.

O Cloudyn detecta a moeda da assinatura do Contrato Enterprise e apresenta-a da forma correta nos relatórios automaticamente.  No entanto, o Cloudyn apenas exibe USD **$** para contas do Azure diretas da Web e de CSP.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quais são os cronogramas de atualização de dados do Cloudyn?

O Cloudyn tem os seguintes cronogramas de atualização de dados:

- **Inicial** : pode levar até 24 horas para exibir dados de custo no Cloudyn após a configuração. Também pode levar até 10 dias para o Cloudyn coletar dados suficientes para exibir as recomendações de dimensionamento.
- **Diariamente** : do décimo dia ao final de cada mês, o Cloudyn deve mostrar os dados atualizados do dia anterior até aproximadamente UTC+3 no dia seguinte.
- **Mensalmente** : do primeiro até o décimo dia de cada mês, o Cloudyn pode mostrar apenas os dados até o final do mês anterior.

O Cloudyn processa os dados do dia anterior, quando os dados completos do dia anterior estão disponíveis. Os dados do dia anterior ficam geralmente disponíveis no Cloudyn todos os dias por volta de UTC+3. Alguns dados, como marcas, podem levar mais 24 horas para serem processados.

Os dados para o mês atual não estão disponíveis para coleta no início de cada mês. Durante o período, os provedores de serviço finalizam sua cobrança do mês anterior. Os dados do mês anterior aparecem no Cloudyn de 5 a 10 dias após o início de cada mês. Durante esse tempo, talvez você veja apenas os custos amortizados do mês anterior. Talvez você não veja dados de cobrança ou de uso diário. Quando os dados se tornam disponíveis, o Cloudyn os processa retroativamente. Após o processamento, todos os dados mensais são exibidos entre o 5º e o 10º dia de cada mês.

Se houver um atraso no envio de dados do Azure para o Cloudyn, os dados serão registrados no Azure. Os dados são transferidos para o Cloudyn quando a conexão é restaurada.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Flutuações de custo nos Relatórios de Custo do Cloudyn

Relatórios de custos podem mostrar flutuações de custo sempre que provedores de serviços de nuvem enviam arquivos atualizados de cobrança. Custos flutuantes ocorrem quando novos arquivos são recebidos de um provedor de serviços de nuvem fora do cronograma de relatório diário ou mensal normal. Alterações de custo não resultam de recálculo do Cloudyn.

Ao longo do mês, todos os arquivos de cobrança enviados pelo seu provedor de serviços de nuvem são uma estimativa dos seus custos diários. Às vezes, os dados são atualizados com frequência, ocasionalmente, várias vezes por dia. As atualizações são mais frequentes com a AWS do que com o Azure. Os custos totais devem permanecer estáveis quando o cálculo de cobrança do mês anterior está concluído e o arquivo de cobrança final foi recebido. Geralmente, até o dia 10 do mês.

As alterações ocorrem quando você recebe ajustes de custo de seu provedor de serviços de nuvem. Receber créditos é um exemplo. As alterações podem ocorrer meses após o mês relevante ter sido encerrado. As alterações são mostradas sempre que um recálculo é feito pelo seu provedor de serviços de nuvem. O Cloudyn atualiza seus dados históricos para garantir que todos os ajustes sejam recalculados. Ele também verifica se os custos são mostrados com precisão em seus relatórios.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Como um CSP direto pode configurar o acesso ao Cloudyn para clientes ou parceiros de CSP indireto?

Consulte [configurar acesso de CSP indireto no Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn) para obter instruções.

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>O que faz com que o item de menu Otimizador apareça?

Após adicionar o acesso do Azure Resource Manager e os dados forem coletados, você deverá visualizar a opção **Otimizador** . Para ativar o acesso do Azure Resource Manager, consulte [Como ativar contas não ativadas com as credenciais do Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>O Cloudyn é baseado em agente?

Não. Os agentes não são usados. Os dados da métrica da máquina virtual do Azure para VMs são coletados da API do Microsoft Insights. Se você quiser reunir dados de métrica de VMs do Azure, eles precisam ter as configurações de diagnóstico habilitadas.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Relatórios Cloudyn mostram mais de um locatário do AD por relatório?

Sim. É possível [criar uma correspondência de entidade de conta de nuvem](tutorial-user-access.md#create-and-manage-entities) para cada locatário do AD que você possui. Então, você poderá visualizar todos os dados de locatário do Azure Active Directory e outros provedores de plataforma de nuvem, incluindo Amazon Web Services e Google Cloud Platform.
