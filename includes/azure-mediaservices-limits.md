>[!NOTE]
>Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um tíquete de suporte. **Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

| Recurso | Limite padrão | 
| --- | --- | 
| AMS (Contas de Serviços de Mídia) do Azure em uma única assinatura | 25 (fixo) |
| RUs (Unidades Reservadas) de Mídia por conta AMS |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| Trabalhos por conta AMS | 50,000<sup>(2)</sup> |
| Tarefas encadeadas por trabalho | 30 (fixo) |
| Ativos por conta AMS | 1.000.000|
| Ativos por tarefa | 50 |
| Ativos por trabalho | 100 |
| Localizadores exclusivos associados a um ativo simultaneamente | 5<sup>(4)</sup> |
| Canais ao vivo por conta AMS  |5|
| Programas no estado interrompido por canal  |50|
| Programa em estado de execução por canal  |3|
| Pontos de extremidade de streaming no estado “executando” por conta AMS|2|
| Unidades de streaming por ponto de extremidade de streaming |10 |
| Contas de armazenamento | 1,000<sup>(5)</sup> (fixo) |
| Políticas | 1,000,000<sup>(6)</sup> |
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>7</sup> |
  
<sup>1</sup> S3 RUs não estão disponíveis na Índia Ocidental. Os limites máximos de RU serão redefinidos se o cliente alterar o tipo (por exemplo, de S2 para S1). 

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir os trabalhos antigos usando **IJob.Delete** ou a solicitação HTTP **DELETE**.

A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias será excluído automaticamente, junto com seus registros de tarefas associados, mesmo que o número total de registros esteja abaixo da cota máxima. Se você precisar arquivar as informações de trabalho/tarefa, poderá usar o código descrito [aqui](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> Ao fazer uma solicitação para listar as Entidades de trabalho, será retornado um máximo de 1.000 por solicitação. Se você precisar manter o controle dos de todos os trabalhos enviados, você pode usar superior/pular conforme descrito em [Opções de consulta OData sistema](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM). Para saber mais, consulte [esta](../articles/media-services/media-services-content-protection-overview.md) seção.

<sup>5</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>6</sup> há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Você deverá usar a mesma ID de política se estiver sempre usando os mesmos dias/permissões de acesso/etc. Para obter informações e um exemplo, veja [esta](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) seção.

<sup>7</sup>Se você estiver carregando conteúdo para um Ativo nos Serviços de Mídia do Azure com o propósito de processá-lo com um dos processadores de mídia em nosso serviço (ou seja, codificadores como mecanismos de Media Encoder Standard e o fluxo de trabalho do Media Encoder Premium ou análise como Detecção Facial), em seguida, você deve estar ciente da restrição para o tamanho máximo. 

A partir de 15 de maio de 2017, o tamanho máximo suportado para um único blob é 195 TB - com arquivos acima desse limite, a tarefa falhará. Estamos trabalhando em uma correção para tratar desse limite. Além disso, a restrição de tamanho máximo do ativo ocorre da seguinte maneira.

| Tipo de Unidade Reservada de Mídia | Tamanho máximo de entrada (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
