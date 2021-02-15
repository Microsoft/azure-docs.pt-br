---
title: Copiar arquivos novos e alterados por LastModifiedDate
description: Saiba como usar um modelo de solução para copiar arquivos novos e alterados por LastModifiedDate com Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 94d09dc96d017dba3b16cc5fe113272a9393741d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362073"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiar arquivos novos e alterados por LastModifiedDate com Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que você pode usar para copiar arquivos novos e alterados somente pelo LastModifiedDate de um repositório baseado em arquivo para um repositório de destino. 

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo primeiro seleciona os arquivos novos e alterados somente por seus atributos **LastModifiedDate** e, em seguida, copia esses arquivos selecionados do repositório de fonte de dados para o repositório de destino de dados.

O modelo contém uma atividade:
- **Copiar** para copiar arquivos novos e alterados somente por LastModifiedDate de um repositório de arquivos para um repositório de destino.

O modelo define seis parâmetros:
-  *FolderPath_Source* é o caminho da pasta em que você pode ler os arquivos do repositório de origem. Você precisa substituir o valor padrão pelo seu próprio caminho de pasta.
-  *Directory_Source* é o caminho da subpasta onde você pode ler os arquivos do repositório de origem. Você precisa substituir o valor padrão pelo seu próprio caminho de subpasta.
-  *FolderPath_Destination* é o caminho da pasta onde você deseja copiar os arquivos para o repositório de destino. Você precisa substituir o valor padrão pelo seu próprio caminho de pasta.
-  *Directory_Destination* é o caminho da subpasta onde você deseja copiar os arquivos para o repositório de destino. Você precisa substituir o valor padrão pelo seu próprio caminho de subpasta.
-  *LastModified_From* é usado para selecionar os arquivos cujo atributo LastModifiedDate é posterior ou igual a esse valor DateTime.  Para selecionar somente os novos arquivos, que não foram copiados pela última vez, esse valor DateTime pode ser o horário em que o pipeline foi disparado pela última vez. Você pode substituir o valor padrão ' 2019-02-01T00:00:00Z ' para o seu LastModifiedDate esperado no fuso horário UTC. 
-  *LastModified_To* é usado para selecionar os arquivos cujo atributo LastModifiedDate é anterior a esse valor DateTime. Para selecionar somente os novos arquivos, que não foram copiados pela última vez, esse valor DateTime pode ser o tempo atual.  Você pode substituir o valor padrão ' 2019-02-01T00:00:00Z ' para o seu LastModifiedDate esperado no fuso horário UTC. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Ir para o modelo **copiar novos arquivos somente por LastModifiedDate**. Crie uma **nova** conexão com o repositório de armazenamento de origem. O repositório de armazenamento de origem do qual você deseja copiar arquivos.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Crie uma **nova** conexão com o repositório de destino. O repositório de destino é onde você deseja copiar arquivos. 

    ![Criar uma nova conexão para o destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Você verá o pipeline disponível no painel, conforme mostrado no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selecione **depurar**, grave o valor para os **parâmetros** e selecione **concluir**.  Na imagem abaixo, definimos os parâmetros como a seguir.
   - **FolderPath_Source** = SourceFolder
   - **Directory_Source** = subpasta
   - **FolderPath_Destination** = DestinationFolder
   - **Directory_Destination** = subpasta
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    O exemplo indica que os arquivos, que foram modificados pela última vez dentro do TimeSpan (**2019-02-01T00:00:00Z** para **2019-03-01T00:00:00Z**) serão copiados do caminho de origem **SourceFolder/subpasta** para o caminho de destino **DestinationFolder/subpasta**.  Você pode substituí-los por seus próprios parâmetros.

    ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Examine o resultado. Você verá que somente os arquivos modificados pela última vez no TimeSpan configurado foram copiados para o repositório de destino.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Agora você pode adicionar um gatilho do Windows em cascata para automatizar esse pipeline, para que o pipeline sempre possa copiar arquivos novos e alterados somente pelo LastModifiedDate periodicamente.  Selecione **Adicionar gatilho** e selecione **novo/editar**.

    ![Captura de tela que realça a opção de menu novo/editar que aparece quando você seleciona adicionar gatilho.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Na janela **Adicionar Gatilhos**, selecione **+ Novo**.

9. Selecione **janela em cascata** para o tipo de gatilho, definido a **cada 15 minutos** como a recorrência (você pode alterar para qualquer hora de intervalo). Selecione **Sim** para a caixa ativada e, em seguida, selecione **OK**.

    ![Criar gatilho](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Defina o valor para os **parâmetros de execução do gatilho** conforme a seguir e selecione **concluir**.
    - **FolderPath_Source**  =  **SourceFolder**.  Você pode substituir por sua pasta no armazenamento de dados de origem.
    - **Directory_Source**  =  **subpasta**.  Você pode substituir pela subpasta no armazenamento de dados de origem.
    - **FolderPath_Destination**  =  **DestinationFolder**.  Você pode substituir por sua pasta no armazenamento de dados de destino.
    - **Directory_Destination**  =  **subpasta**.  Você pode substituir pela subpasta no armazenamento de dados de destino.
    - **LastModified_From**  =   **\@ Trigger (). Outputs. windowStartTime**.  É uma variável de sistema do gatilho que determina a hora em que o pipeline foi disparado pela última vez.
    - **LastModified_To**  =  **\@ Trigger (). Outputs. windowEndTime**.  É uma variável de sistema do gatilho que determina a hora em que o pipeline é disparado desta vez.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Crie novos arquivos na pasta de origem do repositório de fonte de dados.  Agora você está aguardando que o pipeline seja disparado automaticamente e somente os novos arquivos serão copiados para o repositório de destino.

13. Selecione a guia **Monitor** no painel de navegação à esquerda e aguarde cerca de 15 minutos se a recorrência do gatilho tiver sido definida para a cada 15 minutos. 

14. Examine o resultado. Você verá que seu pipeline será disparado automaticamente a cada 15 minutos e somente os arquivos novos ou alterados do repositório de origem serão copiados para o repositório de destino em cada execução de pipeline.

    ![Captura de tela que mostra os resultados que retornam quando o pipeline é disparado.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
