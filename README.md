Para o seu projeto, aqui está um exemplo de README estruturado para explicar o funcionamento do código, os dados e as análises realizadas:

---

# Dividendos- Automáticos 

Este projeto visa analisar o desempenho de uma estratégia baseada em dividendos de empresas (DY) e comparar seu retorno acumulado com o índice Ibovespa. Utilizando bibliotecas como `pandas` e `quantstats`, o script avalia a performance de uma carteira que prioriza empresas com maiores rendimentos de dividendos ao longo do tempo.

## Dados Utilizados

1. **dados_empresas.csv**: Contém informações históricas sobre o fechamento ajustado, volume negociado, e dividend yield (DY) das empresas listadas.
2. **ibov.csv**: Inclui os dados históricos do índice Ibovespa para a mesma linha temporal, permitindo comparação de desempenho.

## Estrutura do Código

1. **Importação de Dados**: Carrega e prepara os dados das empresas e do índice Ibovespa.
2. **Cálculo de Retorno das Ações**: 
    - Calcula o retorno diário das ações baseado no preço de fechamento ajustado.
    - Filtra empresas com volume negociado superior a 1.000.000.
3. **Ranking por Dividend Yield**:
    - Rankea as empresas com base no DY, selecionando as 10 principais para cada mês.
4. **Cálculo de Retorno da Carteira**:
    - Calcula o retorno médio mensal das 10 empresas com maior DY.
    - Cria uma série acumulada da estratégia de dividendos, considerando reinvestimento do retorno.
5. **Comparação com o Ibovespa**:
    - Calcula o retorno acumulado do Ibovespa para o mesmo período.
6. **Visualização dos Resultados**:
    - Utiliza `quantstats` para gerar um mapa de calor mensal dos retornos para a estratégia de dividendos e para o Ibovespa.

## Pré-requisitos

Este projeto foi desenvolvido em Python e utiliza as seguintes bibliotecas:
- **pandas**: para manipulação e análise de dados.
- **quantstats**: para visualização dos retornos e análise de performance.

### Instalação das Bibliotecas

```bash
pip install pandas quantstats
```

## Como Executar o Projeto

1. **Carregue os dados**: Verifique se os arquivos `dados_empresas.csv` e `ibov.csv` estão no mesmo diretório do código.
2. **Execute o script**: O script calculará automaticamente os retornos, filtrará as ações e comparará os resultados.
3. **Análise Visual**: O mapa de calor mensal para a estratégia e o Ibovespa será exibido.

## Exemplo de Código

```python
import pandas as pd
import quantstats as qs

# Carregar e processar dados
dados_empresas = pd.read_csv("dados_empresas.csv")
dados_empresas["Retorno"] = dados_empresas.groupby("ticker")["preco_fechamento_ajustado"].pct_change()
dados_empresas["Retorno"] = dados_empresas.groupby("ticker")["Retorno"].shift(-1)
dados_empresas = dados_empresas[dados_empresas["volume_negociado"] > 1000000]
dados_empresas["ranking_dy"] = dados_empresas.groupby("data")["dy"].rank(ascending=False)
dados_empresas = dados_empresas[dados_empresas["ranking_dy"] <= 10]

# Calcular retorno da carteira de dividendos
rentabilidade_por_carteiras = dados_empresas.groupby("data")["Retorno"].mean().to_frame()
rentabilidade_por_carteiras['estrategia_dy'] = (rentabilidade_por_carteiras['Retorno'] + 1).cumprod() - 1
rentabilidade_por_carteiras = rentabilidade_por_carteiras.shift(1).dropna()

# Carregar dados do Ibovespa e comparar
ibov = pd.read_csv("ibov.csv")
retornos_ibov = ibov['fechamento'].pct_change().dropna()
retorno_acum_ibov = (1 + retornos_ibov).cumprod() - 1
rentabilidade_por_carteiras['Ibovespa'] = retorno_acum_ibov.values
rentabilidade_por_carteiras = rentabilidade_por_carteiras.drop('Retorno', axis=1)

# Visualização
qs.extend_pandas()
rentabilidade_por_carteiras.index = pd.to_datetime(rentabilidade_por_carteiras.index)
rentabilidade_por_carteiras['estrategia_dy'].plot_monthly_heatmap()
rentabilidade_por_carteiras['Ibovespa'].plot_monthly_heatmap()
```

## Resultados Esperados

A saída do script será uma visualização do desempenho da carteira baseada em dividendos e do índice Ibovespa, mostrando o desempenho em cada mês através de um mapa de calor. Isso permitirá avaliar a eficácia da estratégia e comparar com o mercado geral.

## Observações

- O dataset utilizado deve conter uma cobertura ampla do mercado e incluir empresas de diversos setores para um melhor entendimento.
- O volume negociado de cada ativo é uma métrica importante para garantir a liquidez e a robustez da carteira.
