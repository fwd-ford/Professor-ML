Integrantes:

1	Lucca Borges	RM: 554608

2	Ruan Melo	RM: 557599

3	Rodrigo Jimenez	RM: 558148

4	João Victor Franco	RM: 556790


## 📦 Conteúdo desta pasta

```
Professor-ML/
├── analise_completa.ipynb     ← Notebook único com todo o trabalho (Partes 1-7)
├── relatorio.pdf              ← Relatório executivo em PDF (formato pedido no enunciado)
├── relatorio.docx             ← Mesma versão em Word (se preferir editar)
├── README.md                  ← Este arquivo
├── dados/
│   └── ford_clientes_historico_completo.csv   ← Base de dados (500k clientes × 37 colunas)
└── figuras/                   ← 8 PNGs gerados pelo notebook
    ├── 01_eda_base2.png
    ├── 02_eda_categoricas.png
    ├── 03_elbow_silhouette.png
    ├── 04_heatmap_clusters.png
    ├── 05_validacao_cluster.png
    ├── 06_comparacao_modelos.png
    ├── 07_matriz_confusao.png
    └── 08_feature_importance.png
```

> **Sobre o formato do relatório:** o enunciado pede literalmente **PDF**
> ("um arquivo PDF com os achados do time"). Mantemos as duas versões pra você
> escolher conforme a instrução específica do professor — o conteúdo é idêntico.

---

## 🎯 Resumo do que foi entregue (segundo enunciado)

| Parte | Pedido | Entregue |
|---|---|:---:|
| **1** | Entendimento do negócio + EDA + preparação dos dados | ✅ |
| **2** | Segmentação de clientes (clustering) com Base 1 | ✅ K-means k=4 |
| **3** | Interpretação semântica dos clusters (fiel/abandono/esquecido/econômico) | ✅ ARI 0.60 |
| **4** | Estratégia de retenção por segmento | ✅ 4 ações detalhadas |
| **5** | Transformação dos segmentos em target supervisionado | ✅ multiclasse |
| **6** | Modelagem preditiva com Base 2 (sem leak) | ✅ XGBoost F1 macro 0.70 |
| **7** | Leitura executiva — 5 perguntas-chave | ✅ |
| **Entregáveis** | 1 Jupyter Notebook + 1 PDF | ✅ |

---

## 🚀 Como rodar

### Requisitos
```bash
pip install pandas numpy scikit-learn xgboost matplotlib seaborn jupyter
```

### Reproduzir
```bash
# Opção 1 — Jupyter interativo
jupyter notebook analise_completa.ipynb

# Opção 2 — Headless (re-executa do início, regenera figuras)
jupyter nbconvert --to notebook --execute analise_completa.ipynb --output analise_completa_executed.ipynb
```

**Reprodutibilidade:** todas as seeds estão fixas em `42`. Mesma entrada → mesma saída.

---

## 📊 Resultados-chave

### Etapa 1 — Segmentação K-means

- **k=4** (escolhido via elbow + silhouette + aderência ao negócio)
- **ARI = 0.5959** contra o ground truth `perfil_latente`
- **NMI = 0.5798**

| Cluster | n | % | Churn observado | Concordância c/ ground truth |
|---|---:|---:|---:|---|
| **fiel** | 153.339 | 30.7% | <1% | 94.1% bate fiel real |
| **econômico** | 112.956 | 22.6% | ~6% | 81.2% bate econômico real |
| **esquecido** | 105.429 | 21.1% | ~14% | 69.3% bate esquecido real |
| **abandono** | 128.276 | 25.7% | ~50% | 69.7% bate abandono real |

### Etapa 2 — Classificação multiclasse (XGBoost, Base 2 only)

**Comparação de 3 algoritmos:**

| Modelo | Accuracy | F1 macro | F1 weighted |
|---|---:|---:|---:|
| Logistic Regression | 0.7180 | 0.6870 | 0.7106 |
| Random Forest | 0.7194 | 0.6872 | 0.7106 |
| **XGBoost ⭐** | **0.7266** | **0.7012** | **0.7233** |

**Performance do XGBoost por classe:**

| Classe | Precision | Recall | F1 | Suporte |
|---|---:|---:|---:|---:|
| fiel | 0.93 | 0.93 | **0.93** | 30.672 |
| esquecido | 0.70 | 0.71 | 0.71 | 25.651 |
| econômico | 0.64 | 0.73 | 0.68 | 22.591 |
| abandono | 0.53 | 0.44 | 0.48 | 21.086 |

---

## ✅ Regra crítica do enunciado: SEM data leakage

A modelagem supervisionada (Etapa 2) usa **APENAS** as 23 variáveis disponíveis no momento da compra:

```
idade, renda_mensal, score_credito, distancia_concessionaria_km, regiao,
uso_principal, km_estimado_mes, teve_ford_antes, canal_compra,
categoria_veiculo, modelo_veiculo, valor_veiculo, usou_troca, entrada_pct,
prazo_financiamento_meses, compra_a_vista, prestacao_renda_ratio,
garantia_estendida, plano_manutencao, aceitou_marketing,
sensibilidade_preco_inicial, organizacao_proxy, tempo_decisao_dias
```

**Variáveis EXCLUÍDAS (banidas pelo enunciado):**

```
fez_primeira_revisao_rede, meses_ate_primeira_revisao, perdeu_primeira_revisao,
voltou_tarde_revoltado, trouxe_oleo_externo, pede_desconto_revisao,
sensibilidade_desconto_pos, qtde_revisoes_24m, share_revisoes_rede_24m,
gasto_manutencao_rede_24m, satisfacao_marca_24m, churn_rede_24m
```

Estas últimas só são usadas na **Etapa 1 (segmentação)**, conforme regra crítica do PDF do enunciado.

---

## 🏗️ Estrutura do notebook

O notebook `analise_completa.ipynb` está organizado nas 7 partes pedidas pelo enunciado, com **50 células** (26 código + 24 markdown):

1. **Parte 1** — Entendimento + EDA + preparação (8 cells)
2. **Parte 2** — Segmentação K-means (4 cells)
3. **Parte 3** — Interpretação semântica + validação contra ground truth (6 cells)
4. **Parte 4** — Estratégia de retenção por segmento (3 cells)
5. **Parte 5** — Transformação cluster → target supervisionado (2 cells)
6. **Parte 6** — Modelagem preditiva multiclasse (8 cells)
7. **Parte 7** — Leitura executiva (4 cells)

---
