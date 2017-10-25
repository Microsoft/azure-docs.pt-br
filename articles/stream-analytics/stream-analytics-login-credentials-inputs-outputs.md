---
title: "Stream Analytics: alternar as credenciais de logon para entradas e saídas | Microsoft Docs"
description: "Saiba como atualizar as credenciais para as entradas e saídas do Stream Analytics."
keywords: credenciais de logon
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a1a927fa9c34b38e54fdb22782e80fd13bf430c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Fazer a rotação de credenciais de logon para entradas e saídas em trabalhos do Stream Analytics
## <a name="abstract"></a>Resumo
No momento, o Azure Stream Analytics não permite a substituição das credenciais em uma entrada/saída durante a execução do trabalho.

Embora o Azure Stream Analytics ofereça suporte à retomada de um trabalho desde a última saída, queremos compartilhar o processo inteiro para minimizar a latência entre a parada e o início do trabalho e fazer a rotação de credenciais de logon.

## <a name="part-1---prepare-the-new-set-of-credentials"></a>Parte 1 - Preparar o novo conjunto de credenciais:
Essa parte é aplicável às seguintes entradas/saídas:

* Armazenamento de Blob
* Hubs de Eventos
* Banco de Dados SQL
* Armazenamento de tabela

Para outras entradas/saídas, prossiga para a Parte 2.

### <a name="blob-storagetable-storage"></a>Armazenamento de blob/Armazenamento de tabela
1. Vá para a extensão Armazenamento no Portal de Gerenciamento do Azure:   
   ![elementográfico1][graphic1]
2. Localize o armazenamento usado por seu trabalho e acesse-o:   
   ![elementográfico2][graphic2]
3. Clique no comando Gerenciar Chaves de Acesso:   
   ![elementográfico3][graphic3]
4. Entre a Chave de Acesso Primária e a Chave de Acesso Secundária, **escolha a que não é usada por seu trabalho**.
5. Regeneração de ocorrência:   
   ![elementográfico4][graphic4]
6. Copie a chave recém-gerada:   
   ![elementográfico5][graphic5]
7. Prossiga para a Parte 2.

### <a name="event-hubs"></a>Hubs de Eventos
1. Vá para a extensão Barramento de Serviço no Portal de Gerenciamento do Azure:   
   ![elementográfico6][graphic6]
2. Localize o Namespace do Barramento de Serviço usado por seu trabalho e acesse-o:   
   ![elementográfico7][graphic7]
3. Se seu trabalho usar uma política de acesso compartilhado no Namespace de Barramento de Serviço, vá para a etapa 6  
4. Vá para a guia Hubs de Eventos:   
   ![elementográfico8][graphic8]
5. Localize o Hub de Eventos usado por seu trabalho e acesse-o:   
   ![elementográfico9][graphic9]
6. Vá para a guia Configurar:   
   ![elementográfico10][graphic10]
7. No menu suspenso Nome da Política, localize a política de acesso compartilhado usada por seu trabalho:   
   ![elementográfico11][graphic11]
8. Entre a Chave Primária e a Chave Secundária, **escolha a que não é usada por seu trabalho**.  
9. Regeneração de ocorrência:   
   ![elementográfico12][graphic12]
10. Copie a chave recém-gerada:   
   ![elementográfico13][graphic13]
11. Prossiga para a Parte 2.  

### <a name="sql-database"></a>Banco de Dados SQL
> [!NOTE]
> Observação: será necessário se conectar ao Serviço de Banco de Dados SQL. Vamos mostrar como fazer isso usando a experiência de gerenciamento no Portal de Gerenciamento do Azure, mas você também pode optar por usar uma ferramenta do lado cliente, como o SQL Server Management Studio.
>
> 

1. Vá para a extensão Bancos de Dados SQL no Portal de Gerenciamento do Azure:   
   ![elementográfico14][graphic14]
2. Localize o Banco de Dados SQL usado por seu trabalho e **clique no link do servidor** na mesma linha:  
   ![elementográfico15][graphic15]
3. Clique no comando Gerenciar:   
   ![elementográfico16][graphic16]
4. Digite o Banco de Dados Mestre:   
   ![elementográfico17][graphic17]
5. Digite seu Nome de Usuário, sua Senha e clique em Fazer logon:   
   ![elementográfico18][graphic18]
6. Clique em Nova Consulta:   
   ![elementográfico19][graphic19]
