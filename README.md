#Engenheiro de Dados
**Autor:** Thales Dias Braga

Este projeto consiste na implementação de um pipeline de dados utilizando **Databricks**, focado no processamento de dados estatísticos (populacionais) de arquivos XML para uma arquitetura de Lakehouse.


##  Descrição da Solução

O pipeline foi desenhado seguindo a Arquitetura Medalhão, processando os dados em camadas para garantir qualidade e performance:

1.  **Configuração e Setup:** Definição de caminhos para armazenamento utilizando Databricks Volumes e limpeza de ambientes para re-execuções e testes do pipeline.
2.  **Ingestão (Camada Bronze):**
    *   Leitura do arquivo fonte em formato XML (`all.xml`).
    *   Transformação imediata dos dados brutos para o formato **Delta Lake**.
    *   Adição de metadados de ingestão (data de processamento e arquivo de origem).

O objetivo principal desta etapa inicial é converter o dado semi-estruturado (XML) para um formato otimizado (Delta), que permita consultas rápidas e manipulação nas etapas subsequentes (Silver e Gold).

## Instruções Básicas de Execução

### Pré-requisitos
*   Acesso a um ambiente **Databricks** com Unity Catalog habilitado (devido ao uso de `/Volumes`).
*   Cluster ativo para execução de notebooks Spark/Python.

### Configuração do Arquivo de Entrada
Certifique-se de que o arquivo de dados `all.xml` esteja carregado no seguinte caminho do Volume (ou ajuste a variável `input_path` no notebook):




### Executando o Pipeline
1.  Importe o notebook para o seu Workspace do Databricks.
2.  Verifique se os caminhos definidos na célula de configuração (`path_bronze`, `path_silver`, `path_gold`) existem ou podem ser criados pelo usuário que executa o script.
3.  Execute o notebook selecionando a opção **"Run All"**.

> **Nota:** O script está configurado para **apagar** os dados existentes nas pastas de destino a cada execução (`dbutils.fs.rm`), ideal para testes e desenvolvimento. Para produção, esta lógica deve ser alterada.

## Decisões Adotadas

Abaixo estão as justificativas para as principais decisões técnicas observadas no código:

*   **Arquitetura Medalhão (Bronze/Silver/Gold):**
    *   A separação em camadas (`path_bronze`, `path_silver`, `path_gold`) foi adotada para organizar o fluxo de dados, separando o dado bruto (Bronze) do dado tratado e do dado agregado para negócios.

*   **Uso de Databricks Volumes:**
    *   A utilização de caminhos iniciados por `/Volumes/xxx/xxx` indica o uso do Unity Catalog, o que garante facilidade de acesso aos arquivos como se fossem um sistema de arquivos local.

*   **Conversão XML para Delta (Bronze):**
    *   A decisão de converter o XML para Delta logo na camada Bronze visa **performance**. Ler arquivos XML pode ser muito lento além deles não funcionarem bem com processamento paralelo. Já o formato Delta oferece transações ACID, compactação e leitura mais rápida para as próximas etapas.


*   **Limpeza Automática (Idempotência de Desenvolvimento):**
    *   O comando de limpeza inserido no início do script garante que o ambiente esteja "limpo" para validar a lógica de ponta a ponta, sem duplicidade de dados durante a fase de desenvolvimento e avaliação técnica do pipeline.
