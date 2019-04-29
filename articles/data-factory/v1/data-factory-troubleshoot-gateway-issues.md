---
title: Solucionar problemas do Gateway de Gerenciamento de Dados | Microsoft Docs
description: Fornece dicas para solucionar problemas relacionados ao Gateway de Gerenciamento de Dados.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0559d89bd691323a95713d518df05e58283cef39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61252238"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Solucionar problemas usando o Gateway de Gerenciamento de Dados
Este artigo fornece informações sobre como solucionar problemas com o uso do Gateway de Gerenciamento de Dados.

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [IR auto-hospedado no Data Factory](../create-self-hosted-integration-runtime.md).

Consulte o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para saber mais sobre o gateway. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter um passo a passo de como mover dados de um banco de dados SQL Server local para um armazenamento de blobs do Microsoft Azure usando o gateway.

## <a name="failed-to-install-or-register-gateway"></a>Falha ao instalar ou registrar o gateway
### <a name="1-problem"></a>1. Problema
Essa mensagem de erro é exibida ao instalar e registrar um gateway, especificamente, ao baixar o arquivo de instalação do gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
A máquina na qual você está tentando instalar o gateway falha ao baixar o arquivo de instalação do gateway mais recente do Centro de download devido a um problema de rede.

#### <a name="resolution"></a>Resolução
Verifique suas configurações de firewall/servidor proxy para ver se elas bloqueiam a conexão de rede no computador com o [centro de download](https://download.microsoft.com/) e atualize as configurações de acordo.

Como alternativa, você pode baixar o arquivo de instalação para o gateway mais recente do [Centro de download](https://www.microsoft.com/download/details.aspx?id=39717) em outras máquinas que podem acessar o centro de download. Em seguida, você pode copiar o arquivo instalador para o computador host do gateway e executá-lo manualmente para instalar e atualizar o gateway.

### <a name="2-problem"></a>2. Problema
Esse erro é exibido ao tentar instalar um gateway clicando em **Instalar diretamente neste computador** no Portal do Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Um gateway já está instalado no computador.

#### <a name="resolution"></a>Resolução
Desinstale o gateway existente no computador e clique no link **Instalar diretamente neste computador** novamente.

### <a name="3-problem"></a>3. Problema
Esse erro poderá ser exibido ao registrar um novo gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Esta mensagem poderá ser exibida por um dos seguintes motivos:

* O formato da chave do gateway é inválido.
* A chave do gateway foi invalidada.
* A chave do gateway foi regenerada no portal.  

#### <a name="resolution"></a>Resolução
Verifique se você está usando a chave de gateway correta do portal. Se necessário, regenere uma chave e use-a para registrar o gateway.

### <a name="4-problem"></a>4. Problema
A seguinte mensagem de erro poderá ser exibida ao registrar um gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![O conteúdo ou o formato da chave é inválido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
O conteúdo ou o formato da chave do gateway de entrada está incorreto. Um dos motivos pode ser que você copiou apenas parte da chave do portal ou está usando uma chave inválida.

#### <a name="resolution"></a>Resolução
Gere uma chave de gateway no portal e use o botão para copiar a chave completa. Em seguida, cole-a nessa janela para registrar o gateway.

### <a name="5-problem"></a>5. Problema
A seguinte mensagem de erro poderá ser exibida ao registrar um gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![A chave do gateway é inválida ou está vazia](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
A chave do gateway foi regenerada ou o gateway foi excluído do portal do Azure. Isso também pode ocorrer se a configuração do Gateway de Gerenciamento de Dados não for a mais recente.

#### <a name="resolution"></a>Resolução
Verifique se a configuração do Gateway de Gerenciamento de Dados é a versão mais recente. Encontre a versão mais recente no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Se a configuração for a atual/mais recente e o gateway ainda existir no Portal, gere novamente a chave do gateway no Portal do Azure e use o botão Copiar para copiar a chave inteira, e cole-a nesta janela para registrar o gateway. Caso contrário, recrie o gateway e comece novamente.

### <a name="6-problem"></a>6. Problema
A seguinte mensagem de erro poderá ser exibida ao registrar um gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![A chave do gateway é inválida ou está vazia](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Esse erro pode ocorrer porque o gateway foi excluído ou a chave do gateway associada foi regenerada.

#### <a name="resolution"></a>Resolução
Se o gateway foi excluído, recrie o gateway no portal, clique em **Registrar**, copie a chave do portal, cole-a e tente registrar o gateway.

Se o gateway ainda existir, mas a chave tiver sido regenerada, use a nova chave para registrar o gateway. Se você não tiver a chave, regenere-a novamente no portal.

### <a name="7-problem"></a>7. Problema
Ao registrar um gateway, talvez seja necessário inserir o caminho e a senha de um certificado.

![Especificar certificado](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
O gateway foi registrado antes em outros computadores. Durante o registro inicial de um gateway, um certificado de criptografia foi associado ao gateway. O certificado pode ser gerado automaticamente pelo gateway ou fornecido pelo usuário.  Esse certificado é usado para criptografar as credenciais do armazenamento de dados (serviço vinculado).  

![Exportar o certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ao restaurar o gateway em um computador host diferente, o assistente de registro solicita que este certificado descriptografe as credenciais anteriormente criptografadas com esse certificado.  Sem esse certificado, as credenciais não poderão ser descriptografadas pelo novo gateway e as próximas execuções de atividade de cópia associadas a esse novo gateway falharão.  

#### <a name="resolution"></a>Resolução
Se você exportou o certificado de credencial do computador do gateway original usando o botão **Exportar** na guia **Configurações** do Gerenciador de Configuração do Gateway de Gerenciamento de Dados, use o certificado aqui.

Não é possível ignorar este estágio ao recuperar um gateway. Se o certificado estiver ausente, você precisará excluir o gateway do portal e criar um novo gateway.  Além disso, todos os serviços vinculados relacionados ao gateway precisarão ser atualizados com a nova inserção das credenciais.

### <a name="8-problem"></a>8. Problema
A seguinte mensagem de erro poderá ser exibida.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Esse erro ocorre quando o seu gateway está em um ambiente que requer um proxy HTTP para acessar recursos da Internet, ou quando a senha de autenticação do proxy é alterada, mas não é atualizada adequadamente em seu gateway.

#### <a name="resolution"></a>Resolução
Siga as instruções da seção Considerações sobre o servidor proxy deste artigo e defina as configurações de proxy com o Gerenciador de Configuração de Gateway de Gerenciamento de Dados.

## <a name="gateway-is-online-with-limited-functionality"></a>O gateway está online com funcionalidade limitada
### <a name="1-problem"></a>1. Problema
O status do gateway é exibido como online com funcionalidade limitada.

#### <a name="cause"></a>Causa
Você vê o status do gateway como online com funcionalidade limitada por um dos seguintes motivos:

* O gateway não pode se conectar ao serviço de nuvem por meio do Barramento de Serviço do Azure.
* O serviço de nuvem não pode se conectar ao gateway por meio do Barramento de Serviço.

Quando o gateway está online com funcionalidade limitada, você não pode usar o Assistente de Cópia do Data Factory para criar pipelines de dados a fim de copiar dados de/para armazenamentos de dados locais. Como solução alternativa, você pode usar o Editor do Data Factory no Portal do Azure, no Visual Studio ou no Azure PowerShell.

#### <a name="resolution"></a>Resolução
A resolução para esse problema (online com funcionalidade limitada) depende da causa: se é o gateway que não pode se conectar ao serviço de nuvem ou vice-versa. As seções a seguir fornecem essas resoluções.

### <a name="2-problem"></a>2. Problema
Você obtém o erro a seguir.

`Error: Gateway cannot connect to cloud service through service bus`

![O gateway não pode se conectar ao serviço de nuvem](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
O gateway não pode se conectar ao serviço de nuvem por meio do Barramento de Serviço.

#### <a name="resolution"></a>Resolução
Siga estas etapas para colocar o gateway novamente online:

1. Permita as regras de saída do endereço IP no computador do gateway e no firewall corporativo. Você pode encontrar endereços IP no Log de Eventos do Windows (ID == 401): Houve uma tentativa de acessar um soquete de uma maneira proibida pelas permissões de acesso XX.XX.XX.XX:9350.
1. Defina as configurações de proxy no gateway. Confira a seção Considerações sobre servidor proxy para saber mais.
1. Habilite as portas de saída 5671 e 9350 a 9354 no Firewall do Windows, no computador do gateway e no firewall corporativo. Confira a seção Portas e firewall para saber mais. Esta etapa é opcional, mas recomendada devido a considerações sobre desempenho.

### <a name="3-problem"></a>3. Problema
Você obtém o erro a seguir.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Um erro transitório na conectividade de rede.

#### <a name="resolution"></a>Resolução
Siga estas etapas para colocar o gateway novamente online:

1. Aguarde alguns minutos e a conectividade será recuperada automaticamente quando o erro for eliminado.
1. Se o erro persistir, reinicie o serviço do gateway.

## <a name="failed-to-author-linked-service"></a>Falha ao criar serviço vinculado
### <a name="problem"></a>Problema
Esse erro poderá ser exibido ao tentar usar o Gerenciador de Credenciais no portal para inserir credenciais de um novo serviço vinculado ou ao atualizar as credenciais de um serviço vinculado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Ao ver esse erro, a página Configurações do Gerenciador de Configuração de Gateway de Gerenciamento de Dados pode parecer com a captura de tela a seguir.

![Não é possível acessar o banco de dados](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
O certificado SSL pode ter sido perdido no computador do gateway. O gateway não pode carregar o certificado usado no momento para a criptografia SSL. Você também pode ver uma mensagem de erro no log de eventos que é semelhante à seguinte mensagem.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolução
Siga estas etapas para resolver o problema:

1. Inicie o Gerenciador de Configuração do Gateway de Gerenciamento de Dados.
2. Alterne para a guia **Configurações** .  
3. Clique no botão **Alterar** para alterar o certificado SSL.

   ![botão Alterar certificado](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecione um novo certificado SSL. Use qualquer certificado SSL gerado por você mesmo ou por qualquer organização.

   ![Especificar certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Falha na atividade de cópia
### <a name="problem"></a>Problema
Você observa a falha “UserErrorFailedToConnectToSqlserver” após configurar um pipeline no portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Isso pode ocorrer por diferentes motivos e a mitigação varia de acordo.

#### <a name="resolution"></a>Resolução
Permita conexões TCP de saída pela porta TCP/1433 no lado do cliente do Gateway de Gerenciamento de Dados antes de se conectar ao banco de dados SQL.

Se o banco de dados de destino for um banco de dados SQL do Azure, verifique também as configurações de firewall do SQL Server do Azure.

Consulte a seção a seguir para testar a conexão com o armazenamento de dados local.

## <a name="data-store-connection-or-driver-related-errors"></a>Erro de conexão com o armazenamento de dados ou erros relacionados a drivers
Caso visualize erros relacionados ao driver ou à conexão com o repositório de dados, siga as etapas abaixo:

1. Inicie o Gerenciador de Configurações do Gateway de Gerenciamento de Dados no computador do gateway.
2. Alterne para a guia **Diagnóstico** .
3. Em **Teste a conexão**, adicione os valores de grupo de gateway.
4. Clique em **Testar** para verificar se você pode se conectar à fonte de dados local do computador do gateway usando as informações e credenciais de conexão. Se a conexão de teste continuar falhando depois que você instalar um driver, reinicie o gateway para que ele assimile a alteração mais recente.

![Testar a conexão na guia Diagnóstico](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Logs do gateway
### <a name="send-gateway-logs-to-microsoft"></a>Enviar logs de gateway para a Microsoft
Ao contatar o Suporte da Microsoft para obter ajuda com soluções de problemas de gateway, pode ser necessário compartilhar seus logs de gateway. A versão do gateway permite que você compartilhe facilmente logs do gateway necessários por meio de dois cliques de botão no Gerenciador de Configuração de Gateway de Gerenciamento de Dados.    

1. Alterne para a guia **Diagnóstico** no Gerenciador de Configuração de Gateway de Gerenciamento de Dados.

    ![Gateway de Gerenciamento de Dados - guia Diagnóstico](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Clique em **Enviar logs** para ver a caixa de diálogo a seguir.

    ![Envio de logs do Gateway de Gerenciamento de Dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (opcional) Clique em **Exibir logs** para examinar logs no visualizador de eventos.
4. (opcional) Clique em **Privacidade** para examinar a declaração de privacidade dos serviços Web da Microsoft.
5. Quando estiver satisfeito com o que está prestes a carregar, clique em **Enviar logs** para realmente enviar os logs dos últimos sete dias para a Microsoft a fim de solucionar problemas. Você deve ver o status da operação send-logs conforme mostrado na imagem a seguir.

    ![Gateway de Gerenciamento de Dados – status de Enviar logs](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Quando a operação for concluída, você verá uma caixa de diálogo como mostrado na imagem a seguir.

    ![Gateway de Gerenciamento de Dados – status de Enviar logs](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Anote a **ID do relatório** e compartilhe-a com o Suporte da Microsoft. A ID do relatório é usada para localizar os logs de gateway carregados para a solução de problemas.  A ID do relatório também será salva no visualizador de eventos.  Você pode encontrá-la ao observar a ID do evento "25" e verificar a data e a hora.

    ![Gateway de Gerenciamento de Dados – ID de relatório de Enviar logs](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Arquivar logs de gateway no computador host de gateway
Existem alguns cenários em que há problemas de gateway e não é possível compartilhar logs do gateway diretamente:

* Você instala manualmente o gateway e o registra.
* Você tenta registrar o gateway com uma chave regenerada no Gerenciador de Configuração de Gateway de Gerenciamento de Dados.
* Você tenta enviar logs e o serviço de host do gateway não consegue se conectar.

Nesses casos, você pode salvar os logs de gateway como um arquivo zip e compartilhá-los quando contatar o suporte da Microsoft. Por exemplo, se você receber um erro ao registrar o gateway, conforme mostrado na imagem a seguir.   

![Gateway de Gerenciamento de Dados – Erro de registro](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Clique no link **Arquivar logs de gateway** para arquivar e salvar os logs e compartilhar o arquivo zip com o suporte da Microsoft.

![Gateway de Gerenciamento de Dados - Arquivar logs](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Localizar os logs do gateway
Você pode encontrar informações detalhadas sobre os logs de gateway nos logs de eventos do Windows.

1. Inicie o **Visualizador de eventos** do Windows.
2. Localize os logs na pasta **Logs de aplicativos e serviços** > **Gateway de Gerenciamento de Dados**.

   Ao solucionar problemas relacionados ao gateway, procure eventos no nível de erro no visualizador.

![Gateway de Gerenciamento de Dados – Logs no visualizador de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
