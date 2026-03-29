# Priorização de Infraestrutura Urbana Preventiva

**Drenagem, Saneamento e Contenção de Encostas**

Trabalho desenvolvido no âmbito do **MBA em Ciência de Dados e Inteligência Artificial Aplicadas (2025)** da Escola Nacional de Administração Pública — ENAP, na Disciplina Integradora Aplicada: Laboratório de Casos — Políticas Públicas de Infraestrutura.

---

## Sobre o projeto

Este repositório implementa um pipeline de análise de dados para **priorização de municípios brasileiros** em investimentos de infraestrutura urbana preventiva, no contexto da Política Nacional de Proteção e Defesa Civil (PNPDEC — Lei nº 12.608/2012).

O pipeline integra três fontes de dados federais oficiais para responder à pergunta central:

> *Os municípios identificados como mais vulneráveis pelo modelo são de fato os mais expostos a desastres aderentes ao tema de infraestrutura urbana preventiva?*

### Duas etapas complementares

| Etapa | Notebook | Descrição |
|---|---|---|
| 1 — Priorização | `01_infraestrutura.ipynb` | Ingestão, governança LGPD e score composto IVS + CadÚnico |
| 2 — Validação | `02_infraestrutura_validacao.ipynb` | Cruzamento com histórico de desastres (SEDEC/COBRADE) e score ampliado |

---

## Estrutura do repositório

```
pnpdec-priorizacao-infraestrutura/
│
├── notebooks/
│   ├── 01_infraestrutura.ipynb        # Pipeline base: IVS + CadÚnico
│   └── 02_infraestrutura_validacao.ipynb  # Validação empírica
│
├── data/
│   ├── README.md                      # Fontes, licenças e instruções de download
│   ├── raw/
│   │   └── cobrade.csv                # Tabela de referência COBRADE (versionada)
│   └── processed/                     # Outputs gerados pelos notebooks
│       ├── municipios_priorizados.csv
│       ├── zonas_vermelhas.csv
│       ├── municipios_invisiveis.csv
│       └── municipios_score_ampliado.csv
│
├── graficos/
│   ├── graf1_correlacao.png
│   ├── graf2_invisiveis.png
│   └── graf3_score_ampliado.png
│
├── relatorio/
│   └── relatorio_infraestrutura.docx
│
├── .gitignore
└── README.md
```

---

## Dados utilizados

Os microdados brutos **não estão versionados** neste repositório por restrições de tamanho. Faça o download direto nas fontes abaixo antes de executar os notebooks.

| Base | Fonte | Como obter |
|---|---|---|
| Atlas de Vulnerabilidade Social (IVS) | IPEA | [ivs.ipea.gov.br](https://ivs.ipea.gov.br) — baixar `atlasivs_dadosbrutos_pt_v2.xlsx` |
| CadÚnico — amostra dez/2018 | MDS/SAGICAD | [aplicacoes.mds.gov.br/sagi](https://aplicacoes.mds.gov.br/sagi/portal/) |
| Relatório Gerencial de Resposta a Desastres | MIDR/SEDEC | [s2id.mi.gov.br](https://s2id.mi.gov.br) — exportar relatório gerencial |
| COBRADE | MIDR/SEDEC | Incluído em `data/raw/cobrade.csv` |

Após o download, salve os arquivos em `data/raw/` ou no caminho equivalente no Google Drive conforme indicado nos notebooks.

---

## Como reproduzir

Os notebooks foram desenvolvidos para execução no **Google Colab** com dados no Google Drive.

**1. Clone o repositório**
```bash
git clone https://github.com/seu-usuario/pnpdec-priorizacao-infraestrutura.git
```

**2. Suba os dados brutos para o Google Drive**

Coloque os arquivos baixados em:
```
MyDrive/ENAP/dados_pnpdec/
```

**3. Execute os notebooks na ordem**

Abra cada notebook no Google Colab e execute as células em sequência:

```
01_infraestrutura.ipynb        → gera municipios_priorizados.csv e zonas_vermelhas.csv
02_infraestrutura_validacao.ipynb → gera os demais outputs e gráficos
```

> O notebook 2 depende dos outputs do notebook 1. Execute o notebook 1 primeiro e confirme que os arquivos CSV foram salvos no Drive antes de iniciar o notebook 2.

**Dependências Python**
```
pandas
numpy
matplotlib
seaborn
scipy
openpyxl
requests
```

Instalação automática: cada notebook instala as dependências via `pip` na primeira célula de execução.

---

## Principais resultados

| Indicador | Valor |
|---|---|
| Municípios analisados | 5.529 |
| Faixa ALTA — score original | 30 |
| Faixa ALTA — score ampliado | 453 |
| Faixa CRÍTICA — score ampliado | 6 |
| Municípios com histórico de desastre aderente | 384 (6,9%) |
| Municípios prioritários invisíveis para o sistema federal | 577 (81,7%) |
| Correlação Spearman score × n_eventos | ρ = 0,078 (p < 0,0001) |

---

## Decisões metodológicas documentadas

- **Spearman em vez de Pearson** para correlação: a variável `n_eventos_aderentes` tem distribuição de contagem fortemente assimétrica, violando os pressupostos de normalidade exigidos por Pearson.
- **Filtragem COBRADE**: apenas os 11 códigos aderentes ao tema (movimentos de massa e eventos hidrológicos — inundações, enxurradas, alagamentos) foram considerados. Outros tipos de desastre (estiagem, seca, vendaval) têm causas e soluções distintas e sua inclusão contaminaria a análise.
- **Governança LGPD**: os microdados do CadÚnico passaram por pseudoanonimização SHA-256 com salt rotativo, k-anonimato (mínimo 5 famílias/município) e remoção de todos os campos pessoais antes de qualquer operação analítica.
- **Score ampliado**: incorpora exposição histórica a desastres (peso 20%) como terceiro componente, resolvendo a faixa CRÍTICA vazia do modelo original.

---

## Autores

**Fabiana Ferreira do Nascimento** — validação empírica, cruzamento COBRADE/SEDEC e score ampliado

**Francisco Carlos Molina Duarte Júnior** — pipeline de priorização, integração IVS + CadÚnico e governança LGPD

MBA em Ciência de Dados e IA Aplicadas — ENAP, turma 2025

---

## Referências

- BRASIL. Lei nº 12.608/2012 — Política Nacional de Proteção e Defesa Civil. Disponível em: [planalto.gov.br](https://www.planalto.gov.br/ccivil_03/_ato2011-2014/2012/lei/l12608.htm)
- IPEA. Atlas de Vulnerabilidade Social. Disponível em: [ivs.ipea.gov.br](https://ivs.ipea.gov.br)
- MIDR/SEDEC. Sistema Integrado de Informações sobre Desastres (S2iD). Disponível em: [s2id.mi.gov.br](https://s2id.mi.gov.br)
