O GRS (armazenamento com redundância geográfica) replica seus dados para uma região secundária a centenas de quilômetros da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Para uma conta de armazenamento com GRS habilitado, uma atualização primeiro é confirmada para a região primária, na qual é replicada três vezes. Em seguida, a atualização é replicada de modo assíncrono para a região secundária, onde ela também é replicada três vezes.

Com o GRS, as regiões primária e secundária gerenciam réplicas entre domínios de falha e domínios de atualização separados em uma unidade de escala de armazenamento, conforme descrito com o LRS.

Considerações:

* Como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam pedidas se os dados não puderem ser recuperados da região primária.
* A réplica não estará disponível, a menos que a Microsoft inicie o failover para a região secundária. Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](../articles/storage/common/storage-disaster-recovery-guidance.md). 
* Se um aplicativo quiser ler na região secundária, o usuário deverá habilitar o RA-GRS.

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária é determinada com base na região primária e não pode ser alterada. A tabela a seguir mostra os emparelhamentos de regiões primárias e secundárias.

| Primário | Secundário |
| --- | --- |
| Centro-Norte dos EUA | Centro-Sul dos Estados Unidos |
| Centro-Sul dos Estados Unidos | Centro-Norte dos EUA |
| Leste dos EUA | Oeste dos EUA |
| Oeste dos EUA | Leste dos EUA |
| Leste dos EUA 2 | Centro dos EUA |
| Centro dos EUA | Leste dos EUA 2 |
| Norte da Europa | Europa Ocidental |
| Europa Ocidental | Norte da Europa |
| Sudeste da Ásia | Ásia Oriental |
| Ásia Oriental | Sudeste da Ásia |
| China Oriental | Norte da China |
| Norte da China | China Oriental |
| Leste do Japão | Oeste do Japão |
| Oeste do Japão | Leste do Japão |
| Sul do Brasil | Centro-Sul dos Estados Unidos |
| Leste da Austrália | Sudeste da Austrália |
| Sudeste da Austrália | Leste da Austrália |
| Sul da Índia | Centro da Índia |
| Centro da Índia | Sul da Índia |
| Oeste da Índia | Sul da Índia |
| Gov do Iowa nos EUA | Gov. dos EUA – Virgínia |
| Gov. dos EUA – Virgínia | Governo dos EUA do Texas |
| Governo dos EUA do Texas | Governo dos EUA do Arizona |
| Governo dos EUA do Arizona | Governo dos EUA do Texas |
| Canadá Central | Leste do Canadá |
| Leste do Canadá | Canadá Central |
| Oeste do Reino Unido | Sul do Reino Unido |
| Sul do Reino Unido | Oeste do Reino Unido |
| Alemanha Central | Nordeste da Alemanha |
| Nordeste da Alemanha | Alemanha Central |
| Oeste dos EUA 2 | Centro-Oeste dos EUA |
| Centro-Oeste dos EUA | Oeste dos EUA 2 |

Para obter informações atualizadas sobre regiões com suporte do Azure, confira [Regiões do Azure](https://azure.microsoft.com/regions/).

>[!NOTE]  
> A região secundária do US Gov - Virgínia é US Gov - Texas. Anteriormente, o US Gov - Virgínia utilizada o US Gov - Iowa como uma região secundária. As contas de armazenamento que ainda utilizam US Gov - Iowa como uma região secundária estão sendo migradas para US Gov - Texas como uma segunda região. 
> 
> 