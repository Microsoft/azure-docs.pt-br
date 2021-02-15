---
title: Contratos e aditamentos de EA do Azure
description: Este artigo explica como os contratos e aditamentos de EA do Azure afetam o uso do Portal do EA do Azure.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: c2c3636e98d67616826b03cca9657b806c5bd653
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598006"
---
# <a name="azure-ea-agreements-and-amendments"></a>Contratos e aditamentos de EA do Azure

O artigo explica como os contratos e aditamentos do Azure EA podem afetar seu acesso, uso e pagamento pelos serviços do Azure.

## <a name="enrollment-provisioning-status"></a>Status de provisionamento do registro

A data de início de um novo Pagamento antecipado do Azure (anteriormente chamado de compromisso monetário) é definida pela data em que o centro de operações regionais o processou. Como as ordens do pagamento antecipado do Azure realizadas por meio do portal do Azure EA são processadas no fuso horário UTC, poderá ocorrer algum atraso se a sua ordem de compra do pagamento antecipado do Azure tiver sido processada em outra região. A data de início da cobertura na ordem de compra mostra o início do pagamento antecipado do Azure. A data de início da cobertura é quando o pagamento antecipado do Azure é exibido no portal do Azure EA.

## <a name="support-for-enterprise-customers"></a>Compatibilidade com clientes empresariais

 A [Oferta do plano de suporte do Contrato Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/) do Azure está disponível para alguns clientes.

## <a name="enrollment-status"></a>Status do registro

Um registro tem um dos valores de status a seguir. Cada valor determina como você pode usar e acessar um registro. O status de registro determina em qual fase seu registro está. Ele informa se o registro precisa ser ativado antes que possa ser usado. Ou se o período inicial expirou e você está recebendo cobrança pelo uso excedente.

**Pendente** –o administrador do registro precisa entrar no Portal do EA do Azure. Depois de conectado, o registro muda para o status **Ativo**.

**Ativo** – o registro está acessível e pode ser usado. Você pode criar contas e assinaturas no Portal do EA do Azure. O registro permanece ativo até a data de término do Contrato Enterprise.

**Termo Estendido Indefinido** – o status de termo estendido indefinido ocorre depois que a data de término do Contrato Enterprise é atingida. Antes que o registro do EA atinja a data de término do Contrato Enterprise, o administrador do registro deve decidir:

- Renovar o registro adicionando outro pagamento antecipado do Azure
- Transferir o registro existente para um novo registro
- Migrar para o MOSP (Programa de Assinatura do Microsoft Online)
- Confirmar a desabilitação de todos os serviços associados ao registro

**Expirado**: o registro do EA expira quando atinge a data de término do Contrato Enterprise. O cliente EA é desvinculado do termo estendido e todos os serviços dele são desabilitados.

