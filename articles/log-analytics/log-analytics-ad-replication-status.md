<properties
	pageTitle="Solução de Status de Replicação do Active Directory no Log Analytics | Microsoft Azure"
	description="O pacote de solução de Status de Replicação do Active Directory monitora regularmente seu ambiente do Active Directory em busca de falhas de replicação e relata os resultados no seu painel do OMS."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="banders"/>

# Solução de Status de Replicação do Active Directory no Log Analytics

O Active Directory é um componente-chave de um ambiente de TI corporativo. Para garantir a alta disponibilidade e alto desempenho, cada controlador de domínio tem sua própria cópia do banco de dados do Active Directory. Controladores de domínio replicam entre si para propagar alterações em toda a empresa. Falhas nesse processo de replicação podem causar vários problemas em toda a empresa.

O pacote de solução de Status de Replicação do AD monitora regularmente seu ambiente do Active Directory em busca de falhas de replicação e relata os resultados no seu painel do OMS.

## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

- Você deve ter pelo menos um controlador de domínio conectado ao seu espaço de trabalho do OMS. Para entender como conectar um controlador de domínio diretamente ao OMS, consulte [Conectar computadores Windows ao Log Analytics](log-analytics-windows-agents.md). Se o controlador de domínio já fizer parte de um ambiente existente do System Center Operations Manager que você gostaria de conectar ao OMS, consulte [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md).
- Adicione a solução de Status de Replicação do Active Directory ao espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.


## Detalhes de coleta de dados do Status de Replicação do AD

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Status de Replicação de AD.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Sim](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Não](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Não](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Sim](./media/log-analytics-ad-replication-status/oms-bullet-green.png)| a cada 5 dias|


## Outra opção é habilitar um controlador que não seja de domínio para enviar dados do AD para o OMS
Se não quiser conectar qualquer um dos seus controladores de domínio diretamente ao OMS, você poderá usar qualquer outro computador conectado ao OMS em seu domínio para coletar dados para o pacote de solução de Status de Replicação do AD e fazê-lo enviar os dados.

### Para habilitar um controlador que não seja de domínio para enviar dados do AD para o OMS
1.	Verifique se o computador é um membro do domínio que você deseja monitorar usando a solução de Status de Replicação do AD.
2.	[Conecte o computador Windows ao OMS](log-analytics-windows-agents.md) ou [conecte-o usando seu ambiente existente do Operations Manager para OMS](log-analytics-om-agents.md), se ele ainda não estiver conectado.
3.	Nesse computador, defina a seguinte chave do Registro:
    - Chave: **HKLM\\SOFTWARE\\Microsoft\\AzureOperationalInsights\\Assessments\_Targets**
    - Valor: **ADReplication**

    >[AZURE.NOTE]Essas alterações não entrarão em vigor até o serviço do Microsoft Monitoring Agent (HealthService.exe) ser reiniciado.

