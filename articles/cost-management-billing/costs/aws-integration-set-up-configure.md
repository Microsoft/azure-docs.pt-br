---
title: Configurar a integração da AWS com o Gerenciamento de Custos do Azure
description: Este artigo explica como preparar e configurar a integração do relatório de Custo e Uso da AWS com o Gerenciamento de Custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2b8a008decc41a5686fb2c8d9fee271f95f0fef3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122416"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Preparar e configurar a integração do relatório de Custo e Uso da AWS

Com a integração do CUR (relatório de custo e uso) da AWS (Amazon Web Services), você monitora e controla seus gastos com a AWS no Gerenciamento de Custos do Azure. A integração possibilita uma localização no portal do Azure em que você monitora e controla os gastos com o Azure e a AWS. Este artigo explica como preparar a integração e configurá-la para que você possa usar os recursos do Gerenciamento de Custos do Azure para analisar os custos e examinar os orçamentos.

O Gerenciamento de Custos processa o relatório de Custo e Uso da AWS armazenado em um bucket do S3, usando suas credenciais de acesso da AWS para obter definições de relatório e baixar arquivos CSV GZIP de relatório.

Assista ao vídeo [Como configurar conectores para AWS no Gerenciamento de Custos](https://www.youtube.com/watch?v=Jg5KC1cx5cA) para saber mais sobre como configurar a integração de relatórios da AWS. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Criar um relatório de custo e uso na AWS

Usar um relatório de custo e uso é a maneira recomendada pela AWS de coletar e processar os custos com a AWS. Para obter mais informações, veja a documentação [Relatório de custo e uso da AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Use a página **Relatórios de Custo de Uso** do console de Gerenciamento de Custos e Cobrança na AWS para criar um relatório de custo e uso com as seguintes etapas:

1. Entre no Console de Gerenciamento da AWS e abra o [Console de Gerenciamento de Custos e Cobrança](https://console.aws.amazon.com/billing).
2. No painel de navegação, selecione **Relatórios de Custo e Uso**.
3. Selecione **Criar relatório**.
4. Em **Nome do relatório**, insira um nome para o relatório.
5. Em **Detalhes adicionais do relatório**, selecione **Incluir IDs do recurso**.
6. Para **Configurações de atualização de dados**, selecione se deseja que o relatório de Custo e Uso da AWS seja atualizado se a AWS aplicar reembolsos, créditos ou taxas de suporte à sua conta depois de finalizar a sua fatura. Quando um relatório é atualizado, um novo relatório é carregado no Amazon S3. Recomendamos que você deixe a configuração selecionada.
7. Selecione **Avançar**.
8. Para o **bucket do S3**, escolha **Configurar**.
9. Na caixa de diálogo Configurar Bucket S3, insira um nome de bucket e a região em que você deseja criar um bucket e escolha **Avançar**.
10. Selecione **Eu confirmei que essa política está correta** e clique em **Salvar**.
11. (Opcional) Para o prefixo do caminho do relatório, insira o prefixo do caminho do relatório que você deseja que seja anexado ao nome do relatório.
Se você não especificar um prefixo, o prefixo padrão será o nome que você especificou para o relatório. O intervalo de datas tem o formato `/report-name/date-range/`.
12. Para **Unidade de tempo**, escolha **Por hora**.
13. Para **Controle de versão de relatório**, escolha se deseja que cada versão do relatório substitua a versão anterior ou se deseja novos relatórios adicionais.
14. Em **Habilitar a integração de dados para**, nenhuma seleção é necessária.
15. Em **Compressão**, selecione **GZIP**.
16. Selecione **Avançar**.
17. Depois de examinar as configurações do relatório, selecione **Examinar e Concluir**.

    Tome nota do nome do relatório. Você o usará em etapas posteriores.

Pode levar até 24 horas para a AWS começar a entregar relatórios ao seu bucket do Amazon S3. Após o início da entrega, a AWS atualiza os arquivos de relatório de Custo e Uso da AWS pelo menos uma vez por dia. Você pode continuar configurando seu ambiente da AWS sem esperar que a entrega seja iniciada.

## <a name="create-a-role-and-policy-in-aws"></a>Criar uma função e uma política na AWS

O Gerenciamento de Custos do Azure acessa várias vezes por dia o bucket do S3 em que o relatório de Custo e Uso está localizado. O serviço precisa acessar as credenciais para verificar se há novos dados. Você cria uma função e uma política na AWS para permitir que o Gerenciamento de Custos o acesse.

Para habilitar o acesso baseado em função a uma conta da AWS no Gerenciamento de Custos, a função é criada no console da AWS. Você precisa ter o _ARN da função_ e a _ID externa_ do console da AWS. Posteriormente, use-os na página **Criar um conector da AWS** no Gerenciamento de Custos.

Use o assistente Criar Uma Função:

1. Entre no console da AWS e selecione **Serviços**.
2. Na lista de serviços, selecione **IAM**.
3. Selecione **Funções** e depois **Criar Função**.
4. Na próxima página, selecione **Outra conta da AWS**.
5. Em **ID da conta**, insira **432263259397**.
6. Em **Opções**, selecione **Exigir ID externa (melhor prática caso um terceiro vá assumir essa função)** .
7. Em **ID Externa**, insira a ID externa, que é uma senha compartilhada entre a função da AWS e o Gerenciamento de Custos do Azure. A mesma ID externa também é usada na página **Novo Conector** no Gerenciamento de Custos. A Microsoft recomenda que você use uma política de senha forte ao inserir a ID externa.
    > [!NOTE]
    > Não altere a seleção para **Exigir MFA**. Ele deve permanecer limpo.
8. Selecione **Avançar: Permissões.**
9. Selecione **Criar política**. Uma nova guia do navegador é aberta. É aí que você cria uma política.
10. Selecione **Escolher um serviço**.

Configure a permissão para o relatório de Custo e Uso:

1. Insira **Relatório de Custo e Uso**.
2. Selecione **Nível de acesso** > **Leitura** > **DescribeReportDefinitions**. Esta etapa permite que o Gerenciamento de Custos leia quais relatórios de CUR estão definidos e determine se eles correspondem ao pré-requisito de definição de relatório.
3. Selecione **Adicionar permissões adicionais**.

Configure a permissão para os seus objetos e bucket do S3:

1. Selecione **Escolher um serviço**.
2. Insira **S3**.
3. Selecione **Nível de acesso** > **Listar** > **ListBucket**. Esta ação obtém a lista de objetos no bucket do S3.
4. Selecione **Nível de acesso** > **Leitura** > **GetObject**. Essa ação permite o download de arquivos de cobrança.
5. Selecione **Recursos**.
6. Selecione **Bucket – Adicionar ARN**.
7. No **Nome do bucket**, insira o bucket usado para armazenar os arquivos CUR.
8. Selecione **objeto – Adicionar ARN**.
9. No **Nome do bucket**, insira o bucket usado para armazenar os arquivos CUR.
10. Em **Nome do objeto**, selecione **Qualquer**.
11. Selecione **Adicionar permissões adicionais**.

Configurar permissão para o Explorador de Custos:

1. Selecione **Escolher um serviço**.
2. Insira **Serviço Explorador de Custos**.
3. Selecione **Todas as ações do Serviço Explorador de Custos (ce:\*)** . Esta ação valida que a coleção está correta.
4. Selecione **Adicionar permissões adicionais**.

Adicionar permissão para organizações da AWS:

1. Insira **Organizações**.
2. Selecione **Nível de acesso** > **Listar** > **ListAccounts**. Essa ação obtém os nomes das contas.
3. Em **Examinar Política**, insira um nome para a nova política. Verifique se você inseriu as informações corretas e, em seguida, selecione **Criar Política**.
4. Volte para a guia anterior e atualize a página da Web do navegador. Na barra de pesquisa, pesquise pela nova política.
5. Selecione **Avançar: Análise**.
6. Insira um nome para a nova função. Verifique se você inseriu as informações corretas e, em seguida, selecione **Criar Função**.

    Tome nota do ARN da função e da ID externa usados nas etapas anteriores quando você criou a função. Você os usará posteriormente quando configurar o conector do Gerenciamento de Custos do Azure.

O JSON da política deve ser semelhante ao exemplo a seguir. Substitua _bucketname_ pelo nome do bucket do S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Configurar um novo conector para a AWS no Azure

Use as seguintes informações para criar um conector da AWS e começar a monitorar seus custos de AWS:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Acesse a Página Inicial do Azure clicando em **Página Inicial** no menu à esquerda (ícone do menu de "hambúrguer" com três linhas).
3. Acesse **Ferramentas** > **Gerenciamento de Custos** na parte inferior da página.
3. Em **Configurações**, selecione **Conectores da AWS**.  
4. Selecione **+Adicionar** na parte superior da página para criar um conector.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Exemplo mostrando a configuração de Conectores da AWS" :::
1. Na página **Criar conector**, em **Nome de exibição**, insira um nome para o conector.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Exemplo da página para criar um conector da AWS" :::
1. Opcionalmente, selecione o grupo de gerenciamento padrão. Ele armazenará todas as contas vinculadas descobertas. Você pode configurá-lo mais tarde.
1. Na seção **Cobrança**, selecione **Renovação Automática** como **Ativado** se desejar garantir a operação contínua. Se você selecionar a opção automática, deverá selecionar uma assinatura para cobrança.
1. Para o **ARN da função**, insira o valor que você usou ao configurar a função na AWS.
1. Para a **ID Externa**, insira o valor que você usou ao configurar a função na AWS.
1. Para **Nome do Relatório**, insira o nome que você criou na AWS.
1. Selecione **Avançar** e, em seguida, selecione **Criar**.

Pode levar algumas horas para que os novos escopos da AWS, a conta consolidada da AWS, as contas vinculadas da AWS e os respectivos dados de custo sejam exibidos.

Depois de criar o conector, recomendamos que você atribua o controle de acesso a ele. Os usuários recebem permissões para os escopos recém-descobertos: a conta consolidada da AWS e as contas vinculadas da AWS. O usuário que cria o conector é o proprietário do conector, da conta consolidada e de todas as contas vinculadas.

A atribuição de permissões de conector a usuários após a ocorrência da descoberta não atribui permissões aos escopos da AWS existentes. Em vez disso, as permissões são atribuídas somente às novas contas vinculadas.

## <a name="take-additional-steps"></a>Realiza etapas adicionais

- [Configure grupos de gerenciamento](../../governance/management-groups/overview.md#initial-setup-of-management-groups), se ainda não tiver feito isso.
- Verifique se novos escopos são adicionados ao seu seletor de escopo. Selecione **Atualizar** para exibir os dados mais recentes.
- Na página **Conectores de nuvem**, selecione o conector e selecione **Ir para a conta de cobrança** para atribuir a conta vinculada a grupos de gerenciamento.

> [!NOTE]
> Atualmente, não há suporte para os grupos de gerenciamento nos clientes do MCA (Contrato de Cliente da Microsoft). Os clientes do MCA podem criar o conector e ver os respectivos dados da AWS. No entanto, os clientes do MCA não podem ver os custos do Azure e os custos da AWS juntos em um grupo de gerenciamento.

## <a name="manage-aws-connectors"></a>Gerenciar os conectores da AWS

Ao selecionar um conector na página **Conectores da AWS**, você poderá:

- Selecione **Ir para a Conta de Cobrança** para exibir informações para a conta consolidada da AWS.
- Selecione **Controle de Acesso** para gerenciar a atribuição de função para o conector.
- Selecione **Editar** para atualizar o conector. Não é possível alterar o número da conta da AWS, pois ele aparece no ARN da função. Mas você pode criar um conector.
- Selecione **Verificar** para executar novamente o teste de verificação a fim de garantir que o Gerenciamento de Custos possa coletar dados usando as configurações do conector.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Detalhes do exemplo de conector da AWS" :::

## <a name="set-up-azure-management-groups"></a>Configurar grupos de gerenciamento do Azure

Coloque suas assinaturas do Azure e as contas vinculadas da AWS no mesmo grupo de gerenciamento para criar uma localização em que você possa ver as informações do provedor entre nuvens. Se você ainda não tiver configurado seu ambiente do Azure com grupos de gerenciamento, veja [Configuração inicial dos grupos de gerenciamento](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Se você quiser separar os custos, poderá criar um grupo de gerenciamento que contenha apenas contas vinculadas da AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Configurar uma conta consolidada da AWS

A conta consolidada da AWS combina cobrança e pagamento para várias contas da AWS. Ela também atua como uma conta vinculada da AWS. Veja os detalhes da sua conta consolidada da AWS usando o link na página do conector da AWS. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Exemplo de detalhes de uma conta consolidada da AWS" :::

Nessa página, você pode:

- Selecionar **Atualizar** para atualizar em massa a associação de contas vinculadas da AWS a um grupo de gerenciamento.
- Selecione **Controle de Acesso** para definir a atribuição de função para o escopo.

### <a name="permissions-for-an-aws-consolidated-account"></a>Permissões para uma conta consolidada da AWS

Por padrão, as permissões para uma conta consolidada da AWS são definidas na criação da conta, com base nas permissões do conector da AWS. O criador do conector é o proprietário.

Você gerencia o nível de acesso usando a página **Nível de Acesso** da conta consolidada da AWS. No entanto, as contas vinculadas da AWS não herdam permissões da conta consolidada da AWS.

## <a name="set-up-an-aws-linked-account"></a>Definir uma conta vinculada da AWS

A conta vinculada da AWS é onde os recursos da AWS são criados e gerenciados. Uma conta vinculada também age como um limite de segurança.

Nesta página, você pode:

- Selecionar **Atualizar** para atualizar a associação de uma conta vinculada da AWS a um grupo de gerenciamento.
- Selecionar **Controle de Acesso** para definir uma atribuição de função para o escopo.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Exemplo da página da Conta Vinculada da AWS" :::

### <a name="permissions-for-an-aws-linked-account"></a>Permissões para uma conta vinculada da AWS

Por padrão, as permissões para uma conta vinculada da AWS são definidas na criação, com base nas permissões do conector da AWS. O criador do conector é o proprietário. Você gerencia o nível de acesso usando a página **Nível de Acesso** da conta vinculada da AWS. As contas vinculadas da AWS não herdam permissões de uma conta consolidada da AWS.

As contas vinculadas da AWS sempre herdam as permissões do grupo de gerenciamento ao qual pertencem.

## <a name="next-steps"></a>Próximas etapas

- Agora que você configurou e configurou a integração de relatório de Custo e Uso da AWS, continue a [Gerenciar os custos e o uso da AWS](aws-integration-manage.md).
- Se você ainda não estiver familiarizado com a análise de custo, veja o início rápido [Explorar e analisar os custos com a análise de custo](quick-acm-cost-analysis.md).
- Se você não estiver familiarizado com orçamentos no Azure, veja [Criar e gerenciar orçamentos do Azure](tutorial-acm-create-budgets.md).