Desde 1º de agosto de 2019, novos formulários de recusa não são aceitos para clientes comerciais do Azure. Em vez disso, todos os registros entram em um termo estendido indefinido. Se você quiser parar de usar os serviços do Azure, feche sua assinatura no [portal do Azure](https://portal.azure.com). Ou, seu parceiro pode enviar uma solicitação de encerramento. Não há alteração para clientes com tipos de contrato governamentais.

**Transferido** – o status transferido é aplicado a registros que têm os respectivos serviços e contas associados transferidos para um novo registro. Os registros não são transferidos automaticamente se um novo número de registro é gerado durante a renovação. O número de registro anterior deve ser incluído na solicitação de renovação do cliente para uma transferência automática.

## <a name="partner-markup"></a>Marcação de parceiro

No Portal do EA do Azure, a marcação de preço do parceiro ajuda a habilitar relatórios de custo melhores para os clientes. O Portal do EA do Azure mostra o uso e os preços configurados por parceiros para os respectivos clientes.

A marcação permite que os administradores de parceiros adicionem uma marcação percentual aos seus contratos empresariais indiretos. A marcação percentual aplica-se a todas as informações de serviço internas da Microsoft no portal do Azure EA, como taxas de medição, pagamento antecipado do Azure e ordens. Após a marcação ser publicada pelo parceiro, o cliente vê os custos do Azure no portal do Azure EA. Por exemplo, resumo de uso, listas de preços e relatórios de uso baixados.

Desde setembro de 2019, os parceiros podem aplicar marcação a qualquer momento durante um termo. Eles não precisam aguardar até o próximo aniversário do termo para aplicarem a marcação.

A Microsoft não acessará nem utilizará a marcação e os preços associados fornecidos para qualquer finalidade, a menos que seja explicitamente autorizado pelo parceiro do canal.

### <a name="how-the-calculation-works"></a>Como funciona o cálculo

O LSP fornece um número percentual único no portal de EA.    Todas as informações comerciais no portal serão elevadas pelo percentual fornecido pelo LSP. Exemplo:

- O cliente assina um EA com o pagamento antecipado do Azure de US$ 100.000.
- A taxa de medição para o Serviço A é de US$ 10/hora.
- O LSP define a porcentagem de marcação de 10% no portal de EA.
- O exemplo a seguir mostra como o cliente verá as informações comerciais:
    - Saldo monetário: US$ 110.000,00.
    - Taxa de medição para o Serviço A: US$ 11/hora.
    - Informações de uso/hospedagem para o Serviço A, quando usado por 100 horas: US$ 1.100,00.
    - Saldo Monetário disponível para o cliente após a dedução do consumo do Serviço A: US$ 108.900,00.

### <a name="when-to-use-a-markup"></a>Quando usar uma marcação

Use o recurso se você definir a mesma porcentagem de marcação em TODAS as transações comerciais no EA. Ou seja, se você fizer a marcação das informações do pagamento antecipado do Azure, das taxas de medição, das informações de ordens etc.

Não use o recurso de marcação se:
- Você usar taxas diferentes entre o pagamento antecipado do Azure e as taxas de medição.
- Você usar taxas diferentes para medições diferentes.

Se você usa taxas diferentes para medições diferentes, recomendamos que desenvolva uma solução personalizada com base na Chave de API, que pode ser fornecida pelo cliente para efetuar pull de dados de consumo e fornecer relatórios.

### <a name="other-important-information"></a>Outras informações importantes

Esse recurso serve para fornecer uma estimativa do custo do Azure para o cliente final. O LSP é responsável por todas as transações financeiras com o cliente sob o EA.

Revise as informações comerciais, como informação do saldo monetário, lista de preços etc., antes de publicar os preços marcados para o cliente final.

### <a name="how-to-add-a-price-markup"></a>Como adicionar uma marcação de preço

**Etapa 1: adicionar marcação de preço**

1. No Enterprise Portal, escolha **Relatórios** na área de navegação à esquerda.
1. Em _Resumo do uso_, clique na palavra **Marcação** em azul.
1. Insira a porcentagem de marcação (entre -100 e 100) e clique **Visualizar**.


**Etapa 2: revisar e validar**

Examine o preço de marcação em _Resumo do uso_ do termo do pagamento antecipado na exibição do cliente. O preço da Microsoft ainda estará disponível na exibição de parceiro. As exibições podem ser alternadas com a opção "pessoas" da marcação de parceiro no canto superior direito.

1. Reveja os preços na tabela de preços.
1. É possível fazer alterações antes da publicação selecionando **Editar** na guia _Exibir resumo de uso > Exibição de cliente_. 
   
Os preços de serviços e os saldos do pagamento antecipado serão marcados com os mesmos percentuais. Se você tem porcentagens diferentes para o saldo monetário e taxas de medição ou percentuais diferentes para serviços diferentes, não use esse recurso.

**Etapa 3: publicar**

Após a revisão e validação do preço, clique em **Publicar**.
  
Os preços com marcação ficarão disponíveis para os administradores corporativos imediatamente após a publicação. Não é possível fazer edições na marcação. Você deve desabilitar a marcação e começar da Etapa 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Quais registros têm uma marcação habilitada?

Para verificar se um registro tem uma marcação publicada, clique em **Gerenciar** no painel de navegação à esquerda e clique na guia **Registro**. Marque a caixa de registro e veja o status da marcação em _Detalhes do registro_. Lá será exibido o status atual do recurso de marcação para esse EA, que poderá ser Desabilitado, Versão Prévia ou Publicado.

### <a name="how-can-the-customer-download-usage-estimates"></a>Como o cliente pode baixar estimativas de uso?

Após a publicação da marcação de parceiro, o cliente indireto terá acesso ao saldo, arquivos mensais de cobrança em .csv e arquivos de uso detalhado em .csv. Os arquivos de uso detalhado incluirão a taxa de recursos e o custo estendido.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Como eu posso, no papel de parceiro, aplicar a marcação a clientes de EA existentes que anteriormente estavam com outro parceiro?
Os parceiros podem usar o recurso de marcação (no Azure EA) após o processamento de uma Alteração do Parceiro de Canal; não é necessário aguardar o próximo vencimento.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Pagamento antecipado de recursos e solicitação de aumentos de cota

**O sistema impõe as seguintes cotas padrão por assinatura:**

| **Recurso** | **Cota padrão** | **Comentários** |
| --- | --- | --- |
| Instâncias de computação do Microsoft Azure | Vinte instâncias de computação pequenas simultâneas ou equivalentes nos outros tamanhos de instância de computação. | A tabela a seguir instrui como calcular o número equivalente de instâncias pequenas:<ul><li> Extra pequena: uma instância pequena equivalente </li><li> Pequena: uma instância pequena equivalente </li><li> Média: duas instâncias pequenas equivalentes </li><li> Grande: quatro instâncias pequenas equivalentes </li><li> Extragrande: oito instâncias pequenas equivalentes </li> </ul>|
| VMs v2 de instâncias de computação do Microsoft Azure | EA: 350 núcleos | VMs v2 do GA IaaS:<ul><li> Família A0\_A7: 350 núcleos </li><li> Família B\_A0\_A4: 350 núcleos </li><li> Família A8\_A9: 350 núcleos </li><li> Família DF: 350 núcleos</li><li> GF: 350 núcleos </li></ul>|
| Serviços Hospedados do Microsoft Azure | Seis serviços hospedados | Esse limite de serviços hospedados não pode ser aumentado além de seis para uma assinatura individual. Se precisar de serviços hospedados adicionais, incorpore outras assinaturas. |
| Armazenamento do Microsoft Azure | Cinco contas de armazenamento, cada uma com um tamanho máximo de 100 TB. | Você pode aumentar o número de contas de armazenamento para até 20 por assinatura. Se precisar de contas de armazenamento adicionais, incorpore outras assinaturas. |
| SQL Azure | 149 bancos de dados de qualquer tipo (ou seja, Web Edition ou Business Edition). |   |
| Controle de acesso | 50 namespaces por conta. 100 milhões de transações de Controle de Acesso por mês |   |
| Barramento de Serviço | 50 namespaces por conta. 40 conexões de Barramento de Serviço | Os clientes que adquirem conexões do Barramento de Serviço por meio de pacotes de conexão terão cotas iguais ao ponto médio entre o pacote de conexão adquirido e o próximo valor mais alto do pacote de conexão. Os clientes que escolherem um Pacote de 500 terão uma cota de 750. |

## <a name="resource-prepayment"></a>Pagamento antecipado de recursos

A Microsoft fornecerá serviços a você, pelo menos, até o nível do uso associado incluído no pagamento antecipado mensal que você adquiriu (o Pagamento Antecipado de Serviço), mas todos os outros aumentos nos níveis de uso dos recursos do serviço (por exemplo, aumento do número de instâncias de computação em execução ou aumento da quantidade de armazenamento em uso) estão sujeitos à disponibilidade desses recursos de serviços.

Qualquer cota descrita acima não é um Pagamento Antecipado de Serviço. Com o objetivo de determinar o número de instâncias de computação pequenas simultâneas (ou o equivalente delas) que será fornecido pela Microsoft como parte de um Pagamento Antecipado de Serviço, isso é determinado pela divisão do número de horas da instância de computação pequena comprometida adquirida para um mês pelo número de horas do mês mais curto do ano (ou seja, fevereiro, com 672 horas).

## <a name="requesting-a-quota-increase"></a>Solicitar um aumento de cota

Você pode solicitar um aumento de cota a qualquer momento enviando uma [solicitação online](https://g.microsoftonline.com/0WAEP00en/6). Para processar sua solicitação, forneça as seguintes informações:

- A conta Microsoft ou a conta corporativa ou de estudante associada ao proprietário da conta da sua assinatura. Esse é o endereço de email utilizado para entrar no portal do Microsoft Azure para gerenciar suas assinaturas. Além disso, identifique que essa conta está associada a um registro de EA.
- Os recursos e os valores para os quais você quer um aumento de cota.
- A ID da Assinatura do Portal do Desenvolvedor do Azure associada ao seu serviço.
  - Para saber mais sobre como obter sua ID de assinatura, [entre em contato com o suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="plan-skus"></a>SKUs do plano

As SKUs do plano oferecem a capacidade de comprar um pacote de serviços integrados por uma taxa com desconto. As SKUs do plano foram projetadas para se complementarem por meio de ofertas integradas adicionais e proporcionarem maior economia de custos.

Um exemplo seria a assinatura do OMS (Operations Management Suite). O OMS oferece uma maneira simples de acessar um conjunto completo de recursos de gerenciamento baseado em nuvem, incluindo análise, configuração, automação, segurança, backup e recuperação de desastres. As assinaturas do OMS incluem direitos aos componentes do System Center para fornecer uma solução completa para ambientes de nuvem híbrida.

Os Administradores Corporativos podem atribuir Proprietários de Conta para provisionar SKUs de plano adquiridas anteriormente no Enterprise Portal executando estas etapas:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Exibir a tabela de preços para verificar a quantidade incluída

1. Entre como um Administrador Corporativo.
1. Clique em **Relatórios** na área de navegação à esquerda.
1. Clique na guia **Tabela de preços**.
1. Clique no ícone "Download" no canto superior direito.
1. Encontre os números de referência do SKU do plano correspondente com o filtro na coluna "Quantidade Incluída" e selecione valores maiores que "0".

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Proprietários de conta novos/existentes para criar novas assinaturas

**Etapa 1: entre na conta**
1. No portal do Azure EA, selecione a guia **Gerenciar** e navegue até **Assinatura** no menu superior.
1. Verifique se você está conectado como o proprietário da conta.
1. Clique em **+Adicionar assinatura**.
1. Clique em **Comprar**.

Na primeira vez que você adicionar uma assinatura a uma conta, deverá fornecer suas informações de contato. Ao adicionar outras assinaturas, suas informações de contato serão preenchidas para você.

Na primeira vez que você adicionar uma assinatura à conta, receberá a solicitação para aceitar o contrato MOSA e um Plano de Taxas. Essas seções NÃO são aplicáveis a Clientes com Enterprise Agreement, mas atualmente são necessárias para provisionar sua assinatura. O aditamento de registro do Contrato Enterprise do Microsoft Azure substitui os itens acima e a sua relação contratual não será alterada. Marque a caixa indicando que você aceita os termos.

**Etapa 2: atualizar o nome da assinatura**

Todas as novas assinaturas serão adicionadas com o nome de assinatura padrão do "Microsoft Azure Enterprise". É importante atualizar o nome da assinatura para diferenciá-lo das outras assinaturas em seu Registro Corporativo e garantir que seja reconhecido em relatórios no nível corporativo.

Clique em **Assinaturas**, clique na assinatura que você criou e clique em **Editar detalhes da assinatura**.

Atualize o nome da assinatura e o administrador de serviços e clique na marca de seleção. O nome da assinatura aparecerá em relatórios e também será o nome do projeto associado à assinatura no portal de desenvolvimento.
Novas assinaturas podem demorar até 24 horas para aparecer na lista de assinaturas.

Apenas os proprietários da conta podem exibir e gerenciar assinaturas.

### <a name="troubleshooting"></a>Solução de problemas

**Proprietário da conta aparecendo no status pendente**

Quando novos AOs (proprietários da conta) forem adicionados a um registro pela primeira vez, o status deles será exibido sempre como "pendente". Após receber o email de ativação de boas-vindas, o AO poderá entrar para ativar a própria conta. Essa ativação atualizará o status da conta de "pendente" para "ativo".

**Usos cobrados após a compra de SKUs do plano**

Esse cenário ocorre quando o cliente implantou serviços sob o número de registro errado ou selecionou os serviços incorretos.

Para validar se você está implantando sob o registro correto, verifique as informações das unidades incluídas na tabela de preços. Entre como um Administrador Corporativo e clique em **Relatórios** no painel de navegação à esquerda e selecione a guia **Tabela de preços**. Clique no ícone de Download no canto superior direito e encontre os números de parte do SKU do Plano correspondente com o filtro na coluna "Quantidade Incluída" e selecione valores maiores que "0".

Verifique se o seu plano do OMS está aparecendo na tabela de preços sob as unidades incluídas. Se não houver unidades incluídas para o plano do OMS em seu registro, seu plano do OMS poderá estar em outro registro. Entre em contato com o Suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Se as unidades incluídas para os serviços na tabela de preços não corresponderem ao que você implantou, por exemplo, Dados Premium Analisados de Insights Operacionais versus Dados Padrão Analisados de Insights Operacionais, significa que você pode ter implantado serviços que não têm cobertura do plano. Entre em contato com o Suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para obter ajuda.

**Serviços da SKU de plano provisionado no registro incorreto**

Se você tem vários registros e implantou serviços sob o número de registro errado, que não tem um plano do OMS, entre em contato com o suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Portal do EA do Azure, confira [Introdução ao Portal do EA do Azure](ea-portal-get-started.md).
- Os administradores do Portal do EA do Azure devem ler [Administração do Portal do EA do Azure](ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