## Entendendo erros de replicação
Depois de ter enviado os dados do status de replicação do AD para o OMS, você verá um bloco semelhante ao seguinte no painel do OMS indicando o número de erros de replicação que existem no momento. ![Bloco do Status de Replicação do AD](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Erros Críticos de Replicação** são aqueles iguais ou superiores a 75% do [tempo de vida da marca de exclusão](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) para sua floresta do Active Directory.

Ao clicar no bloco, você verá mais informações sobre os erros. ![Painel do Status de Replicação do AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)


### Status do Servidor de Destino e Status do Servidor de Origem
Essas folhas mostram o status dos servidores de destino e servidores de origem que estão com erros de replicação. O número após cada nome de controlador de domínio indica o número de erros de replicação no controlador de domínio.

Os erros para os servidores de destino e os de origem são mostrados porque alguns problemas são mais fáceis de solucionar da perspectiva do servidor de origem e outros da perspectiva do servidor de destino.

Neste exemplo, você pode ver que muitos servidores de destino têm aproximadamente o mesmo número de erros, mas há um servidor de origem (ADDC35) com muito mais erros do que todos os outros. É provável que haja algum problema no ADDC35 que esteja causando a falha ao enviar dados para seus parceiros de replicação. Corrigir os problemas no ADDC35 provavelmente resolverá muitos dos erros que aparecem na folha do servidor de destino.

### Tipos de Erros de Replicação
Essa folha fornece informações sobre os tipos de erros detectados em toda a empresa. Cada erro tem um código numérico exclusivo e uma mensagem que pode ajudar a determinar a causa do erro.

A rosca na parte superior dá uma ideia de quais erros aparecerem com mais ou menos frequência no seu ambiente.

Isso pode mostrar quando vários controladores de domínio têm o mesmo erro de replicação. Nesse caso, você poderá descobrir ou identificar uma solução em um controlador de domínio e repeti-la em outros controladores de domínio afetados pelo mesmo erro.

### Tempo de vida da marca de exclusão
O tempo de vida da marca de exclusão determina quanto tempo um objeto excluído, conhecido como uma marca para exclusão, é mantido no banco de dados do Active Directory. Quando um objeto excluído ultrapassa o tempo de vida da marca de exclusão, um processo de coleta de lixo automaticamente o remove do banco de dados do Active Directory.

O tempo de vida da marca de exclusão padrão é 180 dias para versões mais recentes do Windows, mas era 60 dias em versões mais antigas e pode ser alterado explicitamente por um administrador do Active Directory.

É importante saber se você está tendo erros de replicação que estão se aproximando ou ultrapassaram o tempo de vida da marca de exclusão. Se dois controladores de domínio tiverem um erro de replicação que persiste após o tempo de vida da marca para exclusão, a replicação será desabilitada entre esses dois controladores de domínio, mesmo se o erro de replicação subjacente for corrigido.

A folha Tempo de Vida da Marca de Exclusão ajuda a identificar locais nos quais há risco de isso ocorrer. Cada erro na categoria **Maior que 100% TSL** representa uma partição não replicada entre seu servidor de origem e de destino por pelo menos o tempo de vida da marca de exclusão da floresta.

Nessa situação, simplesmente corrigir o erro de replicação não será suficiente. No mínimo, você precisará investigar manualmente para identificar e limpar objetos remanescentes antes de reiniciar a replicação. Talvez ainda seja necessário encerrar um controlador de domínio.

Além de identificar os erros de replicação que persistiram após o tempo de vida da marca para exclusão, também é desejável prestar atenção nos erros que se enquadram nas categorias **50% a 75% TSL** ou **75% a 100% TSL**.

Esses são erros claramente remanescentes, não transitórios, e que provavelmente precisarão da sua intervenção para serem resolvidos. A boa notícia é que eles ainda não atingiram o tempo de vida da marca de exclusão. Se você corrigir estes problemas imediatamente e *antes* de atingirem o tempo de vida da marca de exclusão, a replicação poderá ser reiniciada com intervenção manual mínima.

Conforme observado anteriormente, o bloco do painel para a solução de Status de Replicação do AD mostra o número de erros *críticos* de replicação em seu ambiente, que é definido como erros de mais de 75% de tempo de vida da marca de exclusão (incluindo erros que são mais 100% da TSL). Tente manter esse número em 0.

>[AZURE.NOTE] Todos os cálculos de percentual de tempo de vida de marca de exclusão baseiam-se em valores atuais sua floresta do Active Directory, portanto você pode confiar que essas porcentagens são precisas, mesmo se tiver um valor de tempo de vida de marca de exclusão personalizado definido.

### Detalhes do status de Replicação do AD
Ao clicar em qualquer item de uma das listas, verá detalhes adicionais sobre ele usando a Pesquisa de Log. Os resultados são filtrados para mostrar somente os erros relacionados a esse item. Por exemplo, se você clicar no primeiro controlador de domínio listado em **Status do Servidor de Destino (ADDC02)**, verá os resultados da pesquisa filtrados para mostrar erros com esse controlador de domínio listado como o servidor de destino:

![Erros de status de replicação do AD nos resultados da pesquisa](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Aqui, você pode filtrar ainda mais, modificar a consulta de pesquisa e muito mais. Para obter mais informações sobre como usar a Pesquisa de Log, consulte [Pesquisas de log](log-analytics-log-searches.md).

O campo **HelpLink** mostra a URL de uma página do TechNet com detalhes adicionais sobre esse erro específico. Você pode copiar e colar esse link na janela do navegador para ver informações sobre solução de problemas e corrigir o erro.

Você também pode clicar em **Exportar** para exortar os resultados para o Excel. Isso permite visualizar os dados de erros de replicação da forma que você desejar.

![erros de status de replicação de AD exportados no Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## Perguntas frequentes do Status de Replicação do AD
**P: Com que frequência os dados de status de replicação do AD são atualizados?** R: As informações são atualizadas a cada 5 dias.

**P: Há uma maneira de configurar a frequência com que tais dados são atualizados?** R: Não no momento.

**P: Preciso adicionar todos os meus controladores de domínio ao meu espaço de trabalho do OMS para ver o status de replicação?** R: Não, apenas um único controlador de domínio deve ser adicionado. Se você tiver vários controladores de domínio em seu espaço de trabalho do OMS, dados de todos eles serão enviados para o OMS.

**P: Não quero adicionar nenhum controlador de domínio ao meu espaço de trabalho do OMS. Ainda posso usar a solução de Status de Replicação do AD?** R: Sim. Você pode definir o valor de uma chave do Registro para habilitar isso. Consulte [Para habilitar um controlador que não seja de domínio para enviar dados do AD para o OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**P: Qual é o nome do processo que faz a coleta de dados?** R: AdvisorAssessment.exe

**P: Quanto tempo leva para os dados serem coletados?** R: O tempo de coleta de dados depende do tamanho do ambiente do Active Directory, mas geralmente leva menos de 15 minutos.

**P: Que tipo de dados é coletado?** R: Informações de replicação são coletadas por meio do LDAP.

**P: Há uma maneira de configurar quando os dados são coletados?** R: Não no momento.

**P: De quais permissões preciso para coletar dados?** R: Permissões de usuário normais para o Active Directory geralmente são suficientes.

## Solucionar problemas de coleta de dados
Para coletar dados, o pacote de solução de Status de Replicação do AD requer pelo menos um controlador de domínio esteja conectado ao seu espaço de trabalho do OMS. Enquanto isso, você verá uma mensagem indicando que os **dados ainda estão sendo coletados**.

Se você precisar de assistência para conectar um dos seus controladores de domínio, você poderá exibir a documentação em [Conectar computadores Windows ao Log Analytics](log-analytics-windows-agents.md). Como alternativa, se o controlador de domínio já estiver conectado a um ambiente existente do System Center Operations Manager, você poderá exibir a documentação em [Conectar o System Center Operations Manager ao Log Analytics](log-analytics-om-agents.md).

Se você não quiser conectar nenhum controlador de domínio diretamente ao OMS ou SCOM, consulte [Para habilitar um controlador que não seja de domínio para enviar dados do AD para o OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).


## Próximas etapas

- Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados de status de replicação do Active Directory.

<!---HONumber=AcomDC_0518_2016-->