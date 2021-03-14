---
title: Como verificar buckets do Amazon S3
description: Este guia de instruções descreve os detalhes de como verificar os buckets do Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/07/2021
ms.custom: references_regions
ms.openlocfilehash: ddd5c5de85da5ae8cec9d24d33dfd2bf035b5b34
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438721"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Conector do Azure alcance para Amazon S3

Este guia de instruções fornece uma explicação de como usar o Azure alcance para verificar os dados não estruturados atualmente armazenados em buckets padrão do Amazon S3 e descobrir quais tipos de informações confidenciais existem em seus dados. Este guia de instruções também descreve como identificar os buckets do Amazon S3 nos quais os dados estão atualmente armazenados para facilitar a proteção de informações e a conformidade de dados.

Para esse serviço, use alcance para fornecer um conta Microsoft com acesso seguro ao AWS, onde o scanner do alcance será executado. O scanner alcance usa esse acesso aos buckets do Amazon S3 para ler seus dados e, em seguida, relata os resultados da verificação, incluindo apenas os metadados e a classificação, de volta ao Azure. Use a classificação alcance e os relatórios de rotulagem para analisar e revisar os resultados da verificação de dados.

Neste guia de instruções, você aprenderá como adicionar buckets do Amazon S3 como recursos de alcance e criar uma verificação para seus dados do Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Escopo de alcance para Amazon S3

O escopo a seguir é específico para o registro e a verificação de buckets do Amazon S3 como fontes de dados alcance.

