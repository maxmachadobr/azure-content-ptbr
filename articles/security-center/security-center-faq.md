<properties
   pageTitle="Perguntas frequentes sobre a Central de Segurança do Azure | Microsoft Azure"
   description="Encontre respostas para perguntas frequentes sobre a Central de Segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="terrylan"/>

# Perguntas frequentes sobre a Central de Segurança do Azure

Estas perguntas frequentes estão relacionadas à Central de Segurança do Azure, um serviço que ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Microsoft Azure.

## Perguntas gerais

### O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### Como posso obter a Central de Segurança do Azure?
A Central de Segurança do Azure é habilitada com sua assinatura do Microsoft Azure e pode ser acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entre no portal](https://portal.azure.com), selecione **Procurar** e role até a **Central de Segurança**).

## Cobrança

### Como funciona o faturamento para a Central de Segurança do Azure?
A Central de Segurança é oferecida em duas camadas: Gratuita e Standard.

A camada Gratuita permite definir políticas de segurança e receber alertas, incidentes e recomendações de segurança que orientam você durante o processo de configuração dos controles necessários. Com a camada Gratuita, você também pode monitorar o estado de segurança de seus recursos do Azure e de soluções de parceiros integradas com sua assinatura do Azure.

A camada Standard oferece os recursos da camada Gratuita mais as detecções avançadas: inteligência de ameaças, análise comportamental, análise de falha e detecção de anomalias. Uma avaliação gratuita de 90 dias da camada Standard está disponível. Para atualizar, selecione Tipo de Preço na [política de segurança](security-center-policies.md#setting-security-policies-for-subscriptions). Consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) para saber mais.

## Coleta de dados

A Central de Segurança coleta dados de suas máquinas virtuais para avaliar o estado de sua segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as máquinas virtuais em sua assinatura. A coleta de dados é recomendada, mas você pode recusá-la [desabilitando a coleta de dados](#how-do-i-disable-data-collection) na política da Central de Segurança.

### Como desabilitar a coleta de dados?

Você pode desabilitar a **Coleta de dados** para uma assinatura na política de Segurança a qualquer momento. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**). Quando você selecionar uma assinatura, uma nova folha será aberta e fornecerá a opção de desativar a **Coleta de dados**. Selecione a opção **Excluir agentes** na faixa de opções para remover os agentes das máquinas virtuais existentes.

> [AZURE.NOTE] As políticas de segurança podem ser definidas no nível de assinatura do Azure e no nível do grupo de recursos, mas você precisa selecionar uma assinatura para desativar a coleta de dados.

### Como habilitar a coleta de dados?
Você pode habilitar a coleta de dados para suas assinaturas do Azure na política de segurança. Para habilitar a coleta de dados, [entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**. Defina a **coleta de dados** como **Ativada** e configure as contas de armazenamento onde você deseja que os dados sejam coletados (veja a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”). Quando estiver habilitada, a **Coleta de dados** coletará automaticamente as informações de configuração e os eventos de segurança de todas as máquinas virtuais com suporte na assinatura.

> [AZURE.NOTE] As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a configuração da coleta de dados ocorre apenas no nível da assinatura.

### O que acontece quando a coleta de dados é habilitada?
A coleta de dados é habilitada por meio do agente de monitoramento do Azure e da extensão de monitoramento de segurança do Azure. A extensão de Monitoramento de Segurança do Azure verifica várias configurações de segurança relevantes e as envia para os rastreamentos do [ETW (Rastreamento de Eventos para Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Além disso, o sistema operacional cria entradas de log de eventos. O agente de monitoramento do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise. Essa é a conta de armazenamento configurada na política de segurança. Para mais informações sobre a conta de armazenamento, consulte a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”

### A extensão do Agente de monitoramento ou o Monitoramento de segurança afetam o desempenho do meu servidor?
O agente e a extensão consomem uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho.

### Onde meus dados são armazenados?
Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados. Você pode escolher a conta de armazenamento para uma assinatura na política de segurança. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**). Quando você clica em uma assinatura, uma nova folha é aberta. Selecione **Escolher contas de armazenamento** para selecionar uma região.

> [AZURE.NOTE] As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a seleção da região de sua conta de armazenamento ocorre apenas no nível da assinatura.

Para saber mais sobre as contas de armazenamento e o armazenamento do Azure, consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) e a seção [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## Como usar a Central de Segurança do Azure

### O que é uma política de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança do Azure, você pode definir as políticas para as assinaturas do Azure e grupos de recurso de acordo com os requisitos de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII (informações de identificação pessoal), podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas na Central de segurança do Azure determinarão as recomendações de segurança e monitoramento. Para saber mais sobre as políticas de segurança, consulte a seção [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md).

> [AZURE.NOTE] No caso de um conflito entre a política de nível de assinatura e a política de nível de grupo de recursos, a política de nível de grupo de recursos tem precedência.

### Quem pode modificar uma política de segurança?
As políticas de segurança são configuradas para cada grupo de recursos ou assinatura. Para modificar uma política de segurança no nível de assinatura ou no nível de grupo de recursos, você deve ser um Proprietário ou Colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](security-center-policies.md).

### O que é são recomendações de segurança?
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam você pelo processo de configuração do controle necessário. Os exemplos abrangem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
- Como configurar [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) e as regras para controlar o tráfego para máquinas virtuais
- Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
- Como implantar atualizações de sistema ausentes
- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas Políticas de segurança são mostradas aqui.

### Como é possível ver o estado atual da segurança dos meus recursos do Azure?
Um bloco **integridade de recursos** da folha da **Central de segurança** mostra a postura de segurança geral do seu ambiente dividido por máquinas virtuais, aplicativos web e outros recursos. Cada recurso tem um indicador mostrando se possíveis vulnerabilidades de segurança foram identificadas. Clicar no bloco de integridade de recursos exibe os recursos e identifica onde a atenção é requerida ou talvez haja problemas.

### O que dispara um alerta de segurança?
A Central de segurança do Azure automaticamente coleta, analisa e funde os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

- As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
- Malware avançado detectado com o relatório de erros do Windows
- Ataques por força bruta contra máquinas virtuais
- Alertas de segurança das soluções de segurança de parceiro integradas, como antimalware ou Firewalls de aplicativo Web

### Qual é a diferença entre ameaças detectadas e alertadas pelo Microsoft Security Response Center versus pela Central de Segurança do Azure?
O MSRC (Microsoft Security Response Center) executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros. Quando o MSRC fica ciente de que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal ou que o uso do cliente do Azure não está de acordo com os termos de Uso Aceitável, um gerente de incidentes de segurança notifica o cliente. Normalmente, a notificação será feita enviando um email para os contatos de segurança especificados na Central de Segurança do Azure ou para o proprietário da assinatura do Azure se um contato de segurança não for especificado.

A Central de Segurança é um serviço do Azure que monitora o ambiente do cliente do Azure continuamente e aplica a análise para detectar automaticamente uma ampla gama de atividades potencialmente mal-intencionadas. Essas detecções são exibidas como alertas de segurança no painel da Central de Segurança.

### Como as permissões são tratadas na Central de segurança do Azure?
A Central de segurança do Azure oferece suporte ao acesso baseado em função. Para saber mais sobre o controle de acesso baseado em função (RBAC) no Azure, consulte o [Controle de acesso baseado em função do Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Quando um usuário abrir a Central de Segurança, somente as recomendações e alertas relacionados aos recursos a que o usuário tem acesso aparecerão. Isso significa que os usuários verão apenas os itens relacionados a recursos nos quais o usuário recebe a função de leitor, colaborador ou proprietário para a assinatura ou grupo de recursos a que o recurso pertence.

Se precisar:

- **editar uma política de segurança**, você precisa ser um proprietário ou colaborador da assinatura.
- **aplicar uma recomendação**, você precisa ser um proprietário ou colaborador da assinatura.
- **ter visibilidade do estado de segurança em todas as suas assinaturas**, você precisa ser um proprietário, colaborador ou leitor (administrador de TI, equipe de segurança) de cada assinatura.
- **ter visibilidade do estado de segurança de seus recursos**, você precisa ser um proprietário, colaborador ou leitor do grupo de recursos (DevOps).

### Quais recursos do Azure são monitorados pela Central de Segurança do Azure?
A Central de Segurança do Azure monitora os seguintes recursos do Azure:

- Máquinas virtuais (incluindo os Serviços de Nuvem)
- Redes Virtuais do Azure
- Serviço do SQL Azure
- Soluções de parceiros integradas com sua assinatura do Azure, como um firewall de aplicativo Web

## Máquinas Virtuais

### Quais tipos de máquinas virtuais terão suporte?
O monitoramento e as recomendações de integridade de segurança estão disponíveis para VMs (máquinas virtuais) criadas usando os [modelos de implantação clássico e do Gerenciador de Recursos](../azure-classic-rm.md).

VMs do Windows com suporte:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

VMs do Linux com suporte:

- Ubuntu versões 12.04, 14.04, 15.10, 16.04
- Debian versões 7, 8
- CentOS versões 6.\*, 7.*
- Red Hat Enterprise Linux (RHEL) versões 6.\*, 7.*
- SUSE Linux Enterprise Server (SLES) versões 11.\*, 12.*

Também há suporte para VMs em execução em um serviço de nuvem. Apenas serviços de nuvem da Web e funções de trabalho em execução em slots de produção são monitorados. Para saber mais sobre o serviço de nuvem, confira [Visão geral dos Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md).

### Por que a Central de Segurança do Azure não reconhece a solução antimalware em execução em minha VM do Azure?

A Central de Segurança do Azure só tem visibilidade de antimalware instalado por meio de extensões do Azure. Por exemplo, a Central de Segurança não será capaz de detectar antimalware que foi pré-instalado em uma imagem fornecida por você ou se você tiver instalado antimalware em suas máquinas virtuais usando seus próprios processos (como sistemas de gerenciamento de configuração).

### Por que recebo a mensagem "Dados de verificação ausentes" para minha VM?

Pode levar algum tempo (normalmente, menos de uma hora) para que os dados de verificação sejam preenchido após a Coleta de dados ser habilitada na Central de Segurança do Azure. As verificações não são preenchidas para VMs com estado parado.

### Por que recebo a mensagem "O Agente de VM está ausente?"

O Agente de VM deve ser instalado nas VMs para habilitar a Coleta de dados. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. Para obter informações sobre como instalar o Agente da VM em outras VMs, consulte a postagem de blog sobre o [Agente de VM e Extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).

<!---HONumber=AcomDC_0831_2016-->