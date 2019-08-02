---
title: Tutorial para copiar dados para o dispositivo do Azure Data Box por meio do serviço de cópia de dados | Microsoft Docs
description: Neste tutorial, você aprenderá a copiar dados para o dispositivo Azure Data Box por meio do serviço de cópia de dados
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: a8a8b9d872860425be721515a7087085acf12065
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206057"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Tutorial: Usar o serviço de cópia de dados para copiar dados para o Azure Data Box (versão prévia)

Este tutorial descreve como ingerir dados usando o serviço de cópia de dados sem um host intermediário. O serviço de cópia de dados é executado localmente no Microsoft Azure Data Box, conecta-se ao dispositivo NAS por meio do SMB e copia os dados para o Data Box.

Use o serviço de cópia de dados:

- Em ambientes NAS, nos quais os hosts intermediários podem não estar disponíveis.
- Com arquivos pequenos que levam semanas para a ingestão e para o upload dos dados. O serviço de cópia de dados melhora consideravelmente o tempo de ingestão e upload de arquivos pequenos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Copiar dados para caixa de dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu este tutorial: [Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu seu dispositivo Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem as credenciais do dispositivo NAS de origem ao qual se conectará para a cópia de dados.
4. Você está conectado a uma rede de alta velocidade. Recomendamos expressamente que você tenha, pelo menos, uma conexão de 10 GbE (Gigabits Ethernet). Se uma conexão de 10 GbE não estiver disponível, você poderá usar um vínculo de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="copy-data-to-data-box"></a>Copiar dados para caixa de dados

Depois que você estiver conectado ao dispositivo NAS, a próxima etapa será copiar os dados. Antes de começar a cópia de dados, examine as considerações a seguir:

- Durante a cópia de dados, verifique se o tamanho dos dados está em conformidade com os limites de tamanho descritos no artigo [Armazenamento do Azure e limites do Data Box](data-box-limits.md).
- Se os dados carregados pelo Data Box forem carregados simultaneamente por outros aplicativos fora do Data Box, isso poderá resultar em falhas no trabalho de upload e em dados corrompidos.
- Se os dados estiverem sendo modificados enquanto o serviço de cópia de dados os estiver lendo, poderão ocorrer falhas ou dados corrompidos.

Para copiar dados usando o serviço de cópia de dados, é necessário criar um trabalho:

1. Na IU da Web local do dispositivo Data Box, acesse **Gerenciar** > **Copiar dados**.
2. Na página **Copiar dados**, selecione **Criar**.

    ![Selecionar Criar na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Na caixa de diálogo **Configurar trabalho e iniciar**, preencha os seguintes campos:
    
    |Campo                          |Valor    |
    |-------------------------------|---------|
    |**Nome do trabalho**                       |Um único nome com menos de 230 caracteres para o trabalho. Estes caracteres não são permitidos no nome do trabalho: \<, \>, \|, \?, \*, \\, \:, \/ e \\\.         |
    |**Localização de origem**                |Forneça o caminho do SMB para a fonte de dados no formato: `\\<ServerIPAddress>\<ShareName>` ou `\\<ServerName>\<ShareName>`.        |
    |**Nome de Usuário**                       |Nome de usuário no formato `\\<DomainName><UserName>` para acessar a fonte de dados. Se um administrador local estiver se conectando, eles precisarão de permissões de segurança explícita. Clique com botão direito do mouse na pasta, selecione **Propriedades** e, em seguida, selecione **Segurança**. Isso deve adicionar o administrador local na guia **Segurança**.       |
    |**Senha**                       |A senha para acessar a fonte de dados.           |
    |**Conta de armazenamento de destino**    |Selecione na lista a conta de armazenamento de destino na qual os dados serão carregados.         |
    |**Tipo de destino**       |Selecione o tipo de armazenamento de destino na lista: **Blob de Blocos**, **Blob de Páginas** ou **Arquivos do Azure**.        |
    |**Contêiner/compartilhamento de destino**    |Insira o nome do contêiner ou do compartilhamento no qual deseja carregar os dados em sua conta de armazenamento de destino. O nome pode ser um nome do compartilhamento ou um nome de contêiner. Por exemplo, use `myshare` ou `mycontainer`. Insira também o nome no formato `sharename\directory_name` ou `containername\virtual_directory_name`.        |
    |**Copiar arquivos que correspondem a um padrão**    | Você pode inserir o nome do arquivo que corresponde a um padrão das duas seguintes maneiras:<ul><li>**Usar expressões curinga:** Há suporte apenas para `*` e `?` em expressões curinga. Por exemplo, a expressão `*.vhd` corresponde a todos os arquivos que têm a extensão `.vhd`. Da mesma forma, `*.dl?` corresponde a todos os arquivos com a extensão `.dl` ou que começam com `.dl`, como `.dll`. Da mesma forma, `*foo` corresponde a todos os arquivos cujos nomes terminam com `foo`.<br>É possível inserir a expressão curinga diretamente no campo. Por padrão, o valor inserido no campo é tratado como uma expressão curinga.</li><li>**Usar expressões regulares:** Há suporte para expressões regulares baseadas em POSIX. Por exemplo, a expressão regular `.*\.vhd` corresponderá a todos os arquivos que têm a extensão `.vhd`. Para expressões regulares, forneça o `<pattern>` diretamente como `regex(<pattern>)`. Para obter mais informações sobre expressões regulares, acesse [Linguagem de expressões regulares – referência rápida](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Otimização de arquivo**              |Quando esse recurso é habilitado, os arquivos menores que 1 MB são empacotados durante a ingestão. Esse empacotamento agiliza a cópia de dados para arquivos pequenos. Ele também economiza uma quantidade de tempo significativa quando o número de arquivos é muito maior do que o número de diretórios.        |
 
4. Selecione **Iniciar**. As entradas são validadas e, se a validação é bem-sucedida, o trabalho é iniciado. Poderá levar alguns minutos para o trabalho ser iniciado.

    ![Iniciar um trabalho por meio da caixa de diálogo "Configurar trabalho e iniciar"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. É criado um trabalho com as configurações especificadas. Você pode pausar, retomar, cancelar ou reiniciar um trabalho. Marque a caixa de seleção ao lado do nome do trabalho e, em seguida, selecione o botão apropriado.

    ![Gerenciar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Você pode pausar um trabalho se ele está afetando os recursos do dispositivo NAS durante o horário de pico:

        ![Pausar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Você pode retomar o trabalho posteriormente, fora do horário de pico:

        ![Retomar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Você pode cancelar um trabalho a qualquer momento:

        ![Cancelar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Quando você cancela um trabalho, uma confirmação é necessária:

        ![Confirmar cancelamento do trabalho](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se você decidir cancelar um trabalho, os dados que já foram copiados não serão excluídos. Para excluir os dados copiados para o dispositivo Data Box, redefina o dispositivo.

        ![Redefinir um dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se você cancelar ou pausar um trabalho, os arquivos grandes poderão ser apenas parcialmente copiados. Esses arquivos parcialmente copiados são carregados no mesmo estado para o Azure. Ao cancelar ou pausar um trabalho, verifique se os arquivos foram copiados corretamente. Para validar os arquivos, examine os compartilhamentos SMB ou baixe o arquivo BOM.

    - Você pode reiniciar um trabalho em caso de falha devido a um erro transitório, como uma falha de rede. No entanto, você não poderá reiniciar um trabalho se ele tiver atingido um status de terminal, como **Com êxito** ou **Concluído com erros**. Falhas de trabalho podem ser causadas por problemas de nomenclatura ou de tamanho do arquivo. Esses erros são registrados em log, mas o trabalho não pode ser reiniciado depois de concluído.

        ![Reiniciar um trabalho com falha](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se ocorrer uma falha e você não puder reiniciar o trabalho, baixe os logs de erros e examine a falha nos arquivos de log. Depois de corrigir o problema, crie um trabalho para copiar os arquivos. Também é possível [copiar os arquivos via SMB](data-box-deploy-copy-data.md).
    
    - Nesta versão, não é possível excluir um trabalho.
    
    - Você pode criar trabalhos ilimitados, mas só pode executar um máximo de 10 trabalhos em paralelo por vez.
    - Se a **Otimização de arquivo** estiver ativada, os arquivos pequenos serão empacotados na ingestão para melhorar o desempenho da cópia. Nesses casos, você verá um arquivo empacotado (ele terá um GUID como o nome de arquivo). Não exclua esse arquivo. Ele será desempacotado durante o upload.

6. Enquanto o trabalho está em andamento, na página **Copiar dados**:

    - Na coluna **Status**, é possível exibir o status do trabalho de cópia. O status pode ser:
        - **Executando**
        - **Com falha**
        - **Êxito**
        - **Pausando**
        - **Em pausa**
        - **Cancelando**
        - **Cancelado**
        - **Concluído com erros**
    - Na coluna **Arquivos**, é possível ver o número e o tamanho total dos arquivos que estão sendo copiados.
    - Na coluna **Processados**, é possível ver o número e o tamanho total dos arquivos processados.
    - Na coluna **Detalhes do trabalho**, selecione **Exibir** para ver os detalhes do trabalho.
    - Se ocorrerem erros durante o processo de cópia, conforme mostrado na coluna **Nº de Erros**, acesse a coluna **Log de erros** e baixe os logs de erros para solução de problemas.

Aguarde a conclusão do trabalho de cópia. Como alguns erros são registrados em log apenas na página **Conectar e copiar**, verifique se os trabalhos de cópia foram concluídos sem erros antes de passar para a próxima etapa.

![Nenhum erro na página "Conectar e copiar"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Para garantir a integridade dos dados, uma soma de verificação é computada embutida à medida que os dados são copiados. Depois que a cópia for concluída, selecione **Exibir painel** para verificar o espaço usado e o espaço livre no dispositivo.
    
![Verificar o espaço livre e usado no painel](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Após a conclusão do trabalho de cópia, você poderá selecionar **Preparar para o envio**.

>[!NOTE]
> A **Preparação para o envio** não poderá ser executada enquanto os trabalhos de cópia estiverem em andamento.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo tutorial para saber como enviar o dispositivo Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Enviar o dispositivo Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

