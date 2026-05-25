# azure-monitor-workbooks

Workbook que realiza inventário de recursos e avalia métricas nativas do Azure Monitor

🌐 **Read this in other languages:** [English](./README.en.md)

## 📌 Finalidade do projeto

Este repositório tem como objetivo disponibilizar **Workbooks do Azure Monitor** voltados ao **inventário de recursos** e à **avaliação de métricas** em ambientes Azure, permitindo:

- Consolidar a visão dos recursos provisionados em uma ou mais assinaturas;
- Avaliar **métricas nativas** de utilização, saúde e desempenho (sem dependência de agentes);
- Apoiar atividades de **governança**, **FinOps** e **observabilidade**;
- Identificar **oportunidades** de otimização (custo, dimensionamento, recursos ociosos);
- Servir como base para personalizações e evoluções conforme a necessidade de cada ambiente.

> ⚠️ O projeto ainda está em **fase de desenvolvimento**. Estruturas, consultas (KQL/ARG) e visualizações poderão sofrer alterações.

## 📄 Sobre o arquivo `workbook.json`

O arquivo [`workbook.json`](./workbook.json) contém a **definição serializada do Workbook** do Azure Monitor utilizado neste projeto. Ele segue o formato padrão de exportação/importação de Workbooks e descreve:

- **Parâmetros globais** de entrada (assinaturas, grupos de recursos, intervalo de tempo, aba selecionada);
- **Consultas** (Azure Resource Graph / Kusto) que coletam o inventário e as informações dos recursos;
- **Métricas nativas** do Azure Monitor (CPU, memória, disco, rede, requests, latência etc.);
- **Visualizações** (tabelas, gráficos de linha, barras, pizza e tiles/KPIs) organizadas em abas.

### 🧭 Estrutura do Workbook

O Workbook está organizado em **abas (tabs)** controladas pelo parâmetro `selectedTab`:

| Aba | Conteúdo principal |
|-----|--------------------|
| 📊 **Executivo** | Visão consolidada de inventário: recursos por tipo, distribuição por região, VMs por tamanho e App Service Plans por SKU/Tier. |
| 💻 **Máquinas Virtuais** | Inventário de VMs (Em Execução / Paradas / Desalocadas), KPIs de CPU e memória, e gráficos de CPU (média e pico), memória (% e bytes disponíveis), disco OS/Data (bytes e IOPS) e rede (entrada/saída). |
| 🌐 **App Services** | Lista de App Services, métricas de **App Service Plan** (CPU % e Memória %), e métricas de **App** (Working Set, Requests, Http4xx e demais erros/latência). |
| 🗄️ **Storage** | Métricas e inventário de contas de armazenamento. |
| 🛢️ **Databases PaaS** | Métricas e inventário de bancos de dados PaaS. |
| 💡 **Oportunidades** | Sugestões de otimização baseadas em inventário e métricas. |

### 🎛️ Parâmetros globais

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `Subscription` | Assinatura | Seleção de uma ou mais assinaturas (multi-select, padrão `value::all`). |
| `ResourceGroup` | Grupo de Recursos | Filtra por um ou mais grupos de recursos da(s) assinatura(s) selecionada(s). |
| `TimeRange` | Intervalo de Tempo | Período de análise (padrão **7 dias**; também 15, 30 e 60 dias; permite intervalo customizado). |
| `selectedTab` | Interno | Controla qual aba está visível. |

Cada aba pode ainda possuir parâmetros próprios (por exemplo, seleção de **VMs**, **App Services** ou **App Service Plans**) para filtrar as métricas exibidas.

### ⚠️ Limitações conhecidas

- **VMs — CPU, Disco e Rede:** baseiam-se em métricas de **host** do Azure Monitor (sem agente).
- **VMs — Memória:** os gráficos de memória usam as métricas de host *Available Memory Percentage* e *Available Memory Bytes* (memória **disponível**, não a "usada" pelo SO/processos). Para métricas de **guest** (consumo real do SO) é necessário habilitar o **Azure Monitor Agent** (AMA) ou Diagnostics.
- **App Service Plan — CPU % / Memória %:** disponíveis apenas em tiers **Standard ou superiores** (não aparecem em tiers Free/Shared/Basic).

### Como utilizar

1. Acesse o portal do Azure: **Azure Monitor → Workbooks → + Novo**.
2. Clique no ícone **</> (Editor avançado)**.
3. Selecione o tipo **Gallery Template (ARM)**.
4. Copie o conteúdo do arquivo [`workbook.json`](./workbook.json) e cole no editor.
5. Clique em **Aplicar** e, em seguida, em **Salvar** para persistir o Workbook na sua assinatura/grupo de recursos.
6. No Workbook aberto, ajuste **Assinatura**, **Grupo de Recursos** e **Período**, e navegue pelas abas conforme a necessidade.

### Pré-requisitos

- Permissões de leitura nos recursos a serem inventariados (ex.: `Reader`);
- Acesso ao **Azure Monitor** e ao **Azure Resource Graph**;
- Para métricas de **guest** de VMs (memória usada, processos etc.): **Azure Monitor Agent (AMA)** habilitado nas VMs.

## 📜 Licença

Distribuído sob a licença **MIT**. Veja o arquivo [LICENSE](./LICENSE) para mais detalhes.

## ✍️ Autoria

Projeto desenvolvido e mantido por **[Erick Medeiros](https://github.com/ErickMedeiros)**.

Contribuições, sugestões e issues são bem-vindas!