7. Digite a consulta a seguir, substituindo <login_name> por seu Nome de Usuário e substituindo <enterStrongPasswordHere> por sua nova senha:  
   `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8. Clique em Executar:   
   ![elementográfico20][graphic20]
9. Volte para etapa 2 e, dessa vez, clique no banco de dados:   
   ![elementográfico21][graphic21]
10. Clique no comando Gerenciar:   
   ![elementográfico22][graphic22]
11. Digite seu Nome de Usuário, sua Senha e clique em Fazer logon:   
   ![elementográfico23][graphic23]
12. Clique em Nova Consulta:   
   ![elementográfico24][graphic24]
13. Digite a consulta a seguir, substituindo <user_name> por um nome pelo qual você deseja identificar esse logon no contexto desse banco de dados (é possível fornecer o mesmo valor atribuído para <login_name>, por exemplo) e substituindo <login_name> por seu novo nome de usuário:  
   `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Clique em Executar:   
   ![elementográfico25][graphic25]
15. Agora, você deve fornecer ao novo usuário as mesmas funções e privilégios que o usuário original tinha.
16. Prossiga para a Parte 2.

## <a name="part-2-stopping-the-stream-analytics-job"></a>Parte 2: Interrompendo o trabalho do Stream Analytics
1. Vá para a extensão Stream Analytics no Portal de Gerenciamento do Azure:   
   ![elementográfico26][graphic26]
2. Localize seu trabalho e acesse-o:   
   ![elementográfico27][graphic27]
3. Vá para a guia Entradas ou para a guia Saídas se você estiver fazendo uma rotação das credenciais em uma Entrada ou em uma Saída.  
   ![elementográfico28][graphic28]
4. Clique no comando Parar e confirme se o trabalho foi interrompido:   
   ![graphic29][graphic29] Aguarde a interrupção do trabalho.
5. Localize a entrada/saída em que você deseja fazer a rotação de credenciais e acesse-a:   
   ![elementográfico30][graphic30]
6. Prossiga para a Parte 3.

## <a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: Editando as credenciais no trabalho do Stream Analytics
### <a name="blob-storagetable-storage"></a>Armazenamento de blob/Armazenamento de tabela
1. Localize o campo Chave da Conta de Armazenamento e cole sua chave recém-gerada nele:   
   ![elementográfico31][graphic31]
2. Clique no comando Salvar e confirme o salvamento das alterações:   
   ![elementográfico32][graphic32]
3. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.
4. Prossiga para a Parte 4.

### <a name="event-hubs"></a>Hubs de Eventos
1. Localize o campo Política de Hub de Eventos e cole sua chave recém-gerada nele:   
   ![elementográfico33][graphic33]
2. Clique no comando Salvar e confirme o salvamento das alterações:   
   ![elementográfico34][graphic34]
3. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.
4. Prossiga para a Parte 4.

### <a name="power-bi"></a>Power BI
1. Clique em Renovar autorização:  

   ![elementográfico35][graphic35]
2. Você obterá a seguinte confirmação:  

   ![elementográfico36][graphic36]
3. Clique no comando Salvar e confirme o salvamento das alterações:   
   ![elementográfico37][graphic37]
4. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.
5. Prossiga para a Parte 4.

### <a name="sql-database"></a>Banco de Dados SQL
1. Localize os campos Nome de usuário e Senha e cole seu conjunto de credenciais recém-criado neles:   
   ![elementográfico38][graphic38]
2. Clique no comando Salvar e confirme o salvamento das alterações:   
   ![elementográfico39][graphic39]
3. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.  
4. Prossiga para a Parte 4.

## <a name="part-4-starting-your-job-from-last-stopped-time"></a>Parte 4: Iniciando o trabalho desde a hora da última interrupção
1. Navegue para fora da Entrada/Saída:   
   ![elementográfico40][graphic40]
2. Clique no comando Iniciar:   
   ![elementográfico41][graphic41]
3. Selecione a Hora da Última Interrupção e clique em OK:   
   ![elementográfico42][graphic42]
4. Prossiga para a Parte 5.  

## <a name="part-5-removing-the-old-set-of-credentials"></a>Parte 5: Removendo o conjunto de credenciais antigo
Essa parte é aplicável às seguintes entradas/saídas:

* Armazenamento de Blob
* Hubs de Eventos
* Banco de Dados SQL
* Armazenamento de tabela

### <a name="blob-storagetable-storage"></a>Armazenamento de blob/Armazenamento de tabela
Repita a Parte 1 da Chave de Acesso usada anteriormente por seu trabalho para renovar a Chave de Acesso agora não utilizada.

### <a name="event-hubs"></a>Hubs de Eventos
Repita a Parte 1 da Chave usada anteriormente por seu trabalho para renovar a Chave agora não utilizada.

### <a name="sql-database"></a>Banco de Dados SQL
1. Vá para a janela de consulta da Parte 1 Etapa 7 e digite a consulta a seguir, substituindo <previous_login_name> pelo Nome de Usuário usado anteriormente por seu trabalho:  
   `DROP LOGIN <previous_login_name>`  
2. Clique em Executar:   
   ![elementográfico43][graphic43]  

Você deverá obter a seguinte confirmação: 

    Command(s) completed successfully.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png

