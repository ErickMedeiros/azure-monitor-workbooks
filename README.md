# azure-monitor-workbooks

Workbook que realiza inventário de recursos para avaliar métricas — ainda em fase de desenvolvimento.

## 📌 Finalidade do projeto

Este repositório tem como objetivo disponibilizar **Workbooks do Azure Monitor** voltados ao **inventário de recursos** em ambientes Azure, permitindo:

- Consolidar a visão dos recursos provisionados em uma ou mais assinaturas;
- Avaliar **métricas** relevantes de utilização, saúde e desempenho;
- Apoiar atividades de **governança**, **FinOps** e **observabilidade**;
- Servir como base para personalizações e evoluções conforme a necessidade de cada ambiente.

> ⚠️ O projeto ainda está em **fase de desenvolvimento**. Estruturas, consultas (KQL/ARG) e visualizações poderão sofrer alterações.

## 📄 Sobre o arquivo `workbook.json`

O arquivo [`workbook.json`](./workbook.json) contém a **definição serializada do Workbook** do Azure Monitor utilizado neste projeto. Ele segue o formato padrão de exportação/importação de Workbooks (Gallery Template), incluindo:

- Parâmetros de entrada (assinaturas, grupos de recursos, intervalos de tempo etc.);
- Consultas (Azure Resource Graph / Kusto) que coletam o inventário e as métricas;
- Visualizações (tabelas, gráficos e tiles) que organizam os resultados.

### Como utilizar

1. Acesse o portal do Azure: **Azure Monitor → Workbooks → + Novo**.
2. Clique no ícone **</> (Editor avançado)**.
3. Selecione o tipo **Gallery Template (ARM)**.
4. Copie o conteúdo do arquivo [`workbook.json`](./workbook.json) e cole no editor.
5. Clique em **Aplicar** e, em seguida, em **Salvar** para persistir o Workbook na sua assinatura/grupo de recursos.

### Pré-requisitos

- Permissões de leitura nos recursos a serem inventariados (ex.: `Reader`);
- Acesso ao **Azure Monitor** e ao **Azure Resource Graph**.

## ✍️ Autoria

Projeto desenvolvido e mantido por **[Erick Medeiros](https://github.com/ErickMedeiros)**.

Contribuições, sugestões e issues são bem-vindas!
