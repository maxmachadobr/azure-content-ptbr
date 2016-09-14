<properties
	pageTitle="Visão Geral de conjunto de dimensionamento (Scale Set) de Máquinas Virtuais - Microsoft Azure"
	description="Saiba mais sobre os Conjuntos de Dimensionamento de Máquinas Virtuais"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="guybo"/>

# Conjuntos de Dimensionamento (Scale Set) de Máquinas Virtuais - Visão Geral

Os conjuntos de dimentionamento de máquinas virtuais são um recurso de Computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas. Com todas as VMs configuradas igualmente, os conjuntos de dimensionamento de VMs foram desenvolvidos para dar suporte ao verdadeiro redimensionamento automático (não é necessário o pré-provisionamento das VMs) e, desse modo, facilitar a criação de serviços em grande escala visando big compute, big data, resiliência e atender cargas de trabalho contidas.

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização. Para ver uma introdução aos conjuntos de  dimensionamento de VMs, confira o artigo recente [Comunicado no blog do Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Assista os vídeos abaixo para saber mais sobre conjuntos de dimensionamento de VM:

 - [Mark Russinovich fala sobre os Conjuntos de Escala do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)

 - [Conjuntos de Escala de Máquinas Virtuais com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Criando e gerenciando conjuntos de dimensionamento de VMs

Os conjuntos de dimentsionamento de VMs podem ser definidos e implantados usando um modelo JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx), assim como as VMs individuais do Gerenciador de Recursos do Azure (Azure Resource Manager ou ARM). Portanto, quaisquer métodos de implantação do Gerenciador de Recursos do Azure poderão ser usados. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Um conjunto de modelos de exemplo para conjuntos de dimensionamento de VMs poderão ser encontrado no repositório GitHub [aqui](https://github.com/Azure/azure-quickstart-templates) (busque por modelos com _vmss_ no título).
*VMSS = Virtual Machine Scale Set

Nas páginas de detalhes para esses modelos, você verá um botão que o levará para o recurso de implantação no portal do Azure. Para implantar o conjunto de escala de VMs, clique no botão e preencha todos os parâmetros obrigatórios no portal. Se você não tiver certeza se um recurso oferece suporte a letras maiúsculas ou combinadas, é mais seguro usar sempre valores de parâmetro em letras minúsculas. Há também um vídeo de que detalha um modelo de conjunto de escala de VMs aqui:

[Detalhamento do Modelo do Conjunto de Escala de VM](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## Expandindo e reduzindo um conjunto de dimensionamento de VMs

Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de dimensionamento de VMs, basta alterar a propriedade _capacity_ e reimplantar o modelo. Essa simplicidade facilita escrever sua própria camada de dimensionamento personalizada se você quiser definir eventos de escala personalizada que não têm suporte do dimensionamento automático do Azure.

Se estiver reimplantando um modelo para alterar a capacidade, você pode definir um modelo muito menor que inclui apenas a SKU e a capacidade atualizada. Veja um exemplo disso [aqui.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Para percorrer as etapas para criar um conjunto de dimensionamento que foi dimensionado automaticamente, confira [Dimensionar automaticamente as máquinas em um Conjunto de dimensionamento de máquina virtual](virtual-machine-scale-sets-windows-autoscale.md)

## Monitoramento de seu conjunto de dimensionamento de VM

O [Portal do Azure](https://portal.azure.com) lista os conjuntos de dimensionamento e mostra as propriedades básicas, bem como as VMs no conjunto. Para obter mais detalhes você pode usar o [Gerenciador de Recursos do Azure](https://resources.azure.com) para exibir os conjuntos de dimensionamento de VM. Os conjuntos de dimensionamento de VM são um recurso encontrado em Microsoft.Compute, de modo que nesse site, você pode vê-los expandindo os seguintes links:

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## Cenários do conjunto de dimensionamento de VM

Esta seção lista alguns cenários típicos de conjunto de dimensionamento de VMs. Além disso, alguns serviços do Azure de nível mais alto (como Lote (Batch), Service Fabric, Serviço de Contêiner do Azure) usarão esses cenários.

 - **RDP / SSH para instâncias de conjunto de dimensionamento de VM**: um conjunto de dimensionamento de VM é criado dentro de uma VNET e as VMs individuais dentro dele não terão endereços IP públicos alocados. Isso é bom, pois geralmente você não quer as despesas e os custos gerais de gerenciamento de alocar endereços IP públicos separados para todos os recursos dinâmicos, mas você pode se conectar facilmente a essas VMs à partir de outros recursos na sua que estejam na mesma VNET, incluindo aqueles que têm endereços IP públicos como balanceadores de carga ou outras máquinas virtuais que não pertençam ao conjunto.

 - **Conectar-se a VMs usando regras de NAT** -Você pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir regras NAT de entrada que mapeiam uma porta do endereço IP público para uma porta em uma máquina virtual no conjunto de dimensionamento de VMs. Por exemplo:
 
	Fonte | Porta de origem | Destino | Porta de destino
	--- | --- | --- | ---
	IP público | Porta 50000 | vmss\_0 | Porta 22
	IP público | Porta 50001 | vmss\_1 | Porta 22
	IP público | Porta 50002 | vmss\_2 | Porta 22

	Veja um exemplo de como criar um conjunto de dimensionamento de VM que usa regras NAT para habilitar a conexão SSH para cada VM em um conjunto de dimensionamento usando um único IP público: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

	Veja um exemplo de como fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Conectar às VMs usando uma "jumpbox"** - Se você criar um conjunto de dimensionamento de VM e uma VM autônoma na mesma rede virtual, a VM autônoma e o conjunto de dimensionamento de VM podem se conectar um ao outro usando seus endereços IP internos, conforme definido pela sub-rede/rede virtual. Se você criar um endereço IP público e atribuí-lo à VM autônoma, poderá realizar RDP ou SSH para essa VM autônoma e conectar-se à partir dessa máquina para as outras instâncias do conjunto de dimensionamento de VMs. Você pode perceber, nesse momento, que um simples conjunto de dimensionamento de VM é inerentemente mais seguro do que uma simples VM autônoma com um endereço IP público em sua configuração padrão.

	[Para obter um exemplo dessa abordagem, este modelo cria um cluster Mesos que consiste em uma VM mestre autônoma que gerencia um cluster de VMs baseado no conjunto de escala.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Balanceamento de carga para instâncias de conjunto de dimensionamento de VM** - Se desejar entregar trabalho a um cluster de VMs de cálculo usando uma abordagem "round-robin", você poderá configurar um balanceador de carga do Azure com regras de balanceamento de carga apropriadas. É possível definir investigações para verificar se o aplicativo está em execução ao executar ping de portas com um protocolo especificado, intervalo e caminho de solicitação. O Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) também oferece suporte aos conjuntos de dimensionamento, além de cenários de balanceamento de carga mais sofisticados.

	[Veja um exemplo que cria um conjunto de escalas de VM de VMs que executam o servidor Web IIS e usa um balanceador de carga para equilibrar a carga que cada VM recebe. Ele também usa o protocolo HTTP para executar ping de uma URL específica em cada VM ](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) (observe o tipo de recurso Microsoft.Network/loadBalancers, bem como networkProfile e extensionProfile no virtualMachineScaleSet)

 - **Implantar um conjunto de dimensionamento de VM como um cluster de computação em um gerenciador de cluster de PaaS** - Os conjuntos de dimensionamento de VM são muitas vezes descritos como uma função de trabalho de próxima geração. É uma descrição válida, mas há o risco de confundir recursos do conjunto de escalas com recursos da função de trabalho PaaS v1. De certo modo, os conjuntos de escalas de VM fornecem uma verdadeira "função de trabalho" ou recurso de trabalho, já que eles fornecem um recurso de computação generalizado que é independente de plataforma/tempo de execução, personalizável e se integra à IaaS do Gerenciador de Recursos do Azure.

	Uma função de trabalho PaaS v1, embora limitada em termos de suporte à plataforma/tempo de execução (apenas imagens da plataforma Windows) também inclui serviços como troca de VIP, definições de atualização configuráveis, configurações específicas de implantação de aplicativo/tempo de execução, que _ainda_ não estão disponíveis nos conjuntos de dimensionamento de VM ou serão fornecidos por outros serviços PaaS de nível mais alto, como o Service Fabric. Com isso em mente, você pode observar os conjuntos de escalas de VM como uma infraestrutura que oferece suporte à PaaS. Isto é, as soluções PaaS, como o Service Fabric ou os gerenciadores de clusters, como o Mesos, podem ser criadas com base nos conjuntos de escalas de VM como uma camada de computação escalonável.

	[Para obter um exemplo dessa abordagem, este modelo cria um cluster Mesos simples que consiste em uma VM mestre autônoma que gerencia um cluster de VMs baseado no conjunto de escala.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) As versões futuras do [Serviço de Contêiner do Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implantarão versões mais complexas/reforçadas desse cenário com base nos conjuntos de dimensionamento de VM.

## Orientação de dimensionamento e desempenho do conjunto de dimensionamento de VM

- Não crie mais de 500 VMs em vários Conjuntos de Escalas de VM por vez.
- Planeje no máximo 20 VMs por conta de armazenamento (a menos que você defina a propriedade _provisionamento excessivo_ como "false", quando então que você pode ir até 40).
- Diversifique o máximo possível as primeiras letras dos nomes da conta de armazenamento. Os modelos de VMSS de exemplo nos [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) fornecem exemplos de como fazer isso.
- Se estiver usando VMs personalizadas, planeje-se para ter não mais que 40 VMs por conjunto de escalas de VM, em uma única conta de armazenamento. Você precisará da imagem previamente copiada na conta de armazenamento para poder começar a implantação do conjunto de escalas de VM. Consulte as Perguntas frequentes para obter mais informações.
- Planeje-se para ter não mais que 4.096 VMs por rede virtual.
- O número de VMs que você pode criar é limitado pela cota de núcleos na região em que você está implantando. Talvez seja necessário contatar o Atendimento ao Cliente para aumentar o limite da cota de computação mesmo que hoje você tenha um limite alto de núcleos para uso com serviços de nuvem ou IaaS v1. Para consultar sua cota, você pode executar o seguinte comando da CLI do Azure: `azure vm list-usage` e o seguinte comando do PowerShell: `Get-AzureRmVMUsage` (se estiver usando uma versão do PowerShell abaixo de 1.0, use `Get-AzureVMUsage`).

## Conjunto de dimensionamento de VM - Perguntas frequentes

**P.** Quantas VMs você pode ter em um conjunto de dimensionamento de VM?

**A.** 100, se você usar imagens de plataforma que podem ser distribuídas entre várias contas de armazenamento. Se você usar imagens personalizadas, até 40 (se a propriedade _provisionamento excessivo_ estiver definida como “false”, 20 por padrão), visto que as imagens personalizadas são limitadas a uma única conta de armazenamento.

**P** Que outros limites de recurso existem para conjuntos de dimensionamento de VM?

**A.** Você está limitado a criar no máximo 500 VMs nos vários conjuntos de escala por região durante um período de 10 minutos. Os [Limites de serviço de assinatura do Azure/](../azure-subscription-service-limits.md) existentes se aplicam.

**P.** Há suporte para os discos de dados nos conjuntos de dimensionamento de VM?

**A.** Não na versão inicial. Suas opções de armazenamento de dados são:

- Arquivos do Azure (unidades compartilhada de SMB)

- Unidade do sistema operacional

- Unidade TEMP (local, não tem relação com o armazenamento do Azure)

- Serviço de dados do Azur (por exemplo, Tabelas e Blobs do Azure)

- Serviço de dados externo (por exemplo, Banco de Dados Remoto)

**P.** Quais são as regiões do Azure que dão suporte aos conjuntos de escala de VM?

**A.** Qualquer região que ofereça suporte ao Gerenciador de Recursos do Azure oferece suporte aos conjuntos de escalas de VM.

**P.** Como criar um conjunto de escala de VM usando uma imagem personalizada?

**A.** Deixe a propriedade vhdContainers em branco, por exemplo:

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
			},
			"osType": "Windows"
		}
	},


**P.** Se eu reduzir a capacidade do meu conjunto de dimensionamento de VM de 20 para 15, quais VMs serão removidas?

**A.** Máquinas virtuais são removidas do conjunto de dimensionamento uniformemente entre domínios de atualização e domínios de falha para maximizar a disponibilidade. VMs com as IDs mais altas são removidas primeiro.

**P.** E se eu aumentar a capacidade de 15 para 18?

**A.** Se você aumentar a capacidade para 18, serão criadas três novas VMs. Cada vez que a ID da instância VM será incrementada do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre FDs e UDs.

**P.** Ao usar várias extensões em um conjunto de dimensionamento de VM, posso impor uma sequência de execução?

**A.** Não diretamente, mas para a extensão de customScript, o script poderia aguardar por outra extensão ser concluída ([por exemplo, monitorando o log de extensão](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Encontre orientações adicionais sobre o sequenciamento de extensão nesta postagem de blog: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Sequenciamento de extensão em conjuntos de dimensionamento de VM do Azure).

**P.** Os conjuntos de dimensionamento de VM funcionam com os conjuntos de disponibilidade do Azure?

**A.** Sim. Um conjunto de escala de VM é um conjunto de disponibilidade implícito com 5 FDs e 5 UDs. Não é preciso configurar nada em virtualMachineProfile. Em versões futuras, os conjuntos de dimensionamento de VM provavelmente incluirão vários locatários, mas, por enquanto, um conjunto de dimensionamento é um único conjunto de disponibilidade.

<!---HONumber=AcomDC_0727_2016-->