|Escopo  |Descrição  |
|---------|---------|
|**Limites de dados**     |    O serviço de scanner do alcance atualmente dá suporte à verificação de buckets do Amazon S3 para até 100 GB de dados por locatário.     |
|**Tipos de arquivo**     | O serviço de scanner do alcance atualmente dá suporte aos seguintes tipos de arquivo: <br><br>. Avro,. csv,. doc,. docm,. docx,. dot,. JSON,. odp,. ods,. odt,. Orc,. parquet,. pdf,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. PSV,. SSV,. TSV,. txt,. xlc,. xls,. xlsb,. xlsm,. xlsx,. xlt,. xml        |
|**Regiões**     | O conector do alcance para o serviço Amazon S3 está atualmente implantado apenas nas regiões **AWS leste dos EUA (Ohio)** e **Europa (Frankfurt)** . <br><br>Para obter mais informações, consulte [regiões de armazenamento e verificação](#storage-and-scanning-regions).   |
|     |         |

Para obter mais informações, consulte os limites de alcance documentados em:

- [Gerenciar e aumentar cotas para recursos com o Azure alcance](how-to-manage-quotas.md)
- [Fontes de dados e tipos de arquivo com suporte no Azure alcance](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>Regiões de armazenamento e verificação

A tabela a seguir mapeia as regiões em que os dados são armazenados na região onde serão verificados pelo Azure alcance.

> [!IMPORTANT]
> Os clientes serão cobrados por todos os encargos de transferência de dados relacionados de acordo com a região de seu Bucket.
>

| Região de armazenamento | Região de verificação |
| ------------------------------- | ------------------------------------- |
| Leste dos EUA (Ohio)                  | Leste dos EUA (Ohio)                        |
| Leste dos EUA (N. Virgínia           | Leste dos EUA (Ohio)                        |
| Oeste dos EUA (N. California         | Leste dos EUA (Ohio)                        |
| Oeste dos EUA (Oregon)                | Leste dos EUA (Ohio)                        |
| África (cidade do cabo)              | Europa (Frankfurt)                    |
| Pacífico Asiático (Hong Kong)        | Europa (Frankfurt)                    |
| Pacífico Asiático (Mumbai)           | Europa (Frankfurt)                    |
| Pacífico Asiático (Osaka-local)      | Europa (Frankfurt)                    |
| Pacífico Asiático (Seul)            | Europa (Frankfurt)                    |
| Pacífico Asiático (Singapura)        | Europa (Frankfurt)                    |
| Pacífico Asiático (Sydney)           | Europa (Frankfurt)                    |
| Pacífico Asiático (Tóquio)            | Europa (Frankfurt)                    |
| Canadá (central)                | Leste dos EUA (Ohio)                        |
| China (Pequim)                 | Sem suporte                    |
| China (Ningxia)                 | Sem suporte                   |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Irlanda)                | Europa (Frankfurt)                    |
| Europa (Londres)                 | Europa (Frankfurt)                    |
| Europa (Milão)                  | Europa (Frankfurt)                    |
| Europa (Paris)                  | Europa (Frankfurt)                    |
| Europa (Estocolmo)              | Europa (Frankfurt)                    |
| Oriente Médio (Bahrein)           | Europa (Frankfurt)                    |
| América do Sul (São Paulo)       | Leste dos EUA (Ohio)                        |
| | |
## <a name="prerequisites"></a>Pré-requisitos

Verifique se você executou os seguintes pré-requisitos antes de adicionar os buckets do Amazon S3 como fontes de dados alcance e examinar seus dados S3.

- Você precisa ser um administrador de fonte de dados do Azure alcance.

- Ao adicionar seus buckets como recursos de alcance, você precisará dos valores de seu [AWS ARN](#retrieve-your-new-role-arn), do [nome do Bucket](#retrieve-your-amazon-s3-bucket-name)e, às vezes, da ID da conta do [AWS](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Criar uma conta do alcance

- **Se você já tiver uma conta do alcance,** poderá continuar com as configurações necessárias para o suporte do AWS S3. Comece com [criar uma credencial alcance para sua verificação de Bucket de AWS](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Se você precisar criar uma conta do alcance,** siga as instruções em [criar uma instância de conta do Azure alcance](create-catalog-portal.md). Depois de criar sua conta, volte aqui para concluir a configuração e comece a usar o conector do alcance para o Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Criar uma credencial alcance para a verificação do AWS Bucket

Este procedimento descreve como criar uma nova credencial alcance para usar ao verificar os buckets do AWS.

> [!TIP]
> Você também pode criar uma nova credencial no meio do processo, ao [configurar sua verificação](#create-a-scan-for-your-amazon-s3-bucket). Nesse caso, no campo **credencial** , selecione **novo**.
>

1. Em alcance, navegue até o **centro de gerenciamento** e, em **segurança e acesso**, selecione **credenciais**.

1. Selecione **novo** e, no painel **nova credencial** que aparece à direita, use os campos a seguir para criar sua credencial alcance:

    |Campo |Descrição  |
    |---------|---------|
    |**Nome**     |Insira um nome significativo para essa credencial ou use o padrão.        |
    |**Descrição**     |Insira uma descrição opcional para essa credencial, como `Used to scan the tutorial S3 buckets`         |
    |**Método de autenticação**     |Selecione a **função ARN**, já que você está usando uma função ARN para acessar seu Bucket.         |
    |**ID de conta Microsoft**     |Clique para copiar esse valor para a área de transferência. Use esse valor como a **ID de conta Microsoft** ao [criar sua função ARN em AWS](#create-a-new-aws-role-for-purview).           |
    |**ID Externa**     |Clique para copiar esse valor para a área de transferência. Use esse valor como a **ID externa** ao [criar sua função ARN em AWS.](#create-a-new-aws-role-for-purview)        |
    |**ARN da Função**     | Depois de [criar a função iam da Amazon](#create-a-new-aws-role-for-purview), navegue até sua função na área iam, copie o valor da **função ARN** e insira-o aqui. Por exemplo: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Para obter mais informações, consulte [recuperar sua nova função ARN](#retrieve-your-new-role-arn). |
    | | |

    Selecione **criar** quando terminar de concluir a criação da credencial.

Para obter mais informações sobre as credenciais do alcance, consulte a [documentação da visualização pública do Azure alcance](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Criar uma nova função AWS para alcance

1.  Abra o console do **Amazon Web Services** e, em **segurança, identidade e conformidade**, selecione **iam**.

1. Selecione **funções** e, em seguida, **criar função**.

1. Selecione **outra conta do AWS** e insira os seguintes valores:

    |Campo  |Descrição  |
    |---------|---------|
    |**ID da Conta**     |    Insira sua ID de conta da Microsoft. Por exemplo: `615019938638`     |
    |**ID Externa**     |   Em opções, selecione **exigir ID externa...** e, em seguida, insira sua ID externa no campo designado. <br>Por exemplo: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>Você pode encontrar essa ID externa quando você.  |
    | | |

    > [!NOTE]
    > Você pode encontrar os valores para a **ID da conta da Microsoft** e para a **ID externa** na área de credenciais do **centro de gerenciamento** do alcance  >   , em que você [criou suas credenciais do alcance](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Por exemplo:

    ![Adicione a ID da conta da Microsoft à sua conta do AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Na área **criar funções > anexar permissões** , filtre as permissões exibidas para **S3**. Selecione **AmazonS3ReadOnlyAccess** e, em seguida, selecione **próximo: marcas**.

    ![Selecione a política ReadOnlyAccess para a nova função de varredura do Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. Na área **Adicionar marcas (opcional)** , opcionalmente, você pode optar por criar uma marca significativa para essa nova função. Marcas úteis permitem organizar, controlar e controlar o acesso para cada função que você criar.

    Insira uma nova chave e valor para sua marca, conforme necessário. Quando terminar, ou se quiser ignorar esta etapa, selecione **Avançar: revisão** para examinar os detalhes da função e concluir a criação da função.

    ![Adicione uma marca significativa para organizar, controlar ou controlar o acesso à sua nova função.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Na área de **revisão** , faça o seguinte:

    - No campo **nome da função** , insira um nome significativo para sua função
    - Na caixa **Descrição da função** , insira uma descrição opcional para identificar a finalidade da função
    - Na seção **políticas** , confirme se a política correta (**AmazonS3ReadOnlyAccess**) está anexada à função.

    Em seguida, selecione **criar função** para concluir o processo.

    Por exemplo:

    ![Examine os detalhes antes de criar sua função.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Configurar a verificação de buckets do Amazon S3 criptografados

Os buckets AWS dão suporte a vários tipos de criptografia. Para buckets que usam criptografia **AWS-kms** , é necessária uma configuração especial para habilitar a verificação.

> [!NOTE]
> Para buckets que não usam criptografia, AES-256 ou AWS-KMS S3 Encryption, pule esta seção e continue a [recuperar o nome do Bucket S3 do Amazon](#retrieve-your-amazon-s3-bucket-name).
>

**Para verificar o tipo de criptografia usado em seus buckets do Amazon S3:**

1. Em AWS, navegue até **armazenamento**  >  **S3** > e selecione **buckets** no menu à esquerda.

    ![Selecione a guia buckets do Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Selecione o Bucket que você deseja verificar. Na página detalhes do Bucket, selecione a guia **Propriedades** e role para baixo até a área de **criptografia padrão** .

    - Se o Bucket selecionado estiver configurado para qualquer coisa, exceto a criptografia **AWS-kms** , incluindo se a criptografia padrão para o Bucket está **desabilitada**, ignore o restante deste procedimento e continue [recuperando o nome do Bucket S3 do Amazon](#retrieve-your-amazon-s3-bucket-name).

    - Se o Bucket selecionado estiver configurado para criptografia **AWS-kms** , continue conforme descrito abaixo para adicionar uma nova política que permita a verificação de um Bucket com criptografia **AWS-kms** personalizada.

    Por exemplo:

    ![Exibir um Bucket S3 do Amazon configurado com criptografia AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Para adicionar uma nova política para permitir a verificação de um Bucket com criptografia AWS-KMS personalizada:**

1. Em AWS, navegue até **Serviços**  >     >   **políticas** iam e selecione **criar política**.

1. Na guia **criar**  >  **Editor Visual** de política, defina sua política com os seguintes valores:

    |Campo  |Descrição  |
    |---------|---------|
    |**Serviço**     |  Insira e selecione **kms**.       |
    |**Ações**     | Em **nível de acesso**, selecione **gravar** para expandir a seção **gravar** .<br>Depois de expandido, selecione apenas a opção **descriptografar** .        |
    |**Recursos**     |Selecione um recurso específico ou **todos os recursos**.         |
    | | |

    Quando terminar, selecione **examinar política** para continuar.

    ![Crie uma política para verificar um Bucket com criptografia AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Na página **examinar política** , insira um nome significativo para a política e uma descrição opcional e, em seguida, selecione **criar política**.

    A política recém-criada é adicionada à sua lista de políticas.

1. Anexe sua nova política à função que você adicionou para verificação.

    1. Navegue de volta para a  >  página **funções** iam e selecione a função que você adicionou [anteriormente](#create-a-new-aws-role-for-purview).

    1. Na guia **permissões** , selecione **anexar políticas**.

        ![Na guia permissões da função, selecione Anexar políticas.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Na página **anexar permissões** , procure e selecione a nova política que você criou acima. Selecione **anexar política** para anexar a política à função.

        A página **Resumo** é atualizada, com a nova política anexada à sua função.

        ![Exiba uma página de resumo atualizada com a nova política anexada à sua função.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Recuperar sua nova função ARN

Você precisará registrar sua função AWS ARN e copiá-la para alcance ao [criar uma verificação para o Bucket S3 da Amazon](#create-a-scan-for-your-amazon-s3-bucket).

**Para recuperar sua função ARN:**

1. Na área de **funções iam (gerenciamento de acesso e identidade)** do AWS  >   , procure e selecione a nova função que você [criou para alcance](#create-a-purview-credential-for-your-aws-bucket-scan).

1. Na página **Resumo** da função, selecione o botão **copiar para área de transferência** à direita do valor da **função ARN** .

    ![Copie o valor da função ARN para a área de transferência.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Cole esse valor em um local seguro, pronto para uso ao [criar uma verificação para o Bucket S3 da Amazon](#create-a-scan-for-your-amazon-s3-bucket).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Recuperar o nome do Bucket S3 do Amazon

Você precisará do nome do seu Bucket do Amazon S3 para copiá-lo para alcance ao [criar uma verificação para o Bucket S3 da Amazon](#create-a-scan-for-your-amazon-s3-bucket)

**Para recuperar o nome do Bucket:**

1. Em AWS, navegue até **armazenamento**  >  **S3** > e selecione **buckets** no menu à esquerda.

    ![Exiba a guia buckets do Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Procure e selecione seu Bucket para exibir a página de detalhes do Bucket e, em seguida, copie o nome do Bucket para a área de transferência.

    Por exemplo:

    ![Recupere e copie a URL do Bucket S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Cole o nome do Bucket em um arquivo seguro e adicione um `s3://` prefixo a ele para criar o valor que você precisará inserir ao configurar o Bucket como um recurso alcance.

    Por exemplo: `s3://purview-tutorial-bucket`

> [!NOTE]
> Somente o nível raiz do Bucket tem suporte como uma fonte de dados alcance. Por exemplo, a URL a seguir, que inclui uma subpasta, *não* é suportada: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Localize sua ID da conta do AWS

Você precisará de sua ID de conta do AWS para registrar sua conta do AWS como uma fonte de dados alcance, junto com todos os seus buckets.

Sua ID de conta do AWS é a ID que você usa para fazer logon no console do AWS. Você também pode encontrá-lo quando estiver conectado no painel IAM, à esquerda, sob as opções de navegação, e na parte superior, como a parte numérica da URL de entrada:

Por exemplo:

![Recupere sua ID da conta do AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Adicionar um único Bucket S3 do Amazon como um recurso alcance

Use este procedimento se você tiver apenas um único Bucket S3 que deseja registrar no alcance como uma fonte de dados ou se tiver vários buckets em sua conta do AWS, mas não quiser registrar todos eles no alcance.

1. Inicie o portal do alcance usando o conector alcance dedicado para a URL do Amazon S3. Essa URL foi fornecida para você pela equipe de gerenciamento de produtos do Amazon S3 alcance Connector.

    ![Inicie o portal do alcance.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navegue até a página de **fontes** alcance do Azure e selecione **o** ![ ícone registrar registro.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Continuar**.

    ![Adicione um Bucket AWS do Amazon como uma fonte de dados alcance.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Se você tiver várias [coleções](manage-data-sources.md#manage-collections) e quiser adicionar o Amazon S3 a uma coleção específica, selecione a **exibição de mapa** na parte superior direita e, em seguida, selecione o ícone **registrar** ![ registro.](./media/register-scan-amazon-s3/register-button.png) botão dentro de sua coleção.
    >

1. No painel **registrar fontes (Amazon S3)** que é aberto, insira os seguintes detalhes:

    |Campo  |Descrição  |
    |---------|---------|
    |**Nome**     |Insira um nome significativo ou use o padrão fornecido.         |
    |**URL do Bucket**     | Insira a URL do Bucket AWS usando a seguinte sintaxe:   `s3://<bucketName>`     <br><br>**Observação**: Certifique-se de usar apenas o nível raiz do Bucket, sem nenhuma subpasta. Para obter mais informações, consulte [recuperar o nome do Bucket S3 do Amazon](#retrieve-your-amazon-s3-bucket-name). |
    |**Selecionar uma coleção** |Se você selecionou registrar uma fonte de dados de dentro de uma coleção, essa coleção já está listada. <br><br>Selecione uma coleção diferente, conforme necessário, **nenhuma** para atribuir nenhuma coleção ou **nova** para criar uma nova coleção agora. <br><br>Para obter mais informações sobre coleções alcance, consulte [gerenciar fontes de dados no Azure alcance](manage-data-sources.md#manage-collections).|
    | | |

    Quando terminar, selecione **concluir** para concluir o registro.

Continue com [criar uma verificação para o Bucket S3 da Amazon.](#create-a-scan-for-your-amazon-s3-bucket)

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>Adicionar todos os buckets do Amazon S3 como recursos de alcance

Use este procedimento se você tiver vários buckets S3 em sua conta do Amazon e quiser registrar todas as fontes de dados do as alcance.

1. Inicie o portal do alcance usando o conector alcance dedicado para a URL do Amazon S3. Essa URL foi fornecida para você pela equipe de gerenciamento de produtos do Amazon S3 alcance Connector.

    ![Iniciar o conector para o portal de alcance dedicado do Amazon S3](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navegue até a página de **fontes** alcance do Azure e selecione **o** ![ ícone registrar registro.](./media/register-scan-amazon-s3/register-button.png) > **Contas**  >  da Amazon **Continuar**.

    ![Adicione uma conta do Amazon como uma fonte de dados alcance.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Se você tiver várias [coleções](manage-data-sources.md#manage-collections) e quiser adicionar o Amazon S3 a uma coleção específica, selecione a **exibição de mapa** na parte superior direita e, em seguida, selecione o ícone **registrar** ![ registro.](./media/register-scan-amazon-s3/register-button.png) botão dentro de sua coleção.
    >

1. No painel **registrar fontes (Amazon S3)** que é aberto, insira os seguintes detalhes:

    |Campo  |Descrição  |
    |---------|---------|
    |**Nome**     |Insira um nome significativo ou use o padrão fornecido.         |
    |**ID da conta do AWS**     | Insira sua ID da conta do AWS. Para obter mais informações, consulte [localizar sua ID de conta do AWS](#locate-your-aws-account-id)|
    |**Selecionar uma coleção** |Se você selecionou registrar uma fonte de dados de dentro de uma coleção, essa coleção já está listada. <br><br>Selecione uma coleção diferente, conforme necessário, **nenhuma** para atribuir nenhuma coleção ou **nova** para criar uma nova coleção agora. <br><br>Para obter mais informações sobre coleções alcance, consulte [gerenciar fontes de dados no Azure alcance](manage-data-sources.md#manage-collections).|
    | | |

    Quando terminar, selecione **concluir** para concluir o registro.

Continue com [criar uma verificação para o Bucket S3 da Amazon](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Criar uma verificação para o Bucket S3 da Amazon

Depois de adicionar seus buckets como fontes de dados do alcance, você pode configurar uma verificação para ser executada em intervalos agendados ou imediatamente.

1. Navegue até a área de **fontes** alcance do Azure e siga um destes procedimentos:

    - No **modo de exibição de mapa**, selecione **novo** ícone Digitalizar ![ nova verificação.](./media/register-scan-amazon-s3/new-scan-button.png) na caixa fonte de dados.
    - No **modo de exibição de lista**, passe o mouse sobre a linha da fonte de dados e selecione novo ícone **digitalizar** ![ nova verificação. ](./media/register-scan-amazon-s3/new-scan-button.png) .

1. No painel **examinar...** que é aberto à direita, defina os campos a seguir e, em seguida, selecione **continuar**:

    |Campo  |Descrição  |
    |---------|---------|
    |**Nome**     |  Insira um nome significativo para sua verificação ou use o padrão.       |
    |**Tipo** |Exibido somente se você tiver adicionado sua conta do AWS, com todos os buckets incluídos. <br><br>As opções atuais incluem apenas **todos os**  >  **Amazon S3**. Fique atento para obter mais opções para selecionar à medida que a matriz de suporte do alcance se expande. |
    |**Credencial**     |  Selecione uma credencial alcance com sua função ARN. <br><br>**Dica**: se você quiser criar uma nova credencial neste momento, selecione **novo**. Para obter mais informações, consulte [criar uma credencial alcance para sua verificação de Bucket AWS](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    |     |         |

    O alcance verifica automaticamente se a função ARN é válida e se o Bucket e o objeto dentro do Bucket estão acessíveis e, em seguida, continua se a conexão for bem sucedido.

    > [!TIP]
    > Para inserir valores diferentes e testar a conexão você mesmo antes de continuar, selecione **testar conexão** na parte inferior direita antes de selecionar **continuar**.
    >

1. No painel **selecionar um conjunto de regras de verificação** , selecione o conjunto de regras padrão **AmazonS3** ou selecione **novo conjunto de regras de verificação** para criar um novo conjunto de regras personalizadas. Quando o conjunto de regras estiver selecionado, selecione **continuar**.

    Se você optar por criar um novo conjunto de regras de verificação personalizada, use o assistente para definir as seguintes configurações:

    |Painel  |Descrição  |
    |---------|---------|
    |**Novo conjunto de regras de verificação** /<br>**Descrição da regra de verificação**    |   Insira um nome significativo e uma descrição opcional para o conjunto de regras      |
    |**Selecionar tipos de arquivo**     | Selecione todos os tipos de arquivo que você deseja incluir na verificação e, em seguida, selecione **continuar**.<br><br>Para adicionar um novo tipo de arquivo, selecione **novo tipo de arquivo** e defina o seguinte: <br>-A extensão de arquivo que você deseja adicionar <br>-Uma descrição opcional  <br>-Se o conteúdo do arquivo tem um delimitador personalizado ou um tipo de arquivo do sistema. Em seguida, insira seu delimitador personalizado ou selecione o tipo de arquivo do sistema. <br><br>Selecione **criar** para criar seu tipo de arquivo personalizado.     |
    |**Selecionar regras de classificação**     |   Navegue até e selecione as regras de classificação que você deseja executar no conjunto de seus conjuntos de seus.      |
    |     |         |

    Selecione **criar** quando terminar de criar o conjunto de regras.

1. No painel **definir um gatilho de verificação** , selecione uma das opções a seguir e, em seguida, selecione **continuar**:

    - **Recorrência** para configurar uma agenda para uma verificação recorrente
    - **Uma vez** para configurar uma verificação que é iniciada imediatamente

1. No painel **examinar sua verificação** , verifique os detalhes da verificação para confirmar se estão corretos e, em seguida, selecione **salvar** ou **salvar e executar** se você selecionou **uma vez** no painel anterior.

    > [!NOTE]
    > Depois de iniciado, a verificação pode levar até 24 horas para ser concluída. Você poderá revisar seus **relatórios de insights** e pesquisar o catálogo 24 horas depois de iniciar cada verificação.
    >

Para obter mais informações, consulte [explorar resultados da verificação de alcance](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Explorar resultados da verificação de alcance

Quando uma verificação de alcance for concluída em seus buckets do Amazon S3, faça uma busca detalhada na área de **fontes** de alcance para exibir o histórico de verificação.

Selecione uma fonte de dados para exibir seus detalhes e, em seguida, selecione a guia **verificações** para exibir qualquer verificação em execução ou concluída no momento.
Se você tiver adicionado uma conta do AWS com vários buckets, o histórico de verificação de cada bucket será mostrado na conta.

Por exemplo:

![Mostre as verificações de Bucket AWS S3 na origem da conta do AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Use as outras áreas do alcance para descobrir detalhes sobre o conteúdo em seu estado de dados, incluindo os buckets do Amazon S3:

- **Pesquise o catálogo de dados alcance** e filtre um Bucket específico. Por exemplo:

    ![Pesquise os ativos AWS S3 no catálogo.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Exiba relatórios de insights** para exibir estatísticas para classificação, rótulos de sensibilidade, tipos de arquivo e mais detalhes sobre seu conteúdo.

    Todos os relatórios do alcance Insight incluem os resultados da varredura do Amazon S3, juntamente com o restante dos resultados de suas fontes de dados do Azure. Quando relevante, um tipo de ativo do **Amazon S3** adicional foi adicionado às opções de filtragem de relatório.

    Para obter mais informações, consulte [entender as ideias no Azure alcance](concept-insights.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os relatórios do Azure alcance Insight:

> [!div class="nextstepaction"]
> [Noções básicas de Insights no Azure Purview](concept-insights.md)
