# Aplicação de Ciência de Dados na Indústria 4.0
### Previsão de Demanda para o Planejamento da Produção

**Centro Universitário Católica de Santa Catarina**
Disciplina: **Inteligência Artificial** — Avaliação N2 (Ciência de Dados)
Professor: **Claudinei Dias (Ney)**

**Alunos:** Rodrigo Klappoth · Henrique Klappoth

---

## Sobre o trabalho

Este repositório aplica o ciclo completo de ciência de dados — coleta, preparação, modelagem e
análise — a um problema real de Indústria 4.0: **prever a demanda com 90 dias de antecedência para
alimentar o Plano Mestre de Produção (MPS)**.

O trabalho parte de uma base pública **real** com **913.000 registros** de demanda diária (5 anos,
10 unidades, 50 SKUs), caracteriza os padrões da série, constrói atributos preditivos sem vazamento de
informação, compara quatro abordagens de previsão e traduz o resultado em dois artefatos de decisão
diretamente utilizáveis pelo PCP: **plano agregado de produção** e **estoque de segurança por SKU**.

## Resultados principais

| Modelo | MAE | RMSE | WMAPE | Ganho vs. heurística |
|---|---:|---:|---:|---:|
| Naïve sazonal (52 semanas) — *prática atual* | 8,55 | 11,15 | 14,54 % | — |
| Média móvel de 364 dias | 12,19 | 16,21 | 20,72 % | −42,5 % |
| Regressão Ridge | 6,94 | 9,01 | 11,79 % | +18,9 % |
| **Histogram Gradient Boosting** | **6,35** | **8,22** | **10,80 %** | **+25,7 %** |

Destaques:

- **WMAPE de 10,80 %** no nível SKU × unidade e **3,24 %** no nível agregado da planta — o chamado
  *efeito de agregação*, que autoriza decisões de capacidade com alta confiança.
- **Redução de 25,7 % do erro** em relação à heurística usada hoje no planejamento.
- **Redução de 27,6 % do estoque de segurança** necessário para sustentar o mesmo nível de serviço
  (95 %) — o valor econômico direto da previsão mais precisa.
- Desempenho **estável** em quatro janelas de validação temporal (desvio-padrão do WMAPE: 0,63 p.p.).

## Estrutura do repositório

```
.
├── previsao_demanda_industria40.ipynb   # Notebook principal (trabalho completo, já executado)
├── dados/
│   └── demanda_diaria.csv               # Base de 913.000 registros (2013–2017)
├── imagens/                             # Gráficos gerados pelo notebook
├── requirements.txt                     # Dependências
└── README.md
```

## Como executar

### Google Colab (sem instalar nada)

Abra o notebook pelo Colab e execute `Ambiente de execução → Executar tudo`. Caso o arquivo de dados
não esteja presente, o próprio notebook faz o download automático da base pública.

### Localmente

```bash
git clone <url-deste-repositorio>
cd n2-ciencia-dados-industria40
pip install -r requirements.txt
jupyter notebook previsao_demanda_industria40.ipynb
```

Tempo total de execução: cerca de 1 a 3 minutos.

## Metodologia em resumo

**Dados** — *Store Item Demand Forecasting Challenge* (Kaggle, 2018): 913.000 registros diários de
01/01/2013 a 31/12/2017, cobrindo 10 unidades × 50 SKUs (500 séries temporais). Painel balanceado, sem
valores ausentes, duplicidades ou datas faltantes.

**Preparação** — padronização, ordenação por série e construção de 18 atributos preditivos em três
famílias: defasagens (91, 98, 105, 112, 364 e 371 dias), estatísticas móveis (médias de 28/91/364 dias
e desvio-padrão de 91 dias) e variáveis de calendário.

> **Restrição metodológica central:** como o horizonte de previsão é de 90 dias, **nenhum atributo usa
> informação mais recente do que 91 dias antes da data prevista**. Isso reduz o desempenho aparente,
> mas garante que o resultado medido seja reproduzível em operação real — e não um artefato de
> *data leakage*.

**Modelagem** — estratégia de **modelo global**: as 500 séries são tratadas como um único problema de
regressão supervisionada tabular, com identificadores de unidade e SKU como atributos. Comparam-se duas
heurísticas de referência, um modelo linear regularizado (Ridge, com padronização) e um *ensemble* de
árvores (Histogram Gradient Boosting).

**Validação** — estritamente temporal: *hold-out* com treino em 2013–2016 e teste no ano completo de
2017 (182.500 registros), complementado por validação de **origem móvel** em quatro janelas sucessivas.

**Métricas** — MAE, RMSE, SMAPE e **WMAPE** (métrica principal, por ser ponderada pelo volume e estável
quando a demanda se aproxima de zero), além do **viés percentual**, que revela superprodução ou
subprodução sistemática.

## Conteúdo do notebook

1. **Introdução** — Indústria 4.0, definição do problema e objetivos
2. **Metodologia** — descrição dos dados, preparação, ferramentas e métodos de análise
3. **Desenvolvimento** — qualidade dos dados, análise exploratória, engenharia de atributos,
   implementação dos modelos, análise dos resultados, importância das variáveis, resíduos, validação
   cruzada temporal, aplicação prática e discussão de eficácia
4. **Conclusões** — conclusões, limitações e trabalhos futuros
5. **Referências**

## Tecnologias

`Python 3` · `pandas` · `NumPy` · `Matplotlib` · `scikit-learn` · `statsmodels` · `Jupyter`

## Fonte dos dados

KAGGLE. *Store Item Demand Forecasting Challenge*, 2018 —
<https://www.kaggle.com/c/demand-forecasting-kernels-only>
Cópia utilizada: <https://github.com/ugursaricam/store-item-demand-forecasting>

---

<div align="center">
Rodrigo Klappoth · Henrique Klappoth — 2026
</div>
