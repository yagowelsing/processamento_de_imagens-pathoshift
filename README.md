# 🔬 PathoShift

**Triagem robusta em patologia digital**: um pipeline de processamento e análise de imagens histopatológicas (H&E) que classifica tiles, agrega evidências por lâmina e mede o quanto o sistema aguenta mudanças de domínio, coloração e qualidade de imagem.

Trabalho da disciplina **Processamento de Imagens (2026/2), DCC/UFRRJ**.

> ⚠️ **Não é ferramenta médica.** Todas as predições são resultados experimentais em dados públicos e anonimizados. Nada aqui serve para diagnóstico, laudo ou qualquer uso clínico.

## 🎯 Objetivo

Imagens de patologia variam muito de um laboratório para outro: coloração, scanner, foco, compressão, fundo. Um modelo que vai bem numa partição aleatória pode desabar em dados de outra origem. O PathoShift investiga, de forma reproduzível, como as escolhas de **pré-processamento, representação, classificação, agregação e validação** afetam a robustez do sistema.

Para cada imagem, o sistema produz:

- a **classe predita**;
- uma **medida de confiança** (calibrada);
- **evidências visuais** (mapas/regiões) para inspecionar a decisão.

## 🗺️ Entregas

| # | Etapa | O que entrega | Peso | Status |
|---|-------|---------------|------|--------|
| 1 | **Pathology Image Pipeline** | Leitura do dataset, manifesto, análise exploratória, pré-processamento, baseline, métricas | 25% | 🚧 Em andamento |
| 2 | **Slide-aware Analysis** | Seleção/agregação de tiles, predição por lâmina, mapas de evidência | 30% | ⏳ A fazer |
| 3 | **Robust Pathology System** | Hipótese de pesquisa, comparação controlada, ablação, relatório e defesa | 45% | ⏳ A fazer |

## ✅ Checklist do trabalho

### Entrega 1 — Pathology Image Pipeline

- [ ] Ler e validar o conjunto de imagens
- [ ] Gerar o manifesto reprodutível do dataset
- [ ] Análise exploratória: distribuição das classes
- [ ] Análise exploratória: dimensões e resolução das imagens
- [ ] Análise exploratória: distribuição de cor
- [ ] Análise exploratória: exemplos de qualidade e artefatos (desfoque, fundo, ruído)
- [ ] Implementar pelo menos um pré-processamento (ex.: rejeição de fundo, foco, RGB→HSV/LAB, normalização de cor, filtragem)
- [ ] Justificar experimentalmente o pré-processamento escolhido
- [ ] Treinar pelo menos um baseline de classificação nas partições fornecidas
- [ ] Avaliar com métricas por classe e macro
- [ ] Gerar a matriz de confusão
- [ ] Mostrar exemplos de erros do modelo
- [ ] Registrar configuração, seed, versões das bibliotecas e custo de execução

### Entrega 2 — Slide-aware Analysis

- [ ] Definir a estratégia de seleção, ordenação ou agregação de tiles
- [ ] Fazer a predição no nível pedido (tile, região ou lâmina)
- [ ] Criar visualização ligando a predição às regiões de evidência
- [ ] Comparar com um baseline que não usa a estratégia proposta
- [ ] Analisar erros de decisão local (tile)
- [ ] Analisar erros de agregação global (lâmina)

### Entrega 3 — Robust Pathology System

- [ ] Escolher o eixo de pesquisa (domínio, normalização vs. aumentação, poucos rótulos, qualidade de imagem, calibração, explicabilidade ou eficiência)
- [ ] Formular uma hipótese objetiva e mensurável
- [ ] Submeter o escopo para aprovação da disciplina
- [ ] Fazer a comparação controlada entre baseline e proposta
- [ ] Fazer o estudo de ablação
- [ ] Analisar erros e limitações
- [ ] Discutir vieses e limites de generalização
- [ ] Escrever o relatório final (no modelo de artigo da disciplina)
- [ ] Preparar a defesa técnica individual

### Ao longo do trabalho

- [ ] Garantir que não há vazamento entre treino, validação e teste (split por paciente/lâmina)
- [ ] Nunca usar o conjunto de teste para escolher hiperparâmetros
- [ ] Garantir que o sistema roda sem internet
- [ ] Adequar ao formato de submissão quando for publicado
- [ ] Manter este README atualizado com os resultados de cada entrega

## 🧪 Pipeline

```
imagens (tiles H&E)
   │
   ├─▶ 1. leitura + validação ──▶ manifesto do dataset
   ├─▶ 2. controle de qualidade ─▶ rejeição de fundo, foco (variância do Laplaciano)
   ├─▶ 3. pré-processamento ────▶ RGB → HSV/LAB, normalização de cor (Reinhard / Macenko)
   ├─▶ 4. extração de features ─▶ histogramas de cor, textura (GLCM, LBP), filtros
   ├─▶ 5. classificação ────────▶ baseline clássico (SVM / Random Forest) e/ou CNN
   ├─▶ 6. agregação por lâmina ─▶ votação, top-k tiles, múltiplas instâncias
   └─▶ 7. avaliação ────────────▶ métricas por classe + macro, matriz de confusão, calibração
```

## 📁 Estrutura do projeto

```
processamento_de_imagens-pathoshift/
├── pathoshift/            # código-fonte
│   ├── data/              # leitura do dataset e geração de manifesto
│   ├── quality/           # rejeição de fundo, detecção de desfoque/artefatos
│   ├── preprocessing/     # conversões de cor, normalização, filtragem
│   ├── features/          # cor, textura (GLCM, LBP), descritores
│   ├── models/            # baselines e modelos propostos
│   ├── aggregation/       # agregação de tiles por lâmina
│   └── evaluation/        # métricas, calibração, matriz de confusão
├── configs/               # configuração de cada experimento (com seed)
├── notebooks/             # análise exploratória
├── experiments/           # resultados, logs e métricas de cada execução
├── reports/               # relatórios de cada entrega + artigo final
└── README.md
```

## 🛠️ Tecnologias

Python, NumPy, OpenCV, scikit-image, scikit-learn, Matplotlib e, se necessário, PyTorch.

## 📊 Métricas e reprodutibilidade

- **Qualidade preditiva:** precisão, recall e F1 por classe e macro, balanced accuracy e matriz de confusão (acurácia sozinha não basta, porque as classes podem ser desbalanceadas).
- **Confiança:** calibração (ECE), curvas de confiabilidade e abstenção.
- **Custo:** tempo de execução e memória.
- **Reprodutibilidade:** cada experimento registra configuração, seed e versões das bibliotecas.
- **Sem vazamento:** imagens do mesmo paciente ou lâmina nunca aparecem em treino e teste ao mesmo tempo.

## 📚 Dataset

São imagens microscópicas de tecido corado com H&E (hematoxilina e eosina), públicas e anonimizadas, fornecidas pela disciplina. Podem vir como tiles (recortes) individuais ou agrupadas por lâmina. As imagens não ficam neste repositório.

## 👥 Grupo

- Beatriz Fernandes Gama
- Yago Welsing Rodrigues Pereira

## 📄 License

MIT License — pode usar à vontade. Só não leve o resultado pro seu médico: nosso modelo estudou patologia por um semestre, ele não fez residência...
